# ee-ansible-ssa

ansible-builder configuration for building Ansible Execution Environments

## Private repository

This repository is set to private since we are not allowed to share EE's based on the Red Hat EE's publicly. See for example the license terms for [ee-ansible-supported](https://catalog.redhat.com/software/containers/ansible-automation-platform-20-early-access/ee-supported-rhel8/60e4bc63c1af85c3015b8588?container-tabs=gti).

## Push images

```bash
# log into registry
podman login registry.gitlab.com
# upload the image with the proper tag
# e.g tag=0.0.1
podman push ee-ansible-ssa:$tag registry.gitlab.com/redhat-cop/ansible-ssa/ee-ansible-ssa/ee-ansible-ssa:$tagp
podman push ee-ansible-ssa:$tag registry.gitlab.com/redhat-cop/ansible-ssa/ee-ansible-ssa/ee-ansible-ssa:latest
```

## Ansible Navigator
