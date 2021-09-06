# ee-ansible-ssa

ansible-builder configuration for building an Execution Environment for the [Ansible SSA](https://www.ansible-labs.de) project.

## Code only

This project contains the execution environment definition file, but does not provide the actual container image. This is due to the requirement of having an active Red Hat Ansible Automation Platform subscription to be able to download some of the Collections used and the base [ee-supported-rhel8](https://catalog.redhat.com/software/containers/ansible-automation-platform-20-early-access/ee-supported-rhel8/60e4bc63c1af85c3015b8588) container image.

## Requirements

To build the container image [ansible-builder](https://github.com/ansible/ansible-builder) has to be installed. It can also be retrieved from Red Hat and [installed via the Software Channel](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html-single/ansible_builder_guide/index#proc-installing-builder).

## Build image

```bash
# log into the Red Hat registry to be able to pull the ee-supported-rhel8
podman login registry.redhat.io
cd ee-ansible-ssa
# -v 3 is optional and will increase the output details
ansible-builder build -f ee-ansible-ssa.yml -v 3
```

## Ansible Navigator

The classic `ansible-playbook` command does not provide support for execution environments. Install [ansible-navigator](https://github.com/ansible/ansible-navigator) manually or from the [Red Hat Software Channel](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.0-ea/html-single/ansible_navigator_creator_guide/index#assembly-installing_on_rhel_ansible-navigator).

The `--eei` command line option allows you to specify which execution environment should be used when running an Ansible Playbook. For convenience it is recommended to add the setting to your personal `~/.ansible-navigator.yml` configuration file.

```yaml
---
ansible-navigator:
  execution-environment:
    pull-policy: missing
    image: <name-of-execution-environment>
```
