# ee-ansible-ssa

ansible-builder configuration for building an Execution Environment for the [Ansible SSA](https://www.ansible-labs.de) project.

**NOTE:** Do not make this project public at the moment! Reach out to Christian Jung, if you have any question related to the visibility of this repository.

## Code only

This project contains the execution environment definition file, but does not provide the actual container image. This is due to the requirement of having an active Red Hat Ansible Automation Platform subscription to be able to download some of the Collections used and the base [ee-supported-rhel8](https://catalog.redhat.com/software/containers/ansible-automation-platform-20-early-access/ee-supported-rhel8/60e4bc63c1af85c3015b8588) container image.

## Requirements

To build the container image [ansible-builder](https://github.com/ansible/ansible-builder) has to be installed. It can also be retrieved from Red Hat and [installed via the Software Channel](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html-single/ansible_builder_guide/index#proc-installing-builder).

## Build image

```bash
# log into the Red Hat registry to be able to pull the ee-supported-rhel8
podman login registry.redhat.io
cd ee-ansible-ssa
# set tag to the proper version, e.g
tag=0.1.2
ansible-builder build -f ee-ansible-ssa.yml -t ee-ansible-ssa:$tag
# you might want to add -v 3 to get more details
ansible-builder build -f ee-ansible-ssa.yml -t ee-ansible-ssa:$tag -v 3
```

## Push the image

```bash
podman login registry.gitlab.com
podman push ee-ansible-ssa:$tag registry.gitlab.com/redhat-cop/ansible-ssa/ee-ansible-ssa/ee-ansible-ssa:$tag
podman push ee-ansible-ssa:$tag registry.gitlab.com/redhat-cop/ansible-ssa/ee-ansible-ssa/ee-ansible-ssa:latest
```

## Ansible Navigator

The classic `ansible-playbook` command does not provide support for execution environments. Install [ansible-navigator](https://github.com/ansible/ansible-navigator) manually or from the [Red Hat Software Channel](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html-single/ansible_navigator_creator_guide/index#assembly-installing_on_rhel_ansible-navigator).

The `--eei` command line option allows you to specify which execution environment should be used when running an Ansible Playbook. For convenience it is recommended to add the setting to your personal `~/.ansible-navigator.yml` configuration file.

```yaml
---
ansible-navigator:
  execution-environment:
    pull-policy: missing
    image: registry.gitlab.com/redhat-cop/ansible-ssa/ee-ansible-ssa/ee-ansible-ssa
```

**NOTE:** If `pull-policy` is set to `missing`, updated container images are not automatically downloaded. Either set the policy to `always` or manually check to make sure you use the latest version. You can manually fetch the latest image by running:

```bash
podman pull registry.gitlab.com/redhat-cop/ansible-ssa/ee-ansible-ssa/ee-ansible-ssa
```
