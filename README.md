# ee-ansible-ssa

ansible-builder configuration for building an Execution Environment for the [Ansible SSA](https://www.ansible-labs.de) project.

**NOTE:** Do not make this project public at the moment! Reach out to Christian Jung, if you have any question related to the visibility of this repository.

## Code only

This project contains the execution environment definition file, but does not provide the actual container image. This is due to the requirement of having an active Red Hat Ansible Automation Platform subscription to be able to download some of the Collections used and the base [ee-supported-rhel8](https://catalog.redhat.com/software/containers/ansible-automation-platform-20-early-access/ee-supported-rhel8/60e4bc63c1af85c3015b8588) container image.

## Requirements

To build the container image [ansible-builder](https://github.com/ansible/ansible-builder) has to be installed. It can be installed with `pip install ansible-builder` or retrieved from Red Hat and [installed via the Software Channel](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html-single/ansible_builder_guide/index#proc-installing-builder).

If you want to use your own instance of private automation hub, use the provided [requirements-galaxy.yml](./requirements-galaxy.yml) to synchronize the required collections.

## Collections

To successfully build this execution environment a number of collections have to be available on automation hub. Sync Ansible Galaxy content with the provided `requirements-galaxy.yml` file (import it in the Repository Management dialog of the automation hub UI).

Also synchronize the following certified content from Red Hat Automation Hub. Log into [console.redhat.com](console.redhat.com) and enable the sync for the following collections:

- name: amazon.aws
- name: ansible.controller
- name: ansible.posix
- name: ansible.windows
- name: azure.azcollection
- name: chocolatey.chocolatey
- name: google.cloud

Check the [requirements.yml](./ee-ansible-ssa/requirements.yml) for specific versions and additional details.

## Build image

```bash
# log into the Red Hat registry to be able to pull the ee-supported-rhel8
podman login registry.redhat.io
# pull latest version of the image
podman pull registry.redhat.io/ansible-automation-platform-21/ee-supported-rhel8:latest
# make sure helper images are up to date
podman pull quay.io/ansible/ansible-builder
cd ee-ansible-ssa
# set tag to the proper version, e.g
tag=0.4.11
ansible-builder build -f ee-ansible-ssa.yml -t ee-ansible-ssa:$tag
# you might want to add -v 3 to get more details
ansible-builder build -f ee-ansible-ssa.yml -t ee-ansible-ssa:$tag -v 3
```

## Push the image

```bash
podman login quay.io
podman push ee-ansible-ssa:$tag quay.io/redhat_emp1/ee-ansible-ssa:$tag
podman push ee-ansible-ssa:$tag quay.io/redhat_emp1/ee-ansible-ssa:latest
```

## Ansible Navigator

The classic `ansible-playbook` command does not provide support for execution environments. Install [ansible-navigator](https://github.com/ansible/ansible-navigator) manually or from the [Red Hat Software Channel](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html-single/ansible_navigator_creator_guide/index#assembly-installing_on_rhel_ansible-navigator).

The `--eei` command line option allows you to specify which execution environment should be used when running an Ansible Playbook. For convenience it is recommended to add the setting to your personal `~/.ansible-navigator.yml` configuration file.

```yaml
---
ansible-navigator:
  execution-environment:
    pull-policy: missing
    image: quay.io/redhat_emp1/ee-ansible-ssa/ee-ansible-ssa
```

**NOTE:** If `pull-policy` is set to `missing`, updated container images are not automatically downloaded. Either set the policy to `always` or manually check to make sure you use the latest version. You can manually fetch the latest image by running:

```bash
podman pull quay.io/redhat_emp1/ee-ansible-ssa/ee-ansible-ssa
```

## Example on how to run the Playbook

It is recommended to keep all your cloud credentials in an variable file, so it's easier to reuse.

```bash
# source environment variables
$ . ~/path/to//cloud-env/all
# cat example
$ cat ~/path/to//cloud-env/all
export AWS_ACCESS_KEY_ID=''
export AWS_SECRET_ACCESS_KEY=''
export AZURE_SECRET=
export AZURE_CLIENT_ID=
export AZURE_SUBSCRIPTION_ID=
export AZURE_TENANT=
export GCP_AUTH_KIND=serviceaccount
export GCP_SERVICE_ACCOUNT_FILE=~/.gcp.json
export GCP_PROJECT=
export CONTROLLER_HOST=
export CONTROLLER_USERNAME=admin
export CONTROLLER_PASSWORD=
export CONTROLLER_VERIFY_SSL=false
$ ansible-navigator run controller-setup.yml -e @/path/to/variables/main.yml -e @/path/to/variables/vault.yml --vault-password-file /path/to/.vault
```
