# PSReleaseTools

[![PSGallery Version](https://img.shields.io/powershellgallery/v/PSReleaseTools.png?style=for-the-badge&logo=powershell&label=PowerShell%20Gallery)](https://www.powershellgallery.com/packages/PSReleaseTools/) [![PSGallery Downloads](https://img.shields.io/powershellgallery/dt/PSReleaseTools.png?style=for-the-badge&label=Downloads)](https://www.powershellgallery.com/packages/PSReleaseTools/)

<p align="left"><img align="left" src = "images/PowerShell_avatar.png"></p>

## Deprecation Notice

### 14 May, 2026

This module is now deprecated and moving to an archive status. No new development is planned. When PowerShell 7 was first introduced, there were very few automated installation methods. Since then, Microsoft has made PowerShell 7 available through multiple means such as the Windows Store, winget, and third-party package management providers. There is no reason to keep this project up-to-date given the number of better alternatives.

You are welcome to continue using this module for as long as it is compatible with Microsoft's GitHub workflow. Please feel free to fork the repository if you wish to maintain a version for yourself or your organization.

## Overview

This PowerShell module provides a set of commands for working with the latest releases from the [PowerShell GitHub repository](https://github.com/PowerShell/PowerShell). The module contains commands to get summary information about the most current PowerShell version as well as functions to download some or all of the release files or install the latest stable and/or preview build of PowerShell.

These commands utilize the GitHub API, which is subject to rate limits. It is recommended that you save results of commands like `Get-PSReleaseAsset` to a variable. If you encounter an error message for `Invoke-RestMethod` like "Server Error" then you have likely exceeded the API limit. You will need to wait a bit and try again. _*You do not need to have or use a GitHub account to use these commands.*_

This module should work cross-platform on both Windows PowerShell 5.1 and PowerShell 7.x, but is primarily intended for Windows platforms.

You can install this module from the PowerShell Gallery.

```powershell
Install-PSResource PSReleaseTools
```

## The Module

The module currently has 9 commands:

| Name | Alias | Synopsis |
|------|-------|----------|
| [Get-PSIssue](docs/Get-PSIssue.md) |  | Get PowerShell issues from GitHub. |
| [Get-PSIssueLabel](docs/Get-PSIssueLabel.md) |  | Get PowerShell issue labels. |
| [Get-PSReleaseAsset](docs/Get-PSReleaseAsset.md) |  | Get PowerShell release assets. |
| [Get-PSReleaseCurrent](docs/Get-PSReleaseCurrent.md) |  | Get the current PowerShell 7.x release. |
| [Get-PSReleaseSummary](docs/Get-PSReleaseSummary.md) |  | Get information about the latest PowerShell 7.x release. |
| [Install-PowerShell](docs/Install-PowerShell.md) | *Install-PSCore* | Install the latest PowerShell 7.x version on Windows. |
| [Install-PSPreview](docs/Install-PSPreview.md) |  | Install the latest PowerShell Preview on Windows. |
| [Open-PSIssue](docs/Open-PSIssue.md) |  | Open a PowerShell issue in your browser. |
| [Save-PSReleaseAsset](docs/Save-PSReleaseAsset.md) |  | Download the latest PowerShell from the Github PowerShell repository. |

All of the functions take advantage of the [GitHub API](https://developer.github.com/v3/ "learn more about the API") which in combination with either [Invoke-RestMethod](http://go.microsoft.com/fwlink/?LinkID=217034 "read online help for the cmdlet") or [Invoke-WebRequest](http://go.microsoft.com/fwlink/?LinkID=217035  "read online help for the cmdlet"), allow you to programmatically interact with GitHub.

### Get Current Release

The first command, [`Get-PSReleaseCurrent`](docs/Get-PSReleaseCurrent.md) can provide a quick summary view of the latest stable or preview release.

```powershell
PS C:\> Get-PSReleaseCurrent

Name                                   OnlineVersion       Released                    LocalVersion
----                                   -------------       --------                    ------------
v7.5.2 Release of PowerShell           7.5.2               6/24/2025 9:31:53 PM               7.5.2
```

The command writes a custom object to the pipeline which has additional properties.

```powershell
PS C:\> Get-PSReleaseCurrent -preview | Select-Object *

Name         : v7.6.0-preview.4 Release of PowerShell
Version      : v7.6.0-preview.4
Released     : 4/8/2025 10:00:57 PM
LocalVersion : 7.5.2
URL          : https://github.com/PowerShell/PowerShell/releases/tag/v7.6.0-preview.4
Draft        : False
Prerelease   : True
```

### Summary Information

[`Get-PSReleaseSummary`](docs/Get-PSReleaseSummary.md) queries the PowerShell repository release page and constructs a text summary. You can also have the command write the report text as markdown.

![get-psreleasesummary.png](/images/get-psreleasesummary.png)

I put the release name and date right at the top so you can quickly check if you need to download something new. In GitHub, each release file is referred to as an _asset_. The [`Get-PSReleaseAsset` ](docs/Get-PSReleaseAsset.md) command will query GitHub about each file and write a custom object to the pipeline.

```powershell
PS C:\> Get-PSReleaseAsset

FileName  : powershell-7.5.2-1.cm.aarch64.rpm
Family    : {Arm, CBL-Mariner}
Format    : rpm
Updated   : 6/24/2025 8:49:15 PM
Downloads : 38,280
SizeMB    : 37
URL       : https://github.com/PowerShell/PowerShell/releases/download/v7.5.2/powershell-7.5.2-1.cm.aarch64.rpm

FileName  : powershell-7.5.2-1.cm.x86_64.rpm
Family    : CBL-Mariner
Format    : rpm
Updated   : 6/24/2025 8:49:18 PM
Downloads : 13,333
SizeMB    : 39
URL       : https://github.com/PowerShell/PowerShell/releases/download/v7.5.2/powershell-7.5.2-1.cm.x86_64.rpm

FileName  : powershell-7.5.2-1.rh.x86_64.rpm
Family    : {Rhel, CentOS}
Format    : rpm
Updated   : 6/24/2025 8:49:21 PM
Downloads : 9,372
SizeMB    : 71
URL       : https://github.com/PowerShell/PowerShell/releases/download/v7.5.2/powershell-7.5.2-1.rh.x86_64.rpm
...
```

By default, the command will display assets for all platforms, but I added a `-Family` parameter if you want to limit yourself to a single entry like MacOS.

```powershell
PS C:\> Get-PSReleaseAsset -Family MacOS | Select *

FileName      : powershell-7.5.2-osx-arm64.pkg
Family        : {Arm, MacOS}
Format        : pkg
Size          : 71331087
Hash          : 84F095DE7DDD62C0B2F1D6DC68EFE0B35A0FB90C2FE5127DE2B0DD15EAB4D95C
Created       : 6/24/2025 8:49:39 PM
Updated       : 6/24/2025 8:49:42 PM
URL           : https://github.com/PowerShell/PowerShell/releases/download/v7.5.2/powershell-7.5.2-osx-arm64.pkg
DownloadCount : 309127

FileName      : powershell-7.5.2-osx-arm64.tar.gz
Family        : {Arm, MacOS}
Format        : gz
Size          : 70976191
Hash          : A02D1D79589C71E8D35E458D90F085CFC1F0A688EBBEA4DAB8632187C057F7A1
Created       : 6/24/2025 8:49:42 PM
Updated       : 6/24/2025 8:49:46 PM
URL           : https://github.com/PowerShell/PowerShell/releases/download/v7.5.2/powershell-7.5.2-osx-arm64.tar.gz
DownloadCount : 16079
...
```

Of course, you will want to download these files, which is the job of the last command. By default, `Get-PSReleaserAsset` will save all files to the current directory unless you specify a different path. You can limit the selection to a specific platform with the `-Family` parameter, which uses a validation set.

```powershell
PS C:\> Save-PSReleaseAsset -Family Ubuntu -Path C:\Temp -WhatIf
What if: Performing the operation "Downloading https://github.com/PowerShell/PowerShell/releases/download/v7.5.2/powershell_7.5.2-1.deb_amd64.deb" on target "c:\Temp\powershell_7.5.2-1.deb_amd64.deb".
```

You can select multiple names. If you choose Windows, there is a dynamic parameter called `-Format` where you can select ZIP or MSI. [`Save-PSReleaseAsset`](docs/Save-PSReleaseAsset.md) supports `-WhatIf`.

I also realized you might run `Get-PSReleaseAsset`, perhaps to examine details before downloading. Since you have those objects, why not be able to pipe them to the save command?

```powershell
PS C:\> Get-PSReleaseAsset -Family Rhel | Save-PSReleaseAsset -Path C:\Temp -Passthru

        Directory: C:\Temp


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a---         8/22/2025  10:05 AM       74955389 powershell-7.5.2-1.rh.x86_64.rpm
```

The current version of this module uses regular expression named captures to pull out the file name and corresponding SHA256 hashes. The save command then calls [Get-FileHash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-5.1&WT.mc_id=ps-gethelp "read online help for the cmdlet") to get the current file hash and compares them.

### Installing a Build

On Windows, it is pretty easy to install a new build with a one-line command:

```powershell
Get-PSReleaseAsset -Family Windows -Only64Bit -Format msi |
Save-PSReleaseAsset -Path d:\temp -Passthru | Invoke-Item
```

Or you can use one of two newer functions to install the latest 64bit release. You can specify the interaction level.

[`Install-PSPreview`](/docs/Install-PSPreview.md) will download the latest 64-bit _*preview*_ build for Windows and kick off the installation.

 ```powershell
Install-PSPreview -Mode Passive
 ```

[`Install-PowerShell`](/docs/Install-PowerShell.md) will do the same thing but for the latest stable release. The command retains `Install-PSCore` as an alias.

```powershell
Install-PowerShell -Mode Quiet -EnableRemoting -EnableContextMenu -EnableRunContext
```

The functionality of these commands could have been combined, but I decided to leave them as separate commands, so there is no confusion about what you are installing. In both cases, an installation log file will be created at `$env:TEMP\PS7Install.log`.

Non-Windows platforms have existing command-line installation tools that don't need to be replaced. Plus, I don't have the resources to develop and test installation techniques for all of the non-Windows options. That is why install-related commands in this module are limited to Windows.

### Preview Builds

Beginning with v0.8.0 of this module, command functions have a `-Preview` parameter, which will get the latest preview build. Otherwise, the commands will use the latest stable release.

### PowerShell Repository Issues

A new set of commands have been introduced in [v1.8.0](https://github.com/jdhitsolutions/PSReleaseTools/releases/tag/v1.8.0 "see release 1.8.0"). These commands are intended to make it easier for you to look at [issues from the PowerShell GitHub repository](https://github.com/PowerShell/PowerShell/issues). The idea is that you can take a peek at open issues from your PowerShell session and then open the issue in your browser to learn more or contribute.

#### Get-PSIssue

[`Get-PSIssue`](docs/Get-PSIssue.md) is intended to get open PowerShell issues from Github. With no parameters, you can get the 25 most recent issues. Use the `-Count` parameter to increase that value using one of the possible values. The actual number of issues returned may vary depending on the rest of your command and how GitHub pages results.

You can also fine-tune your search to get issues that have been updated since a given date. Finally, you can also limit your search to issues tagged with a specific label.

![Get-PSIssue](images/get-psissue.png)

The function writes a custom object to the pipeline and includes a default formatted view. If you are running PowerShell 7, the issue body will be rendered as markdown.

Here is another way you might use the command.

![Get-PSIssue Summary](images/get-psissue-summary.png)

__Note:__ The _PSIssue_ commands use the GitHub API and anonymous connections. The API has rate limits. If you run one of these commands excessively in a short period of time, you might see an error about exceeding the rate limit. If this happens, all you can do is wait an hour and try again. You can read more about [GitHub rate-limiting](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting "read the Github documentation on rate limiting").

#### Get-PSIssueLabel

To make it easier to search for issues based on a label run [`Get-PSIssueLabel`](docs/Get-PSIssueLabel.md). This command will list available labels from the PowerShell repository. However, you most likely won't need to run this command often. When you import the `PSReleaseTools` module, it will create a global variable called `$PSIssueLabel`.

```powershell
PS C:\> $PSIssueLabel

name                         description
----                         -----------
.NET                         Pull requests that update .net code
Area-Build
Area-Cmdlets
Area-Cmdlets-Archive
Area-Cmdlets-Core
Area-Cmdlets-Management
Area-Cmdlets-Utility
Area-Console
Area-DSC
...
```

This variable is used as part of an argument completer for the `Labels` parameter on `Get-PSIssue`.

#### Open-PSIssue

Finally, you may want to respond to an issue. If you run [`Open-PSIssue`](docs/Open-PSIssue.md) without any parameters, it should open the Issues section of the PowerShell repository in your browser. Or you can pipe an issue object to the command, as long as you include the `Url` property.

```powershell
Get-PSIssue | Select-Object Updated,Labels,Title,Url | Out-GridView -PassThru | Open-PSIssue
```

There are no plans to add a command to open a new issue from a PowerShell session. You can use `Open-PSIssue` to get to GitHub and then use your browser to submit a new issue.

## Support

If you have suggestions or encounter problems, please post an issue in this GitHub repository. If you find this project useful, or any of my work, please consider a small support donation.

[<kbd>:heart:Sponsor</kbd>](https://paypal.me/jdhitsolutions?locale.x=en_US)
