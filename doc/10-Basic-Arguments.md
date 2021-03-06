Basic Arguments
==============

To use the module a bunch of arguments are available to entirely configure the Icinga 2 Agent, 
ensuring it will correctly communicate with the Icinga 2 instances it should connect to and to 
create the required certificates for a secure communication.

## -AgentName
This is in general the name of your Windows host. It will have to match with your Icinga configuration, 
as it is part of the Icinga 2 Ticket and Certificate handling to ensure a valid certificate is generated

## -Ticket
The Ticket you will receive from your Icinga 2 CA. In combination with the Icinga Director, it will 
tell you which Ticket you will require for your host

## -InstallAgentVersion
You can either leave this parameter or add it to allow the module to install or update the Icinga 2 
Agent on your system. It has to be a string value and look like this for example:
```powershell
    -InstallAgentVersion '2.6.3'
```

### -FetchAgentName
Instead of setting the Agent Name with -AgentName, the PowerShell module is capable of retreiving 
the information automaticly from Windows. Please note this is **not** the FQDN. In case this argument 
is set, **-AgentName is ignored**.

Default value: **$FALSE**

## -FetchAgentFQDN
Like -FetchAgentName, this argument will ensure the hostname is set inside the script, will however 
include the domain to provide the FQDN internally. In case this argument is set, **-AgentName and 
-FetchAgentName are ignored**.

Default value: **$FALSE**

## -TransformHostname
Allows to transform the hostname to either lower or upper case if required.
0: Do nothing
1: To lower case
2: To upper case

Defalut value: **0**

## -AgentListenPort

This variable allows to specify on which port the Icinga 2 Agent will listen on

Default value: **5665**

## -ParentZone
Each Icinga 2 Agent is in general forwarding it's check results to a parent master or satellite zone. 
Here you will have to specify the name of the parent zone

## -AcceptConfig
Icinga 2 internals to make it configurable if the Agent is accepting configuration from the Icinga 
config master.

Default value: **$TRUE**

## -IcingaEnableDebugLog

This argument will define if the Icinga 2 debug log will be enabled or disabled.

Default value: **$FALSE**

## -AgentAddFirewallRule

Allows to specify if the PowerShell Module will add a firewall rule, allowing Icinga 2 masters or
Satellites to connect to the Icinga 2 Agent on the defined port

Default value: **$FALSE**

## -ParentEndpoints

This parameter requires an array of string values, to which endpoints the Agent should in general 
connect to. If you are only having one endpoint, only add one. You will have to specify all endpoints 
the Agent requires to connect to. Examples:
```powershell
    -ParentEndpoints 'icinga2a'
    -ParentEndpoints 'icinga2a', 'icinga2b'
    -ParentEndpoints 'icinga2a', 'icinga2b', 'icinga2c'
```

## -EndpointsConfig

While -ParentEndpoints will define the name of endpoints by an array, this parameter will allow to 
assign IP address and port configuration, allowing the Icinga 2 Agent to directly connect to parent 
Icinga 2 instances. To specify IP address and port, you will have to seperate these entries by using 
';' without blank spaces. The order of the config has to match the assignment of -ParentEndpoints. 
You can specify the IP address only without a port definition by just leaving the last part.
If you wish to not specify a config for a specific endpoint, simply add an empty string to the correct 
location.

**Examples:**
```powershell
    -ParentEndpoints 'icinga2a' -EndpointsConfig 'icinga2a.localhost;5665'
    -ParentEndpoints 'icinga2a', 'icinga2b' -EndpointsConfig 'icinga2a.localhost;5665', 'icinga2b.localhost'
    -ParentEndpoints 'icinga2a', 'icinga2b', 'icinga2c' -EndpointsConfig 'icinga2a.localhost;5665', 'icinga2b.localhost', 'icinga2c.localhost;5665'
    -ParentEndpoints 'icinga2a', 'icinga2b', 'icinga2c' -EndpointsConfig 'icinga2a.localhost;5665', '', 'icinga2c.localhost;5665'
```

## -IcingaServiceUser

This argument will allow to override the user the Icinga 2 service is running with. Windows provides
some basic users already which can be configured:

* LocalSystem (Icinga default)
* NT AUTHORITY\NetworkService
* NT AUTHORITY\LocalService

If you require an own user, you can add that one as well for the argument. If a password is required
for the user to login, seperate username and password with a ':'.

Example: jdoe:mysecretpassword

Furthermore you can also use domains in combination and pass them over.

Example: icinga\jdoe:mysecretpassword

## -DownloadUrl

With this parameter you can define a download Url or local directory from which the module will 
download/install a specific Icinga 2 Agent MSI Installer package. Please ensure to only define the 
base download Url / Directory, as the Module will generate the MSI file name based on your operating 
system architecture and the version to install. The Icinga 2 MSI Installer name is internally build 
as follows: *Icinga2-v[InstallAgentVersion]-[OSArchitecture].msi*

Full example: Icinga2-v2.6.3-x86_64.msi

Default value: **https://packages.icinga.com/windows/**

## -AllowUpdates

In case the Icinga 2 Agent is already installed on the system, this parameter will allow you to 
configure if you wish to upgrade / downgrade to a specified version with the *-InstallAgentVersion* 
parameter as well. If none of both parameters is defined, the module will not update or downgrade the 
agent.

Default value: **$FALSE**

## -InstallerHashes

To ensure downloaded packages are build by the Icinga Team and not compromised by third parties, you 
will be able to provide an array of SHA1 hashes here. In case you have defined any hashses, the module 
will not continue with updating / installing the Agent in case the SHA1 hash of the downloaded MSI 
package is not matching one of the provided hashes of this parameter.

**Example:**
```powershell
    -InstallerHashes 'hash1'
    -InstallerHashes 'hash1', 'hash2'
    -InstallerHashes 'hash1', 'hash2', 'hash3'
```    

## -FlushApiDirectory

In case the Icinga Agent will accept configuration from the parent Icinga 2 system, it will possibly 
write data to /var/lib/icinga2/api/* By setting this parameter to *$TRUE*, all content inside the api 
directory will be flushed once a change is detected by the module which requires a restart of the 
Icinga 2 Agent

Default value: **$FALSE**

## -CAServer

Here you can provide a string to the Icinga 2 CA or any other CA responsible to generate the required 
certificates for the SSL communication between the Icinga 2 Agent and it's parent

## -CAPort

Here you can specify a custom port in case your CA Server is not listening on 5665

Default value: **5665**

## -ForceCertificateGeneration

The module will generate the certificates in general only if one of the required files is missing. By 
setting this parameter to $TRUE, the module will force the re-creation of the certificates.

Default value: **$FALSE**

## -CAFingerprint

This option will allow the validation of the trusted-master.crt generated during certificate generation, 
to ensure we are connected to the correct endpoint to prevent possible man-in-the-middle attacks.

## -FullUninstallation

This argument is only used by the function 'uninstallIcinga2Agent' and will remove the remaining content 
from 'C:\Program Data\icinga2' to prepare a clean setup of the Icinga 2 infrastrucure.

Default value: **$FALSE**

## -DebugMode

This is simply for development purposes and is barely used inside the module. Might change in the future.

Default value: **$FALSE**