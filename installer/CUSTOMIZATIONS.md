# Customizing The Install

## Table of contents

- [Volumes](#volumes)
  - [Special Dirs](#special-dirs)
  - [Direct method](#direct-method)
- [Custom Packages](#custom-packages)
- [Custom Files](#custom-files)

If you build your own images, there are a few customizations that you can add
to your `awx_task` docker. 

Custom volume mounts are available for both locally built as well as the dockerhub images.

To configure the installer with your customizations, create `group_vars` and/or `host_vars`
folders and populate them as you would for any other ansible project.

## Volumes

There are times when you may need the host system to share files with the docker container. These can be
configured as vars when you run the install playbook.

Volume mounting is available both with the dockerhub images as well as any locally built images.

### Special Dirs

If you need to mount `/etc/ansible/` or `/var/lib/awx/projects/`, you only need to specify the
location on the host system in a variable (`custom_ansible_dir` and `custom_projects_dir` repectively).

The following settings will:

1. Mount `/opt/ansible:/etc/ansible` inside `awx_task`
2. Mount `/opt/ansible_projects:/var/lib/awx/projects/` inside `awx_task`
3. Mount `/opt/ansible_projects:/var/lib/awx/projects/` inside `awx_web`

```yaml
# file: installer/group_vars/all
custom_ansible_dir: "/opt/ansible"
custom_projects_dir: "/opt/ansible_projects"
```

This will allow you to manage your manual projects and global ansible roles and configs from the host without
requiring connecting to the docker container's shell.

### Direct method

If you need to mount other volumes from your host into your AWX docker containers, simple create
`task_volumes[]` and/or `web_volumes[]` vars and list the volumes as `<host_dir>:<docker_dir>`.

e.g. 

```yaml
# file: installer/group_vars/all
task_volumes:
- "/opt/roles:/opt/roles"
- "/etc/ansible/ansible.cfg:/etc/ansible/ansible.cfg"
- "/opt/custom_python_libs/:/opt/lib/python2.7/site-packages"
```

## Custom Packages

> ***Note:*** *These customizations are only available when locally creating images.*

There are times when you want to some extra packages to be installed inside of the `awx_task` docker container. 
Usually, this occurs when you have custom modules that use packages that aren't installed by default.

The vars `custom_pip_packages` and `custom_yum_packages` are available for this purpose:

```yaml
# file: installer/group_vars/all
custom_pip_packages: 
- requests
- SOAPpy
- junos-eznc
- ncclient
```

## Custom Files

> ***Note:*** *These customizations are only available when locally creating images.
  If you need to include files with the dockerhub images, use volumes instead.*

If you need to bring some custom files into your `awx_task` container, you can include them with the `custom_files[]` var.

```yaml
# file: installer/group_vars/all
custom_files:
- src: pypath.pth
  dst: /usr/lib/python2.7/site-packages/pypath.pth
```

