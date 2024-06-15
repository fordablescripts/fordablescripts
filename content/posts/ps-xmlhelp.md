+++
authors = "David Ford"
title = "PowerShell: Headers, XML-Based Help"
date = "2024-06-15T12:00:00-04:00"
description = "documentation guidelines for XML-based Help"
tags = [
    "powershell",
    "get-help",
    "comment",
    "xml",
    "headers",
]
categories = [
    "tutorial",
]
+++

PSVersion: 7.4  
OS: Ubuntu 24.04 LTS

TL;DR: [XML-Based Help Example](#xml-based-example)

<!--Intro-->

## XML-Based Help

The `Get-Help` cmdlet in PowerShell is a handy tool, commonly used for obtaining details about commands and modules. It’s equally _helpful_ for user-written scripts and functions, serving as a structured way to document the Scope, Parameters, Examples, and any additional Notes.

There are three main ways to accomplish this: Comment-Based, XML-Based, and Online. In this Article, we will be going over XML-Based Help.

<!--Brief Overview-->

### Message in a Bottle

XML-Based Help involves creating an XML file separate from your script that contains the help content. This method is more structured and is typically used for more complex scenarios or when you want to maintain documentation separately from the code. XML-Based comments work great in large organizations.

In the following showcase, we will create an XML-Based help file using the platyPS PowerShell Module.

#### Creating the Markdown and XML file

Import the Module:

```powershell
PS /home/fordablescripts> install-module -name platyPS -force
PS /home/fordablescripts> import-module platyPS 
```

Here we have a function that gets the cron jobs created for the current signed in user. Following the Comment-Based Help Documentation[^1], we add a comment block and add the `.EXTERNALHELP` keyword with the expected path for the get-help file:

```powershell
function get-cron {
    <#
    .EXTERNALHELP /home/fordablescripts/Documents/Dev/fordablecontent/getcronhelp.xml
    #>
    Try {
        $usr = id -un
        $getcron = crontab -u $usr -l
        return $getcron
    } catch {
        Write-Host "Error: $_"
        return
    }
}
```

Once we run the function, it's saved in the current session as a command. We use platyPS and create a new markdown help file. The output will list the saved location and file name. Here's an example:

```powershell
PS /home/fordablescripts> new-markdownhelp -command "get-cron" -outputfolder "/home/fordablscripts/Documents/Dev/fordablecontent/" -force 
WARNING: [GetHelpFileName] Cannot find module for command 'get-cron'

    Directory: /home/fordablescripts/Documents/Dev/fordablecontent

UnixMode         User Group         LastWriteTime         Size Name
--------         ---- -----         -------------         ---- ----
-rw-rw-r--    grumble grumble      6/9/2024 11:41          450 get-
                                                               cron
                                                               .md

```

If we move to that Directory we can see the saved file:

```powershell
PS /home/fordablescripts> cd "./Documents/Dev/fordablecontent/"
PS /home/fordablescripts/Documents/Dev/fordablecontent> ls
get-cron.md          powershell-headers.md
get-cron.ps1         powershellwithrandoms.md
md-syntax.es-mx.md   sitemap.xml
md-syntax.md         xml-basedhelp.md
```

Opening the Markdown file will show the below template:

```markdown
---
external help file:
Module Name:
online version:
schema: 2.0.0
---

# get-cron

## SYNOPSIS
{{ Fill in the Synopsis }}

## SYNTAX

~~~
get-cron
~~~

## DESCRIPTION
{{ Fill in the Description }}

## EXAMPLES

### Example 1
~~~powershell
PS C:\> {{ Add example code here }}
~~~

{{ Add example description here }}

## PARAMETERS

## INPUTS

### None
## OUTPUTS

### System.Object
## NOTES

## RELATED LINKS
```

Now we fill in the needed information. Be sure to stick as closely to the template format as possible to avoid any issues:

```markdown
---
external help file: /home/fordablescripts/Documents/Dev/fordablecontent/getcronhelp.xml
Module Name:
online version:
schema: 2.0.0
---

# get-cron

## SYNOPSIS
Gets the scheduled cron jobs

## SYNTAX

~~~
get-cron
~~~

## DESCRIPTION
Lists the current users cron jobs

## EXAMPLES

### Example 1
~~~powershell
PS C:\> get-cron
no crontab for fordablescripts
~~~

call the function 'get-cron' to list the cron jobs for the current user

## PARAMETERS

## INPUTS

### None
## OUTPUTS

### System.Object
## NOTES

## RELATED LINKS
```

Once the markdown file is looking good we can go ahead and create our new XML help file:

```powershell
PS /home/fordablescripts> new-externalhelp -path "./Documents/Dev/fordablecontent/" -outputpath "/" -force

    Directory: /home/fordablescripts/Documents/Dev/fordablecontent

UnixMode         User Group         LastWriteTime         Size Name
--------         ---- -----         -------------         ---- ----
-rw-rw-r--    grumble grumble      6/9/2024 12:25         2038 getc
                                                               ronh
                                                               elp.
                                                               xml
```

#### XML-Based Example

If we go and open our new XML help file, this is what we see:

```xml
<?xml version="1.0" encoding="utf-8"?>
<helpItems schema="maml" xmlns="http://msh">
  <command:command xmlns:maml="http://schemas.microsoft.com/maml/2004/10" xmlns:command="http://schemas.microsoft.com/maml/dev/command/2004/10" xmlns:dev="http://schemas.microsoft.com/maml/dev/2004/10" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">
    <command:details>
      <command:name>get-cron</command:name>
      <command:verb>get</command:verb>
      <command:noun>cron</command:noun>
      <maml:description>
        <maml:para>Gets the scheduled cron jobs</maml:para>
      </maml:description>
    </command:details>
    <maml:description>
      <maml:para>Lists the current users cron jobs</maml:para>
    </maml:description>
    <command:syntax>
      <command:syntaxItem>
        <maml:name>get-cron</maml:name>
      </command:syntaxItem>
    </command:syntax>
    <command:parameters />
    <command:inputTypes>
      <command:inputType>
        <dev:type>
          <maml:name>None</maml:name>
        </dev:type>
        <maml:description>
          <maml:para></maml:para>
        </maml:description>
      </command:inputType>
    </command:inputTypes>
    <command:returnValues>
      <command:returnValue>
        <dev:type>
          <maml:name>System.Object</maml:name>
        </dev:type>
        <maml:description>
          <maml:para></maml:para>
        </maml:description>
      </command:returnValue>
    </command:returnValues>
    <maml:alertSet>
      <maml:alert>
        <maml:para></maml:para>
      </maml:alert>
    </maml:alertSet>
    <command:examples>
      <command:example>
        <maml:title>-------------------------- Example 1 --------------------------</maml:title>
        <dev:code>PS C:\&gt; get-cron
no crontab for fordablescripts</dev:code>
        <dev:remarks>
          <maml:para>call the function 'get-cron' to list the cron jobs for the current user</maml:para>
        </dev:remarks>
      </command:example>
    </command:examples>
    <command:relatedLinks />
  </command:command>
</helpItems>
```

With our XML-based help ready we can verify the information using `get-help`:

```powershell
PS /home/fordablescripts> get-help get-cron

NAME
    get-cron
    
SYNOPSIS
    Gets the scheduled cron jobs. New Update
    
    
SYNTAX
    get-cron [<CommonParameters>]
    
    
DESCRIPTION
    Lists the current users cron jobs
    

RELATED LINKS

REMARKS
    To see the examples, type: "Get-Help get-cron -Examples"
    For more information, type: "Get-Help get-cron -Detailed"
    For technical information, type: "Get-Help get-cron -Full"
```

```powershell
PS /home/fordablescripts> get-help get-cron -examples

NAME
    get-cron
    
SYNOPSIS
    Gets the scheduled cron jobs
    
    
    -------------------------- Example 1 --------------------------
    
    PS C:\> get-cron
    no crontab for fordablescripts
    
    call the function 'get-cron' to list the cron jobs for the 
    current user
```

When using XML-Based Help, it's important to keep a few recommendations in mind:

* Keep your XML help file up-to-date and accurate. This will ensure that users have the most current and relevant information about your functions.

* Include clear and concise descriptions of your functions, as well as examples to help users understand how to use them.

* If your team utilizes DFS, keep in mind that access to the file is needed to be able to view the help information.

Leveraging XML-Based Help can be useful in documenting PowerShell Functions or Modules effectively when you have a large internal team. By creating a separate XML file for your help content, you can maintain documentation separately from your code, making it easier to manage and update.

Get-Scripting

[^1]:[PowerShell: Headers, Comment-Based Help](https://www.fordablescripts.com/posts/ps-commenthelp/)
