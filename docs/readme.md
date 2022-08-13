
# Examples playbook

* [Azure](./azure-pandora.yml)
* [Digital ocean](./digitalocean-pandora.yml)

# Setup

* two admin users: myadmin (Administrator with sudo), deploy (dedicated for automation)
* tinyproxy
* pandora with redis and kvrocks
* option to disable Recent Tasks page and regularly purge files.

# End-User FAQ

* what is this about?
  Pandora is tool to discover if a document or file is suspicious and shows it in a convenient way.
  For many formats, it will also generate preview and extract observables.

* My file is marked malicious. What to do?
  Please reach to your security team for additional review. Web interface allows to do this quickly.
  You can eventually keep the file but you should not open or use it in anyway until green light has been given by security team.

* I'm sure this file is legitimate and not malicious.
  Some files are marked malicious based on the mime-type and file extension only, for example .exe and .js.
  For .exe files, it is always recommended to use your company approved trusted path to install application.
  You should reach to your security team for confirmation.

# Admin FAQ

* Setup should be segmented from other environments and considered DMZ/"dirty"

* How to get ansible credentials for Azure?
  * portal
https://docs.microsoft.com/en-us/azure/digital-twins/how-to-create-app-registration?tabs=portal
  * cli
```
# create
az ad app create --display-name TOKEN_NAME --available-to-other-tenants false --reply-urls http://localhost --native-app false --credential-description "DESCRIPTION"
az ad app credential reset --id <appid per previous outpout> --append
Connect-AzureAD
New-AzureADServicePrincipal -AccountEnabled $true -AppId "<appid per previous outpout>" -AppRoleAssignmentRequired $true -DisplayName "TOKEN_NAME" -Tags {WindowsAzureActiveDirectoryIntegratedApp}
# validate
az ad app credential list --id <id>
# rotate
az ad app list --show-mine
az ad app credential list --id <appid>
az ad app credential reset --id <appid>
# removal
az ad app list --filter "displayname eq 'TOKEN_NAME'"
az ad app delete <id>
```
https://docs.microsoft.com/en-us/cli/azure/ad/app?view=azure-cli-latest
