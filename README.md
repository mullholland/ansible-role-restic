# [Ansible role restic](#restic)

description

|GitHub|Downloads|Version|
|------|---------|-------|
|[![github](https://github.com/mullholland/ansible-role-restic/actions/workflows/molecule.yml/badge.svg)](https://github.com/mullholland/ansible-role-restic/actions/workflows/molecule.yml)|[![downloads](https://img.shields.io/ansible/role/d/mullholland/restic)](https://galaxy.ansible.com/mullholland/restic)|[![Version](https://img.shields.io/github/release/mullholland/ansible-role-restic.svg)](https://github.com/mullholland/ansible-role-restic/releases/)|
## [Example Playbook](#example-playbook)

This example is taken from [`molecule/default/converge.yml`](https://github.com/mullholland/ansible-role-restic/blob/master/molecule/default/converge.yml) and is tested on each push, pull request and release.

```yaml
---
- name: converge
  hosts: all
  become: true
  gather_facts: true
  vars:
    restic_install: "package"
    restic_env:
      - 'export RESTIC_REPOSITORY=/opt/restic-repo'
      - 'export RESTIC_PASSWORD=SuperSecure'
  roles:
    - role: "mullholland.restic"
```

The machine needs to be prepared. In CI this is done using [`molecule/default/prepare.yml`](https://github.com/mullholland/ansible-role-restic/blob/master/molecule/default/prepare.yml):

```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: true

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

The default values for the variables are set in [`defaults/main.yml`](https://github.com/mullholland/ansible-role-restic/blob/master/defaults/main.yml):

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

- pip packages listed in [requirements.txt](https://github.com/mullholland/ansible-role-restic/blob/master/requirements.txt).


## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://mullholland.net) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/mullholland/ansible-role-restic/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

|container|tags|
|---------|----|
|[EL](https://hub.docker.com/r/mullholland/enterpriselinux)|all|
|[Amazon](https://hub.docker.com/r/mullholland/amazonlinux)|Candidate|
|[Fedora](https://hub.docker.com/r/mullholland/fedora/)|all|
|[Ubuntu](https://hub.docker.com/r/mullholland/ubuntu)|all|
|[Debian](https://hub.docker.com/r/mullholland/debian)|all|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.

If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-restic/issues).

## [License](#license)

[MIT](https://github.com/mullholland/ansible-role-restic/blob/master/LICENSE).

## [Author Information](#author-information)

[Mullholland](https://mullholland.net)
