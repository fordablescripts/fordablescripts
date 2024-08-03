+++
authors = "David Ford"
title = "PowerShell: Get-Help, Online-Based Help"
date = "2024-07-30T12:00:00-04:00"
description = "documentation guidelines for Online-based Help"
tags = [
    "powershell",
    "get-help",
    "comment",
    "Online",
    "headers",
]
categories = [
    "tutorial",
]
+++

PSVersion: 7.4  
OS: Ubuntu 24.04 LTS

TL;DR: [Online-Based Help Example](#example)

<!--Intro-->

## Help _but Online_

The `Get-Help` cmdlet in PowerShell is a handy tool, commonly used for obtaining details about commands and modules. It’s equally _helpful_ for user-written scripts and functions, serving as a structured way to document the Scope, Parameters, Examples, and any additional Notes.

There are three main ways to accomplish this: Comment-Based, XML-Based, and Online. In this Article, we will be going over Online-Based Help.

<!--Brief Overview-->

### Take me to your Leader

The ability to provide updated information to a group of users at the speed of light is the main upside to using Online `Get-Help`. There are a few good reasons why you'll want to take advantage of it:  

* Version Control: The information remains up-to-date and allows for easy tracking and management of changes.

* Collaboration: If the linked resource is a collaborative platform, such as a wiki or a discussion forum, users can contribute their knowledge and provide feedback.

Let's go over what that looks like with an example scenario.

#### Engage

In this function example, I've written a tiny function that changes the output of a Debian-based Linux distro's Debian Package Manager cmdlet `dpkg -l` into a PowerShell Object.

```powershell
Function Get-Packages {
        ((dpkg -l).split("ii")).Trimstart() | 
        Select-Object -Skip 6 | 
        ForEach-Object {$_ -replace '\s{2,}', ', '} | 
        ConvertFrom-Csv -Header Name, Version, Architecture, Description | 
        Select-Object -Property Name, Version, Architecture, Description
}
```

Now that I have my stellar function ready, let's start the documentation!

In my [XML-Based article](https://www.fordablescripts.com/posts/ps-xmlhelp/), we went over using the PlatyPS PowerShell Module to create a Template Markdown file. I'll be using that again here to make things a little easier.

First, I'll set the function in the terminal then use `new-markdownhelp`:

```powershell
PS /home/grumble> new-markdownhelp -command "Get-Packages" -outputfolder "/home/fordablscripts/Documents/Dev/fordablecontent/Help" -force
WARNING: [GetHelpFileName] Cannot find module for command 'Get-Packages'

    Directory: /home/fordablscripts/Documents/Dev/fordablecontent/Help

UnixMode         User       Group         LastWriteTime         Size Name
--------         ----       -----         -------------         ---- ----
-rw-r--r--       grumble    grumble      8/3/2024 10:15          559 Get-Packages.md
```

This creates a quick template where I can add the functions information. Here's a blank template:

~~~markdown
---
external help file:
Module Name:
online version:
schema: 2.0.0
---

# Get-Packages

## SYNOPSIS
{{ Fill in the Synopsis }}

## SYNTAX

```
Get-Packages
```

## DESCRIPTION
{{ Fill in the Description }}

## EXAMPLES

### Example 1
```powershell
PS C:\> {{ Add example code here }}
```

{{ Add example description here }}

## PARAMETERS

## INPUTS

### None
## OUTPUTS

### System.Object
## NOTES

## RELATED LINKS
~~~

Using Github, I'll upload this `.md` file to the [Fordablescripts' Github PowerShell Repository](https://github.com/fordablescripts/PowerShell/blob/main/Help/Get-Packages.md), make the necessary tweaks and changes, and add the link to the page back into the function along with some basic help information.

##### Example

```powershell
Function Get-Packages {
    <#
    .SYNOPSIS
    List installed packages for Debian-based Linux systems
    .DESCRIPTION
    Runs the Debian package manager (dpkg) and converts the output to a PowerShell Object.
    .LINK
    https://github.com/fordablescripts/PowerShell/blob/main/Help/Get-Packages.md
    .EXAMPLE
    Get-Installed | Where-Object {$_.Name -like "fire*"} | Select-Object -Property Name, Version | Format-List
    #>
        ((dpkg -l).split("ii")).Trimstart() | 
        Select-Object -Skip 6 | 
        ForEach-Object {$_ -replace '\s{2,}', ', '} | 
        ConvertFrom-Csv -Header Name, Version, Architecture, Description | 
        Select-Object -Property Name, Version, Architecture, Description
}
```

And just like that we have our Online-Based help article. With `Get-Help Get-Packages -Online`, users will be directed to my github page that will have the functions information along with any updated changes.

### Enlist in the PowerShell Community Today

This Article concludes my `Get-Help` series where I go over the differences and importance of using documentation for your PowerShell scripts. We've gone over Comment-Based Help[^1], XML-Based Help[^2], and finally Online-Based help with this Article. You can learn more about this topic over at [Microsoft's PowerShell Hub Site](https://learn.microsoft.com/en-us/powershell/).

My last recommendation with this series and any that I write, is to become a member of the community.

For the PowerShell community, you can file issues, bugs, or feature requests over at the [GitHub repository](https://github.com/powershell/powershell/issues) or in one of these public forums:

* [PowerShell User Groups List](https://aka.ms/psusergroup)
* [PowerShell Tech Community](https://techcommunity.microsoft.com/t5/PowerShell/ct-p/WindowsPowerShell)
* [DSC Community](https://dsccommunity.org/)
* [PowerShell.org](https://forums.powershell.org/)
* [StackOverFlow](https://stackoverflow.com/questions/tagged/powershell)
* [r/PowerShell subreddit](https://www.reddit.com/r/PowerShell/)
* PowerShell Virtual User Group - join via:
    * [Slack](https://aka.ms/psslack)
    * [Discord](https://aka.ms/psdiscord)

Now, Join us in the fight against the darkness of outdated knowledge and embrace the light of community-driven learning. With online-based help, you'll have the opportunity to expand your skills, gain valuable insights, and become a trusted member of the PowerShell community.

Don't let your knowledge be limited by the confines of your local library. With online-based help, you can access the collective, ensuring that you have the most accurate and relevant information to continue growing and improving your PowerShell skills.

So, what are you waiting for? Voyage with the PowerShell Community and become a part of something greater. Together, we can create a brighter future for PowerShell, where knowledge and innovation march us forward!

Get-Scripting

[^1]:Fordablescripts: [Comment-Based Help](https://www.fordablescripts.com/posts/ps-commenthelp/)

[^2]:Fordablescripts: [XML-Based Help](https://www.fordablescripts.com/posts/ps-xmlhelp/)
