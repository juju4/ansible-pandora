# Security

## Architecture

* Pandora base setup is single-tier, single host via public Internet
```
┌───────────────┐
│ User          │
└──────┬────────┘
       │
┌──────▼────────┐
│ Pandora       │
└───────────────┘
```

* Pandora setup on Azure via Express Route
```
┌───────────────┐
│ User          │
└──────┬────────┘
       │
┌──────▼────────┐
│ Express Route │
└──────┬────────┘
       │
┌──────▼────────┐
│ Pandora       │
└───────────────┘
```
(https://asciiflow.com/#/)

## Infrastructure setup

* Fully automated with ansible
* Upstream ansible role with continuous integration and weekly test.

## Risks

* Confidentiality
Uploaded files can contain sensitive data (PII, PHI, Intellectual Property...)

* Integrity
Limited risk of tampering as files are analyzed for review and output is likely used for a limited time after, mostly by requesting user or security staff.
A threat actor compromising documents could make them appear not dangerous and lead a user to open a malicious file.

* Availability
Limited risk as will only impede the service itself with no dependency.
Service can also be rebuilt in less than few hours as fully automated.
SLA at 90% enough (https://sre.google/sre-book/availability-table/)

* Attack on underlying Azure infrastructure

## Mitigating factors

### Identity

* No frontend authentication
Pandora is using Flask login but no providers/connectivity seems to be in place.
Possible option to use with a reverse proxy: https://github.com/vouch/vouch-proxy

* Backend (kvrocks) has no authentication and no options is offered but is local to server.
"Apache Kvrocks (Incubating) is a distributed key value NoSQL database that uses RocksDB as storage engine and is compatible with Redis protocol." - https://kvrocks.apache.org/

### System

* Systemd hardening with seccomp, capabilities.
* hardening roles: juju4.harden, juju4.osquery, juju4.falco
* Azure
  * Microsoft.GuestConfiguration VM extension
  * Azure Backup policy set daily
  * Optional Azure Bastion
  * Optional use of existing virtual network/subnet with private IP mapped on an Express Route to avoid having public IP
  * Issue: LinuxDiagnostic v3 does not support Ubuntu 22.04 at August 2022

### Network

|From|To|protocol/port|
|----|--|-------------|
|Authorized EndUser/Jumphost|Website|tcp/443|
|Website|Web servers|tcp/80+443|
|Website|DNS server|tcp+udp/53|
|Website|SMTP server|tcp/587|

* Firewall at host level and nsg for azure for both incoming and outgoing traffic
  For azure playbook:
  * DNS hosts restricted to Azure as default
  * SMTP hosts restricted to O365 as default

* Website access can be over public Internet. If using Azure Express Route, this is limited to internal network.
* Reverse proxy (nginx) with https. Default self-signed certificate, optional letsencrypt but only if public access, else it is recommended to use internal CA.

* Web proxy for outgoing traffic (local). If larger environment, use separate host.
A allow-only web proxy is setup with less than 40 domains approved, related to system and pandora operations
```
# install
github.com
objects.githubusercontent.com
code.jquery.com
momentjs.com
download.comodo.com
install.python-poetry.org
pypi.org
files.pythonhosted.org
# usage (misp, lookiloo...)
cdn.download.comodo.com
bazaar.abuse.ch
www.hybrid-analysis.com
jbxcloud.joesecurity.org
www.virustotal.com
```

### Logging

* System level

* Cloud level
Use of Azure Diagnostics.

### Application Code scanning

Upstream project workflows include:
* codeql analysis
* mypy
* test api
https://github.com/pandora-analysis/pandora/tree/main/.github/workflows

## Improvements

* Split service under multiple systemd units allowing
  * different user
  * different level of privilege, system hardening
  * separating between different hosts aka three-tier architecture, at least redis part
* Use web proxy with TLS inspection
* App code scanning
* Document requirements for source-built redis and kvrocks vs upstream packages
