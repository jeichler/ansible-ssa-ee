# README

build the image:

```bash
# set tag to the proper version, e.g
# tag=0.0.1
ansible-builder build -f ee-ansible-ssa.yml -t ee-ansible-ssa:$tag
# you might want to add -v 3 to get more details
ansible-builder build -f ee-ansible-ssa.yml -t ee-ansible-ssa:$tag -v 3
```
