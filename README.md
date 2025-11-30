[![Actions Status - Main](https://github.com/juju4/ansible-pandora/workflows/AnsibleCI/badge.svg)](https://github.com/juju4/ansible-pandora/actions?query=branch%3Amain)
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

* poetry 1.2.1 seems to introduce a regression with dulwich module
```
         "",
        "  HangupException",
        "",
        "  The remote server unexpectedly closed the connection.",
        "",
        "  at /usr/local/share/poetry/venv/lib/python3.8/site-packages/dulwich/protocol.py:232 in read_pkt_line",
        "      228│ ",
        "      229│         try:",
        "      230│             sizestr = read(4)",
        "      231│             if not sizestr:",
        "    → 232│                 raise HangupException()",
        "      233│             size = int(sizestr, 16)",
        "      234│             if size == 0:",
        "      235│                 if self.report_activity:",
        "      236│                     self.report_activity(4, \"read\")",
        "",
        "The following error occurred when trying to handle this error:",
        "",
        "",
        "  HangupException",
        "",
        "  ssh: Could not resolve hostname https: Temporary failure in name resolution",
        "",
        "  at /usr/local/share/poetry/venv/lib/python3.8/site-packages/dulwich/client.py:1151 in fetch_pack",
```
https://github.com/python-poetry/poetry/issues/6329
https://github.com/python-poetry/poetry/issues/6428
https://github.com/jelmer/dulwich/issues/1032
Normally fixed in upstream dulwich 0.20.46 but still seeing occurrence. As workaround, either revert to poetry 1.1.15, either force use of system git.

* Memory can easily be an issue and result in oom-kill some process. Recommend at least 6GB of RAM.

* If web UI does not react as expected, check browser console for possible Content-Security-Policy mismatch.

## Alternatives

* [Suspicious is a powerful web application designed to help users submit and analyze emails, files, IP addresses, and URLs](https://github.com/thalesgroup-cert/suspicious)
* [Dangerzone - Take potentially dangerous PDFs, office documents, or images and convert them to safe PDFs.](https://dangerzone.rocks/)

## License

BSD 2-clause
