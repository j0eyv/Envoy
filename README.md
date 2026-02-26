# ENVOY - Lightweight User Environment Manager

This tool automates the deployment or execution of configurations during logon on Windows machines. It is mainly built for Intune Managed devices where certain actions are not natively possible from Intune. During execution it connects to Microsoft Graph using an Enterprise App and grabs the group memberships from the user. The script performs tasks such as drive mapping, printer mapping, registry key management, File Actions (copy/move/delete/rename) and starts executables based on Entra ID group membership.


 - 🌐 https://www.envoycontrol.com
 - ✉️ info@envoycontrol.com


&nbsp;

# 📺 Demo

Watch the video on Youtube

[![Watch the video on Youtube](https://i9.ytimg.com/vi/HaOsP7huuDw/mqdefault.jpg?v=68232e2a&sqp=CMDMgs0G&rs=AOn4CLCpD4WIjZRtJRM7bJ7PXmu0ZPFGdw)](https://www.youtube.com/watch?v=HaOsP7huuDw)

Watch the promo video on YouTube

[![Watch the promo video on Youtube](https://i9.ytimg.com/vi_webp/pWEkDXyE4g4/mq1.webp?sqp=CMDMgs0G-oaymwEmCMACELQB8quKqQMa8AEB-AH-CYAC0AWKAgwIABABGBMgPyh_MA8=&rs=AOn4CLAoWW6pHQXHmb5FWr0NUbaK5dRwmw)](https://www.youtube.com/watch?v=pWEkDXyE4g4)

&nbsp;

# 📑 Contents
- [ENVOY - Lightweight User Environment Manager](#envoy---lightweight-user-environment-manager)
- [📺 Demo](#-demo)
- [📑 Contents](#-contents)
- [🚀 Core Components](#-core-components)
- [🔜 Roadmap](#-roadmap)
- [🚧 Release Notes](#-release-notes)
- [🛑 Known issues](#-known-issues)
- [💥 Functions](#-functions)
    - [📰 Write-Log](#-write-log)
    - [📰 Write-AdminLog](#-write-adminlog)
    - [🔑 Tenant Configuration](#-tenant-configuration)
    - [📁 Deploy-DriveMappings](#-deploy-drivemappings)
    - [📘 Deploy-RegistryKeys](#-deploy-registrykeys)
    - [⏳ Deploy-Executables](#-deploy-executables)
    - [💾 Deploy-FileActions](#-deploy-fileactions)
    - [📠 Deploy-Printers](#-deploy-printers)
    - [➡️ Deploy-StartMenuEntry](#️-deploy-startmenuentry)
    - [➡️ Deploy-DesktopShortcut](#️-deploy-desktopshortcut)
  - [✅ Execution](#-execution)
  - [🔃 Envoy Refresh](#-envoy-refresh)
  - [⚠️ Dependencies](#️-dependencies)
- [💻 Installation](#-installation)
  - [App registration](#app-registration)
  - [Install Envoy](#install-envoy)
  - [Configure Envoy](#configure-envoy)
  - [Centralized distribution](#centralized-distribution)
    - [GitHub-Hosted Config Delivery](#github-hosted-config-delivery)
    - [Azure Blob](#azure-blob)
    - [Azure Files](#azure-files)
    - [File Share (on-prem)](#file-share-on-prem)
- [Support the project](#support-the-project)


&nbsp;

# 🚀 Core Components

- **Microsoft Graph Authentication**: The script uses the `Microsoft.Graph` modules to interact with Microsoft Graph APIs.
- **Configuration File**: Reads settings from `Config.json` located at `C:\ProgramData\Envoy\Config.json`.
- **Authentication**: Connects to Microsoft Graph using an App Registration.

&nbsp;

# 🔜 Roadmap
- Support for nested groups in Entra ID
- IconIndex for Desktop and Start-Menu shortcuts

  **Long term:**
- Investigating for a more robust and centralized logging method/dashboard
- Multiple Entra Groups per item/block  
  
  &nbsp;

# 🚧 Release Notes
- 1.1.116: Adding an option to set specific printer as the default printer
- 1.2.001: Locally stored AppID & Client Secret are now obsolete. Migrated to Delegated Permissions based on **Microsoft Graph Command Line Tools** public endpoint.
- 1.2.005: Removed a small piece of VBScript-based code. With VBScript being retired in Windows, this change makes Envoy more future-proof.
- 1.2.007: Fixed a bug where drive mappings showed a popup.
- 1.2.008: Fixed a bug where printer mappings showed a popup.
- 1.2.009: File Actions feature now supports parameters like %localappdata%
- 1.2.010: Registry settings now supports Binary values. Also improved error handling which annoyed when logging on using a local account on a device.
- 1.2.011: A new capability has been introduced to automatically create Start Menu shortcuts. Additionally, an issue that could block installation on certain non-English operating systems has been resolved.
- 1.2.013: Added support for %UPN% and %USERNAME% variables in the Drive Mapping function, enabling better handling of home drive paths.
- 1.2.014: This version includes several enhancements along with a new feature.
  
  - Fixed a problem that might cause a popup error when a logfile failed to be created
  - Fixed a problem that might lead to Update Tasks being deleted during upgrades to newer versions.
  - Introduced action methods for the start-menu entry function, allowing you to remove start-menu entries as well  
  - Enabled support for subfolders within the start-menu  
  - Implemented a new feature that lets you add and delete desktop shortcuts!

- 1.3.001: This version includes several enhancements and bug fixes. **BREAKING CHANGE!**
	 - Authentication method has been updated to utilize MgGraph differently due to the retirement of Graph CLI  
	 - Added support for using Entra ID Group ObjectID/GUID alongside the group name  
	 - Added support for the IconPath setting in Desktop Shortcuts and Start-Menu Shortcuts features  
	 - Added support for Arguments in Desktop Shortcuts and Start-Menu Shortcuts features  
	 - Added support for StartIn path in Desktop and Start-Menu shortcuts
   - Fixed an issue where File Actions could be triggered even when a user was not part of a group
  
- 1.3.002: This release addresses issues with Microsoft.Graph.Authentication found in versions 2.34.0 and 2.35.0
    - Authentication method has been modified to utilize 'Set-MgGraphOption -DisableLoginByWAM $true' if available
    - Added functionality to store Admin logs alongside the existing User logs.

- 1.3.003: Enhancements for Drive Mapping feature
  - Drive mappings now supports friendly drive labels. The **Description** field is used to define the label.  

- 1.3.004: Bug fix
  - Small bug fix to prevent authentication pop-ups in non-managed user contexts.

 - 1.3.005: Bug fix
  - Fixed an issue where File Actions could be triggered even when a user was not part of a group
  
&nbsp;

# 🛑 Known issues
- Microsoft Graph PowerShell modules (version 2.34 and 2.35) may cause Graph connection issues, resulting in the following error: _WARNING: Note: Sign in by Web Account Manager (WAM) is enabled by default on Windows. If using an embedded terminal, the interactive browser window may be hidden behind other windows._ To maintain stability within the Envoy environment, upgrading the PowerShell modules to version 2.35.1 is recommended, as this release includes the DisableLoginByWAM fix implemented in Envoy.

&nbsp;

# 💥 Functions

### 📰 Write-Log

- **Purpose:** Logs messages to a user-specific log file located at `C:\ProgramData\Envoy\Logging\<username>\User.log`.

- **Key Features:** Ensures the log directory exists and appends timestamped log entries.

### 📰 Write-AdminLog

- **Purpose:** Logs messages to a admin-specific log file located at `C:\ProgramData\Envoy\Logging\<username>\Admin.log`.

- **Key Features:** Ensures the log directory exists and appends timestamped log entries.

&nbsp;

### 🔑 Tenant Configuration

**Purpose**: Being used to configure the Entra ID connection.

**Key Features:** Used for TenantId and ApplicationId parameters.




```
"Tenant": [
      {
        "TenantId": "****-****-****-**********",
        "AppId": "****-****-****-**********"
      },
    ],
```

**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| TenantId      | ******** | Configure the desired TenantId guid.                   |
| AppId           | ******** | Configure the AppId. This is the AppRegistration which handles the authentication |

&nbsp;

### 📁 Deploy-DriveMappings

**Purpose**: Maps or removes network drives based on configuration.

**Key Features:**
  - Reads drive mapping configurations from `Config.json`.
  - Checks user group memberships to determine eligibility.
  - Supports adding and removing drive mappings.
  - Logs success or failure of each operation.
  - Priority handling for situations with conflicting actions (e.g. same drive letters).

&nbsp;

**Example:**
```
"Drives": [
      {
        "DriveLetter": "X",
        "UNCPath": "\\\\storageaccount.file.core.windows.net\\share1",
        "Group": "GG - Share 1",
        "Description": "Test Drive",
        "Priority": 1,
        "Action": "add"
      },
      {
        "DriveLetter": "Y",
        "UNCPath": "\\\\fileserver\\share2",
        "Group": "GG - Share 2",
        "Description": "Test Drive 2",
        "Priority": 1,
        "Action": "add"
      },
      {
        "DriveLetter": "H",
        "UNCPath": "\\\\fileserver\\Home\\%username%",
        "Group": "",
        "Description": "Home",
        "Priority": 1,
        "Action": "add"
      },
      {
        "DriveLetter": "Z",
        "UNCPath": "\\\\fileserver\\Home\\%upn%",
        "Group": "",
        "Description": "Home",
        "Priority": 1,
        "Action": "add"
      }
    ],
```

**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| DriveLetter      | x,y,z, etc.  | Configure the desired drive mapping letter.                   |
| UNCPath           |\\\\server.domain.local\\share  | Configure the desired UNC path. Don't forget double slashes for JSON. Supports %upn% and %username% variables. Under the hood, %username% maps to "$env:USERNAME" and %upn% maps to "whoami /upn" |
| Group | e.g. "GG - Sales Team" | Configure the desired Entra ID group name or Object ID. Users in this group will receive this drive mapping. Leave the group empty for "everyone". |
| Description | Text | Fill in a description. E.g. Sales Drive, Marketing Team. This is being used as friendlyname |
| Priority | 1,2,3,4,5,6, etc | Conflicts with drive mappings can occur if a user is a member of multiple groups with the same drive letter as result. |Prio 1 is the lowest, higher wins. |
| Action | Add or Remove | Define the action for the drive mapping |

&nbsp;

### 📘 Deploy-RegistryKeys

**Purpose:**  Adds or removes registry keys based on configuration.

**Key Features:**
  - Reads registry configurations from `Config.json`.
  - Checks user group memberships to determine eligibility.
  - Ensures registry keys exist before setting values.
  - Supports adding and removing registry keys.
  - Logs success or failure of each operation.

&nbsp;

**Example:**
```
    "Registries": [
      {
        "Key": "HKCU:\\Software\\TestKey",
        "ValueName": "TestValue1",
        "ValueType": "DWORD",
        "ValueData": 1,
        "Group": "GG - Registry - TestKey",
        "Action": "add"
      },
      {
        "Key": "HKCU:\\Software\\TestKey",
        "ValueName": "TestValue2",
        "ValueType": "STRING",
        "ValueData": "2",
        "Group": "",
        "Action": "add"
      },
      {
        "Key": "HKCU:\\Software\\JoeyTestKey",
        "ValueName": "BinaryValueA",
        "ValueType": "BINARY",
        "ValueData": [1,2,3,4,5,6,7,8],
        "Group": "",
        "Action": "add"
      },
      {
        "Key": "HKCU:\\Software\\JoeyTestKey",
        "ValueName": "BinaryValueB",
        "ValueType": "BINARY",
        "ValueData": "0A,0B,0C,0D,0E,0F",
        "Group": "",
        "Action": "add"
      }
    ],
```

**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| Key | HKCU:\\Path\\ | Fill in the desired HKCU Path |
| ValueName | Text | Fill in the desired value name |
| ValueType | DWORD, STRING, BINARY, etc | Define the desired type |
| ValueData | Data | Define the desired data. Decimal, text, path's, etc. |
| Group | e.g. "GG - Sales Team" | Configure the desired Entra ID group name or Object ID. Users in this group will receive this registry setting. Leave the group empty for "everyone". |
| Action | Add or Remove | Define the action for the registry key |

&nbsp;

### ⏳ Deploy-Executables

**Purpose:** Starts executables with optional arguments based on configuration.

**Key Features:**
  - Reads executable configurations from `Config.json`.
  - Checks user group memberships to determine eligibility.
  - Supports starting executables with or without arguments.
  - Logs success or failure of each operation.

&nbsp;
**Example:**
```
    "Executables": [
      {
        "FilePath": "C:\\ProgramFiles\\CustomApp\\Setup.msi",
        "Arguments": "/qn",
        "Group": "GG - Custom App"
      },
      {
        "FilePath": "C:\\Windows\\System32\\calc.exe",
        "Arguments": "",
        "Group": "GG - Calculator"
      }
    ],
```

&nbsp;

**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| FilePath | C:\\Folder\\File.exe | Configure the file path |
| Arguments | /qn, /silent, etc | Supports arguments belonging to the application |
| Group | e.g. "GG - Sales Team" | Configure the desired Entra ID group name or Object ID. Users in this group will automatically launch the configured application. Leave the group empty for "everyone". |

&nbsp;

### 💾 Deploy-FileActions

**Purpose:** Performs file operations (copy, delete, rename, move) based on configuration.

**Key Features:**
  - Reads file action configurations from `Config.json`.
  - Checks user group memberships to determine eligibility.
  - Supports the following actions:
    - Copy: Copies files to a destination.
    - Delete: Deletes files.
    - Rename: Renames files.
    - Move: Moves files to a new location.
  - Logs success or failure of each operation.

&nbsp;

**Example:**
```
    "FileActions": [
      {
        "FileActionType": "copy",
        "SourcePath": "C:\\Temp\\Source\\File.txt",
        "DestinationPath": "C:\\Temp\\Destination\\File.txt",
        "NewName": "NewFileName.txt",
        "Group": "GG - FileAction - Copy"
      },
      {
        "FileActionType": "copy",
        "SourcePath": "%localappdata%\\File.txt",
        "DestinationPath": "C:\\Temp\\Destination\\File.txt",
        "NewName": "NewFileName.txt",
        "Group": "Envoy - FileAction - EID - Test"
      },  
      {
        "FileActionType": "rename",
        "SourcePath": "C:\\Temp\\Source\\File.txt",
        "DestinationPath": "C:\\Temp\\Destination\\File.txt",
        "NewName": "NewFileName.txt",
        "Group": "GG - FileAction - Rename"
      },
      {
        "FileActionType": "move",
        "SourcePath": "C:\\Temp\\Source\\File.txt",
        "DestinationPath": "C:\\Temp\\Destination\\File.txt",
        "NewName": "NewFileName.txt",
        "Group": "GG - FileAction - Move"
      },
      {
        "FileActionType": "delete",
        "SourcePath": "C:\\Temp\\Source\\File.txt",
        "DestinationPath": "C:\\Temp\\Destination\\File.txt",
        "NewName": "NewFileName.txt",
        "Group": "GG - FileAction - Delete"
      }
    ],
```

**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| FileActionType | copy, rename, move, delete | Configure the file action |
| SourcePath | C:\\Folder\\Source\\File.ini | Being used for actions: copy, rename, move, delete. Supports parameters like %localappdata% |
| DestinationPath | C:\\Folder\\Destination\\File.ini | Being used for actions: copy, move |
| NewName | FileName | Set the desired new file name. Being used for actions: rename |
| Group | e.g. "GG - Sales Team" | Configure the desired Entra ID group name or Object ID. Users in this group will automatically execute the file actions. Leave the group empty for "everyone". |

&nbsp;

### 📠 Deploy-Printers

**Purpose:** Adds or removes printers based on configuration.

**Key Features:**
  - Reads printer configurations from `Config.json`.
  - Checks user group memberships to determine eligibility.
  - Supports adding and removing printers.
  - Logs success or failure of each operation.

&nbsp;
**Example:**
```
    "Printers": [
      {
        "PrinterPath": "\\\\PRINTSRV.domain.local\\FollowMe",
        "Group": "GG - Printers - FollowMe",
        "Action": "add",
        "DefaultPrinter": "True"
      },
      {
        "PrinterPath": "\\\\PRINTSRV.domain.local\\PRT01",
        "Group": "",
        "Action": "remove"
      }
    ]
```
**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| PrinterPath | \\\\server\\printer | Configure the UNC path for the desired printer |
| Group | e.g. "GG - Sales Team" | Configure the desired Entra ID group name or Object ID. Users in this group will automatically add or remove the printer queue. Leave the group empty for "everyone". |
| Action | Add or Remove | Define if the printer queue should be added or removed |
| DefaultPrinter | True | Sets the specified printer queue as the default printer in Windows. If this value is empty or set to False, no action is taken. If multiple printer queues are marked as default in the configuration, the last one listed will be applied as the default printer. |

&nbsp;

### ➡️ Deploy-StartMenuEntry

**Purpose:** Add's or removes Start-Menu shortcuts based on group membership.

**Key Features:**
  - Reads shortcut configurations from `Config.json`.
  - Checks user group memberships to determine eligibility.
  - Supports adding and removing Start-Menu shortcuts.
  - Supports subfolders within the Start-Menu. It requires at least 2 shortcuts per subfolder to actually show up. This is how Windows handles the start-menu.
  - Logs success or failure of each operation.

&nbsp;
**Example:**
```
    "StartMenuEntry": [
      {
        "shortcutName": "Notepad++",
        "executable": "C:\\App1\Launch.exe",
        "arguments": "-file \"C:\\App1\\Config.json\"",
        "StartIn": "C:\\App1\"
        "shortcutFolder": "Utilities",
        "action": "Add",
        "iconPath": "c:\\App1\\logo.ico",
        "Group": ""
      } 
    ] 
```
**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| Shortcutname | Application Name | Defines the displayname the shortcut presents to the user |
| Group | e.g. "GG - Sales Team" | Configure the desired Entra ID group name or Object ID. Users in this group will automatically receive this start-menu shortcut. Leave the group empty for "everyone". |
| Shortcutfolder | e.g. "Utilities" | Supports subfolders within the Start-Menu. It requires at least 2 shortcuts per subfolder to actually show up. This is how Windows handles the start-menu. |
| Action | Add or Remove | Define if the shortcut should be added or removed |
| IconPath | C:\\App\\Logo.ico | Configure the desired path where the icon file is located |
| StartIn | C:\\App\\ | Configure the working directory for the program when it launches. |
| Executable | C:\\App\\Start.exe | Shortcut target |
| Arguments | -file \"C:\\App1\\Config.json\" |	Optional command-line arguments passed to the executable |

&nbsp;

### ➡️ Deploy-DesktopShortcut

**Purpose:** Add's or removes desktop shortcuts based on group membership.

**Key Features:**
  - Reads shortcut configurations from `Config.json`.
  - Checks user group memberships to determine eligibility.
  - Supports adding and removing desktop shortcuts.
  - Logs success or failure of each operation.

&nbsp;
**Example:**
```
    "StartMenuEntry": [
      {
        "shortcutName": "Command Prompt",
        "executable": "C:\\Windows\\System32\\cmd.exe",
        "arguments": "",
        "action": "remove",
        "iconPath": "c:\\application\\logo.ico",
        "StartIn": "c:\\application\\,
        "Group": ""
      },
      {
        "shortcutName": "Remote Assistance",
        "executable": "C:\\Windows\\System32\\msra.exe",
        "arguments": "-x \"\\\\server\\share\connect.rdp"",
        "action": "add",
        "iconPath": "c:\\application\\logo.ico",
        "StartIn": "C:\\Windows\\System32\\,
        "Group": ""
      }       
    ] 
```
**Usage:**
| Setting           | Values      | Description  |
|------------------|-----|-----------------------|
| Shortcutname | Application Name | Defines the displayname the shortcut presents to the user |
| Group | e.g. "GG - Sales Team" | Configure the desired Entra ID group name or Object ID. Users in this group will automatically receive this start-menu shortcut. Leave the group empty for "everyone". |
| Action | Add or Remove | Define if the shortcut should be added or removed |
| IconPath | C:\\App\\Logo.ico | Configure the desired path where the icon file is located |
| StartIn | C:\\App\\ | Configure the working directory for the program when it launches. |
| Executable | C:\\App\\Start.exe | Shortcut target |
| Arguments | -file \"C:\\App1\\Config.json\" |	Optional command-line arguments passed to the executable |

&nbsp;

## ✅ Execution

The script executes the following tasks sequentially while logging on. The scheduled task created by `Envoy.msi` will execute these functions.

1. **Drive Mapping**: Execute `Deploy-DriveMappings` to manage network drives.
2. **Registry Key Management**: Execute `Deploy-RegistryKeys` to manage registry keys.
3. **Process execution**: Execute `Deploy-ProcessExecution` to manage process executions.
4. **File Actions**: Execute `Deploy-FileActions` to manage file actions.
5. **Printer mapping**: Execute `Deploy-PrinterMappings` to manage printer mappings.
6. **Start-Menu shortcut**: Execute `Deploy-StartMenuEntry` to add shortcuts.
7. **Desktop shortcut**: Execute `Deploy-DesktopShortcut` to add shortcuts.
8. **All functions**: Execute `Invoke-UEMDeployment` to manage all functions at once.

&nbsp;

## 🔃 Envoy Refresh

Configurations can be refreshed during an active user session. A shortcut in the public start menu allows you to re-launch Envoy. All configurations will be reassessed and executed by referencing the Config.JSON file.

![Refresh](Images/EnvoyRefresh.png)

## ⚠️ Dependencies

Requires the following PowerShell modules. Installation will be handled by the MSI installer. If required modules are already installed it will skip the installation. Otherwise it will download and install the latest version of these modules.

- `Microsoft.Graph.Authentication`
- `Microsoft.Graph.Groups`
- `Microsoft.Graph.Users`

&nbsp;

# 💻 Installation

> [!NOTE]
> Installation steps for previous versions starting with 1.1.* can be found [here](https://github.com/j0eyv/Envoy/blob/1.1.-versions/README.md). Installation steps for versions starting with 1.2.* can be found [here](https://github.com/j0eyv/Envoy/blob/1.2.-versions/README.md).

> [!IMPORTANT]
> If you are upgrading from version 1.2.* to 1.3.*, please be aware that this release introduces breaking changes if not followed correctly! See [Upgrade.md](Upgrade.md).

Complete the following steps to set up Envoy correctly in your environment.

## App registration

Envoy retrieves user and group membership information from Microsoft Entra ID via Microsoft Graph. It uses the Microsoft.Graph.Authentication, Microsoft.Graph.Groups, and Microsoft.Graph.Users modules to access and query directory data. Envoy requires an App Registration with the appropriate Graph API permissions.

**1. Create the App registration:** Go to https://entra.microsoft.com/ -> Identity -> Applications -> App registrations -> New registration.

![EntraAppReg1](Images/AppReg1.png)

**2. Fill in the desired App registration name:** For example Envoy. Select Single tenant and configure a redirect URI (http://localhost). Finally click Create.

![EntraAppReg2](Images/AppReg2-1.3.png)

**3. Create the newly created App Registration and add another Redirect URI: Mobile and desktop applications -> ms-appx-web://Microsoft.AAD.BrokerPlugin/<your_client_id>

![EntraAppReg5](Images/AppReg5-1.3-v2.png)

**4. Enable Public Client flows:** Make sure to enable Public client flows.

![EntraAppReg3](Images/AppReg3-1.3.png)

**5. Set required API permissions:** Microsoft Graph
  -	Group.Read.All (Delegated)
  -	GroupMember.Read.All (Delegated)
  -	User.Read (Delegated)
  -	User.Read.All (Delegated)

  Make sure to **Grant admin consent**.

![EntraAppReg3](Images/AppReg4-1.3-v2.png)

**Manual trigger:**

Option 1: When Envoy is launched from a test or admin machine, an interactive permission prompt will automatically appear. These permissions must be granted for proper functionality.

Option 2: Run the command below from a test or admin device to authenticate with Microsoft Graph. Ensure your account has the necessary Entra ID permissions, such as Application Administrator. Fill in the ClientId from the App Registration and TenantId.

```
# Install and Import required modules
"Microsoft.Graph.Authentication", "Microsoft.Graph.Groups", "Microsoft.Graph.Users" | ForEach-Object {
    if (-not (Get-Module -ListAvailable -Name $_)) { Install-Module -Name $_ -Scope CurrentUser -Force -AllowClobber }
    Import-Module $_
}

# Connect to Entra ID using Microsoft Graph delegated permissions
Connect-MgGraph -ClientId "eccfcb90-****-****-*********" -TenantId "42ba3ed1-a****-****-*************" -Scopes "User.Read.All","Group.Read.All","GroupMember.Read.All" -NoWelcome
```

This will result in the following Interactive popup. Accept the permissions and grant admin consent.

![AppReg5](Images/AppReg5.png)


## Install Envoy
Visit the official releases page: 👉 https://github.com/j0eyv/Envoy/releases

Look for the latest release at the top of the page (marked with a “Latest” label). Under the Assets section of that release, click the .msi file (e.g., Envoy.msi) to download the Windows installer. Once downloaded, distribute the MSI installation to your managed endpoints.

> [!IMPORTANT]
> The installation **must** be installed in `C:\ProgramData\Envoy`. Using a different location might break the entire product.

The installation process is simple—just run the MSI file manually to start the setup. For bulk or unattended deployments, it's recommended to use silent installation parameters for automation. Therefor, use `msiexec /i "C:\Path\To\Envoy.msi" /qn /norestart`. While using **Intune**, you can simply distribute Envoy via the available methods.

- `/i Envoy.msi`: Installs the MSI package named Envoy.msi.
- `/qn`: Quiet mode with no UI.
- `/norestart`: Prevents the installer from restarting the system.

## Configure Envoy

Once distributed, we only need to make sure the `Config.JSON` file is being used is filled correctly. The default config file that comes with the installation is mainly filled with examples. There are currently 2 ways to build to desired configuration file:

- **Manual:** See the the detailed [documentation](#-functions) for configuration examples)
- **Config Builder:** GUI interface with step-by-step instructions and configuration download -> [https://www.envoycontrol.com/config](https://www.envoycontrol.com/config).

![ConfigBuilder1](Images/ConfigBuilder1.png)

![ConfigBuilder2](Images/ConfigBuilder2.png)

![ConfigBuilder3](Images/ConfigBuilder3.png)

![ConfigBuilder4](Images/ConfigBuilder4.png)

&nbsp;

## Centralized distribution

There are several ways to manage and distribute a centralized `Config.JSON`, and the best approach depends entirely on your existing infrastructure and the tools it supports. Unfortunately, there’s no universal solution that satisfies all use cases and security best practices. Therefore, we provide a few potential options that you will need to implement yourself. It's important to evaluate each option carefully, as some may have limitations or may not fully align with recommended security practices.

### GitHub-Hosted Config Delivery
This method stores a Config.JSON file in a private GitHub repository. Windows clients securely download this file on a schedule using PowerShell and a GitHub Personal Access Token (PAT) for authentication.

- ✅ Secure storage in a private repo
- 🔐 Access controlled via read-only PAT
- ⏰ Automated 15-minute interval download using a Scheduled Task
- 🛠️ Script uses Invoke-WebRequest with the raw.githubusercontent.com endpoint

A scheduled task for this method is delivered with the Envoy.MSI installation file. You are required to configure a few parameters within `C:\ProgramData\Envoy\Core\Update\Download-EnvoyConfig-Github.ps1`. Find a way to distribute this PowerShell script into your endpoints.

> [!NOTE]
> This method requires you to create a private Github repo with a Personal Access Token (PAT). See this link for more information: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens

### Azure Blob

This method stores a Config.JSON file in a Azure Blob container. Windows clients securely download this file on a schedule using PowerShell.

- ✅ Secure storage in a Azure Blob container
- ⏰ Automated 15-minute interval download using a Scheduled Task
- 🛠️ Script uses Invoke-WebRequest with the blob.core.windows.net endpoint

A scheduled task for this method is delivered with the Envoy.MSI installation file. You are required to configure a single parameters within `C:\ProgramData\Envoy\Core\Update\Download-EnvoyConfig-Blob.ps1`. Find a way to distribute this PowerShell script into your endpoints.

> [!NOTE]
> This method requires you to create a Azure Blob storage within a Storage Account. The endpoint running Envoy, should have read access to the blob storage. Try accessing the URL from a browser in the endpoint to verify if access is allowed. See this link for more information: https://learn.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal

### Azure Files

This method stores a Config.JSON file in a Azure File Share. Windows clients securely download this file on a schedule using PowerShell.

- ✅ Secure storage in a Azure File Share
- 🔐 Access controlled via Storage Account key (less recommended)
- ⏰ Automated 15-minute interval download using a Scheduled Task
- 🛠️ Script uses Invoke-WebRequest with the file.core.windows.net endpoint

A scheduled task for this method is delivered with the Envoy.MSI installation file. You are required to configure a few parameters within `C:\ProgramData\Envoy\Core\Update\Download-EnvoyConfig-AzureFiles.ps1`. Find a way to distribute this PowerShell script into your endpoints.

> [!NOTE]
> This method requires you to create a Azure File Share within a Storage Account. The endpoint running Envoy, should be able to reach the file share. See this link for more information: https://learn.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-portal?tabs=azure-portal

### File Share (on-prem)
This method stores a Config.JSON file in a regular File Share. Windows clients securely download this file on a schedule using PowerShell.

- ✅ Secure storage in a File Share
- 🔐 Access controlled via NTFS permissions
- ⏰ Automated 15-minute interval download using a Scheduled Task
- 🛠️ Scheduled Task should be configured to run under a service account

A scheduled task for this method is delivered with the Envoy.MSI installation file. You are required to configure a few parameters within `C:\ProgramData\Envoy\Core\Update\Download-EnvoyConfig-FileShare.ps1`. The scheduled task should run under a service account which needs to be configured by yourself in the scheduled task. Find a way to distribute this PowerShell script and the fully configured scheduled task into your endpoints.


# Support the project

> [!NOTE]
> If you're making money with our open source work, consider giving back to what made it possible.

Envoy is completely free to use! That said, building and improving it takes significant time and resources. By supporting us, you help keep Envoy open source and freely available for everyone. Your contribution makes ongoing development and maintenance possible.

- Keep Envoy free and accessible for everyone
- Support ongoing development and new features
- Help maintain and improve documentation

**Github Sponsors:** https://github.com/sponsors/j0eyv
**Buy me a coffee**: https://buymeacoffee.com/j0eyv


















