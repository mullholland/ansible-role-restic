# [Ansible role ansible-generator](#ansible-generator)

description

|GitHub|Downloads|Version|
|------|---------|-------|
|[![github](https://github.com/mullholland/ansible-role-ansible-generator/actions/workflows/molecule.yml/badge.svg)](https://github.com/mullholland/ansible-role-ansible-generator/actions/workflows/molecule.yml)|[![downloads](https://img.shields.io/ansible/role/d/mullholland/ansible-generator)](https://galaxy.ansible.com/mullholland/ansible-generator)|[![Version](https://img.shields.io/github/release/mullholland/ansible-role-ansible-generator.svg)](https://github.com/mullholland/ansible-role-ansible-generator/releases/)|
## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/mullholland/ansible-role-ansible-generator/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: converge
  hosts: all
  gather_facts: true
  vars:
    restic_install: "package"
    restic_env:
      - 'export RESTIC_REPOSITORY=/opt/restic-repo'
      - 'export RESTIC_PASSWORD=SuperSecure'
  roles:
    - role: "{{ lookup('env', 'MOLECULE_PROJECT_DIRECTORY') }}"
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/mullholland/ansible-role-ansible-generator/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  gather_facts: true

  roles:
    - role: mullholland.repository_epel
      when:
        - ansible_distribution in [ "RedHat", "CentOS", "Amazon", "Rocky", "AlmaLinux" ]

  tasks:
    - name: Install dependencies
      ansible.builtin.package:
        name:
          - "cronie"  # For the cron script
          - "hostname"  # For the cron script
        state: present
      when:
        - ansible_distribution in [ "RedHat", "CentOS", "Amazon", "Rocky", "AlmaLinux", "Fedora" ]

    - name: Install dependencies
      ansible.builtin.package:
        name:
          - "cron"  # To create crontab entry
          - "hostname"  # For the cron script
        state: present
      when:
        - ansible_os_family == "Debian"
```


## [Role Variables](#role-variables)

The default values for the variables are set in [`defaults/main.yml`](https://github.com/mullholland/ansible-role-ansible-generator/blob/master/defaults/main.yml):

```yaml
---
# can be:
# package => install restic via package manager
# git => downloads the release from git
# some RedHat based OS may need an extra repository for package install
# see scenario "package" under "molecule/package"
restic_install: "package"
# if downloaded from git set version number
restic_version: "0.12.1"

# Directories
restic_config_path: "/etc/restic"

# if downloaded from git set paths
restic_path: "/opt/restic"
restic_download_path: "{{ restic_path }}/tmp"
restic_bin_path: "{{ restic_path }}/bin"
restic_bin_system_path: "/usr/local/bin"

# User
restic_user: '{{ ansible_user | default("root") }}'
restic_group: '{{ ansible_user | default("root") }}'

restic_env:
  - 'RESTIC_REPOSITORY=/opt/restic-repo'
  - 'RESTIC_PASSWORD=SuperSecure'

restic_backup_options:
  - '--one-file-system'
  - '--exclude-caches'
restic_backup_folders:
  - '/etc'
  - '/root'
  - '/var/spool/cron'
restic_backup_excludes:
  - '*.swp'
  - '*.tmp'
  - '/tmp'

# configure retentions
restic_retentions:
  - "--keep-hourly 12"
  - "--keep-daily 7"
  - "--keep-weekly 4"
  - "--keep-monthly 6"
  - "--keep-yearly 2"

# define restic-backup cron
restic_cron_backup:
  minute: "5"
  hour: "*"
  day: "*"
  weekday: "*"
  month: "*"

# define restic-prune cron
restic_cron_prune:
  minute: "35"
  hour: "5"
  day: "*"
  weekday: "*"
  month: "*"

# Define pre/post commands for restic backup
restic_backup_pre_command: ""
restic_backup_post_command: ""  # Execute on success
restic_backup_post_error_command: ""  # Execute on error
# restic_post_command: "curl -fsS -m 10 --retry 5 -o /dev/null https://hc-ping.com/XXXX-XXXX-XXXX-XXXX"

# Define pre/post commands for restic prune
restic_prune_pre_command: ""
restic_prune_post_command: ""  # Execute on success
restic_prune_post_error_command: ""  # Execute on error
# restic_post_command: "curl -fsS -m 10 --retry 5 -o /dev/null https://hc-ping.com/XXXX-XXXX-XXXX-XXXX"
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/mullholland/ansible-role-ansible-generator/blob/master/requirements.txt).

## [State of used roles](#state-of-used-roles)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[mullholland.repository_epel](https://galaxy.ansible.com/mullholland/repository_epel)|[![Build Status GitHub](https://github.com/mullholland/ansible-role-repository_epel/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-repository_epel/actions)|[![Build Status GitLab](https://gitlab.com/mullholland-github-mirror/ansible-role-repository_epel/badges/master/pipeline.svg)](https://gitlab.com/mullholland-github-mirror/ansible-role-repository_epel)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://mullholland.net) for further information.

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/mullholland/enterpriselinux)|all|
|[Rocky](https://hub.docker.com/r/mullholland/rockylinux)|all|
|[AlmaLinux](https://hub.docker.com/r/mullholland/almalinux)|all|
|[Amazon](https://hub.docker.com/r/mullholland/amazonlinux)|all|
|[Fedora](https://hub.docker.com/r/mullholland/fedora/)|all|
|[Ubuntu](https://hub.docker.com/r/mullholland/ubuntu)|all|
|[Debian](https://hub.docker.com/r/mullholland/debian)|all|
|[CentOS](https://hub.docker.com/r/mullholland/centos)|all|

The minimum version of Ansible required is 2.10, tests have been done to:

- The version before the previous version.
- The previous version.
- The current version.

If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-ansible-generator/issues).

## [License](#license)

[MIT](https://github.com/mullholland/ansible-role-ansible-generator/blob/master/LICENSE).

## [Author Information](#author-information)

[Mullholland](https://mullholland.net)
