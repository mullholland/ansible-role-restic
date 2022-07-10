# [restic](#restic)

|GitHub|GitLab|
|------|------|
|[![github](https://github.com/mullholland/ansible-role-restic/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-restic/actions)|[![gitlab](https://gitlab.com/mullholland/ansible-role-restic/badges/master/pipeline.svg)](https://gitlab.com/mullholland/ansible-role-restic)|[![quality](https://img.shields.io/ansible/quality/unset)](https://galaxy.ansible.com/mullholland/restic)|

description

## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
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


## [Example Playbook](#example-playbook)

This example is taken from `molecule/default/converge.yml` and is tested on each push, pull request and release.
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

The machine needs to be prepared in CI this is done using `molecule/default/prepare.yml`:
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





## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

-   [debian9](https://hub.docker.com/r/mullholland/docker-molecule-debian9)
-   [debian10](https://hub.docker.com/r/mullholland/docker-molecule-debian10)
-   [debian11](https://hub.docker.com/r/mullholland/docker-molecule-debian11)
-   [ubuntu1804](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu1804)
-   [ubuntu2004](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu2004)
-   [ubuntu2204](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu2204)
-   [centos7](https://hub.docker.com/r/mullholland/docker-molecule-centos7)
-   [centos-stream8](https://hub.docker.com/r/mullholland/docker-molecule-centos-stream8)
-   [centos-stream9](https://hub.docker.com/r/mullholland/docker-molecule-centos-stream9)
-   [ubi8](https://hub.docker.com/r/mullholland/docker-molecule-ubi8)
-   [fedora35](https://hub.docker.com/r/mullholland/docker-molecule-fedora35)
-   [fedora36](https://hub.docker.com/r/mullholland/docker-molecule-fedora36)
-   [amazonlinux](https://hub.docker.com/r/mullholland/docker-molecule-amazonlinux)

The minimum version of Ansible required is 2.10, tests have been done to:

-   The previous versions.
-   The current version.
-   The [devel](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-devel-from-github-with-pip) version.

This Role has the following additional molecule test scenarios:
-   git

Details can be found in ```molecule/```




If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-restic/issues)

## [License](#license)

MIT


## [Author Information](#author-information)

[Mullholland](https://github.com/mullholland)

## [Special Thanks](#special-thanks)

Template inspired by [Robert de Bock](https://github.com/robertdebock)
