- The exam will come with some PS and CLI commands that you need to know how to use them, however, it's not strictly necessary to memory all of them. **There is a predictable naming system for commands**.
# Predictable CLI
- The CLI follows a name system, so for example, for the CLI all the commands start with AZ.
- After the AZ it most of the time comes with the resource and a verb that describes an action on that resource
	- Virtual Machines Examples:
		- az **vm** ==list==
		- az **vm** ==create==
		- az **vm** ==delete==
	- KeyVault Examples:
		- az **keyvault** ==list==
		- az **keyvault** ==create==
		- az **keyvault** ==delete==
	- Virtual Network Examples:
		- az **network vnet** ==list==
		- az **network vnet** ==create==
		- az **network vnet** ==delete==
- When it comes to Virtual Networks, the VNET is abbreviated because the VNET is actually a sub-category of the **network service**
	- Subnet Examples:
		- az **network vnet subnet** ==list==
		- az **network vnet subnet** ==create
		- az **network vnet subnet** ==delete==
- The subnet is more complicated because it is a sub component of VNET
# Predictable PowerShell
- With PowerShell you have the verb as the first part of the word, or command.
- After the verb you'll get a dash, Az and the action you want to perform.
	- Virtual Machines Examples:
		- ==Get==-Az**VM**
		- ==New==-Az**VM**
		- ==Remove==-Az**VM**
		- Instead of list is **Get**; instead of create is **New**; instead of delete is **Remove**
	- KeyVault Examples:
		- ==Get==-Az**KeyVault**
		- ==New==-Az**KeyVault**
		- ==Remove==-Az**KeyVault**
	- Virtual Network Examples:
		- ==Get==-Az**VirtualNetwork**
		- ==New==-Az**VirtualNetwork**
		- ==Remove==-Az**VirtualNetwork**
	- Subnet Examples
		- ==Get==-Az**VirtualNetworkSubnetConfig**
		- ==New==-Az**VirtualNetworkSubnetConfig**
		- ==Remove==-Az**VirtualNetworkSubnetConfig**
# Details about PS and CLI for Managing Azure
### PowerShell
- For the sake of this document, almost all the Windows that has PS installed, has a older version of it called **Windows PowerShell**.
- After that, Microsoft has created a **cross-platform version** of PS that used to be called **PowerShell Core**.
- Now it is called only **PowerShell** and the latest version is **PowerShell 7**.
- PS7 is cross-platform, it is available for **Windows, OS X, and for Linux**.
### CLI
- We also have a **CLI option** sometimes called Bash within Azure, which is the equivalent tool within a Linux-based system.
- Although you can get PS for Linux and the CLI for Windows. There is a CLI available that you can download, **cross-platform** for all the three major platforms.
### Modules
- It isn't enough to have the PowerShell installed in the system, you must have the necessary Azure-specific modules.
- It's possible to avoid having to install anything related to terminal in our systems by using the **Azure Cloud Shell** though.
- Microsoft moved from an old module called **AzureRM** to a module that's just called **AZ**, and you can't have both in the same version.
- ==Install-Module -Name AZ -AllowClobber -Repository PSGallery -Force==
- ==Update-Module -Name AZ -AllowClobber -Repository PSGallery==
- To start running commands for Azure, it is mandatory to log in to your Azure Account first, that's the equivalent of **aws configure** command: ==Connect-AzAccount== or ==az login== for CLI.