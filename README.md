[![Actions Status - Master](https://github.com/juju4/ansible-pandora/workflows/AnsibleCI/badge.svg)](https://github.com/juju4/ansible-pandora/actions?query=branch%3Amaster)
[![Actions Status - Devel](https://github.com/juju4/ansible-pandora/workflows/AnsibleCI/badge.svg?branch=devel)](https://github.com/juju4/ansible-pandora/actions?query=branch%3Adevel)

# pandora ansible role

Setup pandora server, an analysis framework to discover if a file is suspicious and conveniently show the results.
* https://github.com/pandora-analysis/pandora
* https://pandora.circl.lu/submit

## Requirements & Dependencies

### Ansible
It was tested on the following versions:
 * 2.12

### Operating systems

Tested on Ubuntu 20.04, 22.04.

## Example Playbook

Just include this role in your list.
For example

```
- host: myhost
  roles:
    - juju4.pandora
```

See also docs folder for example playbooks for Azure and Digital Ocean

## Variables

TBD

## Continuous integration

```
$ pip install molecule docker
$ molecule test
$ MOLECULE_DISTRO=ubuntu:20.04 molecule test --destroy=never
```

## Troubleshooting & Known issues

* No identified logs files.
* Analyzed files and previews are stored in `/var/_pandora/pandora/tasks/`.
* Extensions considered as malicious by default are defined in pandora/workers/blocklists.py. This is independent of clean return by hashlookup. (example: dropzone.min.js)

* pandora not starting after install, automatically or manually
```
_pandora@test:~/pandora$ /usr/local/share/poetry/bin/poetry run update --yes
* Update repository.


Fetching submodule yara_repos/signature-base
You are not currently on a branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.

    git pull <remote> <branch>
```
partial fix
```
$ git pull --recurse-submodules origin main
```

* pandora shutting down itself just after start
Possibly related to systemd hardening or some missing components.

* `[Errno 13] Permission denied: '/home/runner/.config/pypoetry/config.toml` at pandora install.
This happens in GitHub action and only on Ubuntu 22.04
It has not been reproducible locally.

## License

BSD 2-clause
