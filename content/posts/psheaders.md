+++
authors = "David Ford"
title = "PowerShell: Comment-Based Help"
date = "2024-05-27T23:12:56-04:00"
description = "documentation guidelines for comment-based Help"
tags = [
    "powershell",
    "get-help",
    "comment",
    "headers",
]
categories = [
    "tutorial",
]
+++

PSVersion: 7.4  
OS: Ubuntu 24.04 LTS


TL;DR: [Comment-Based Help Example](#comment-based-help-example)

<!--Intro-->

## Help Me Help You	 	 	 	

The `Get-Help` cmdlet in PowerShell is a handy tool, commonly used for obtaining details about commands and modules. It’s equally _helpful_ for user-written scripts and functions, serving as a structured way to document the Scope, Parameters, Examples, and any additional Notes.

There are three main ways to accomplish this: Comment-Based, XML-Based, and Online. In this Article, we will be going over Comment-Based Help. 

<!--Brief Overview-->

### Comment-Based Help

Comment-Based Help is the most direct method, where you write the documentation directly within your script or function using formatted comments. This type of help is easily accessible and what you'll most commonly see when working with scripts.

You have two options for creating comments:

1. Prefix each line with a comment symbol (#).  
    ```powershell
    #.helpkeyword
    #helpcontent
    #.helpkeyword
    #helpcontent
    ```
2. Use the <# and #> symbols to create a comment block.  
    ```powershell
    <#
    .helpkeyword
    helpcontent
    .helpkeyword
    helpcontent
    #>
    ```
Remember the following rules when utilizing Comment-Based Help:

* All lines within a comment block are treated as comments, but only comments containing Keywords will be shown in `Get-Help`.

* If a comment-based help topic follows a non-help comment, leave at least one blank line between the last non-help comment and the start of the comment-based help:  
    ```powershell
    <#
    .helpkeyword
    helpcontent

    this is a non-help comment. This will be ignored with Get-Help

    .helpkeyword
    helpcontent
    #>
    ```

* Keywords define different sections of comment-based help. These keywords (e.g., .SYNOPSIS, .DESCRIPTION) are case-insensitive and can appear in any order.
    * PowerShell's Keywords and descriptions[^1]:
    ```powershell
    <# 
    .SYNOPSIS
    A brief description of the function or script. This keyword can be used only once in each topic.
    .DESCRIPTION
    A detailed description of the function or script. This keyword can be used only once in each topic.
    .PARAMETER <Parameter-Name>
    The description of a parameter. You can include a .PARAMETER keyword for each parameter in the function or script.
    .EXAMPLE
    A sample command that uses the function or script, optionally followed by sample output and a description. Repeat this keyword for each example.
    .INPUTS
    The Microsoft .NET Framework types of objects that can be piped to the function or script. You can also include a description of the input objects.
    .OUTPUTS
    The .NET Framework type of the objects that the cmdlet returns. You can also include a description of the returned objects.
    .NOTES
    Additional information about the function or script.
    .LINK
    The name of a related topic. Repeat this keyword for each related topic. This content appears in the Related Links section of the Help topic.
    .COMPONENT
    The name of the technology or feature that the function or script uses, or to which it's related. The Component parameter of Get-Help uses this value to filter the search results returned by Get-Help.
    .ROLE
    The name of the user role for the help topic. The Role parameter of Get-Help uses this value to filter the search results returned by Get-Help.
    .FUNCTIONALITY
    The keywords that describe the intended use of the function. The Functionality parameter of Get-Help uses this value to filter the search results returned by Get-Help.
    .FORWARDHELPTARGETNAME <Command-Name>
    Redirects to the Help topic for the specified command. You can redirect users to any Help topic, including Help topics for a function, script, cmdlet, or provider.
    .FORWARDHELPCATEGORY <Category>
    Specifies the Help category of the item in .FORWARDHELPTARGETNAME. Use this keyword to avoid conflicts when there are commands with the same name.
    Valid values are:
    Alias
    Cmdlet
    HelpFile
    Function
    Provider
    General
    FAQ
    Glossary
    ScriptCommand
    ExternalScript
    Filter
    All
    .REMOTEHELPRUNSPACE <PSSession-variable>
    Specifies a session that contains the Help topic. Enter a variable that contains a PSSession. This keyword is used by the Export-PSSession cmdlet to find the Help topics for the exported commands.
    .EXTERNALHELP <XMLHelpFile>
    Specifies the path and/or name of an XML-based Help file for the script or function.
    #>
    ```

* When adding comment-based help to a function, you can position it in one of three places:

    * At the start of the function body:  
        ```powershell
        function ifwork {
            <#
            .helpkeyword
            helpcontent
            #>
            function body
        }
        ```
    * At the end of the function body:  
        ```powershell
        function dowork {
            function body
            <#
            .helpkeyword
            helpcontent
            #>
        }
        ```
    * Directly before the function keyword. There can only be one blank line or less separating the function help’s last line and the function declaration:
        ```powershell    
        <#
        .helpkeyword
        helpcontent
        #>
        #blank line
        function didwork {
            function body
        }
        ```

* For scripts, comment-based help can be situated at the beginning or the end of the script's file. If using a digital signature, write the comment-based help at the beginning, as the signature block occupies the end.
  
* If the first element after the script help is a function declaration, you must have at least two blank lines between the script help’s end and the function declaration. Otherwise, it will be related to the function and not the script.

<!--Example-->

###### Comment-Based Help Example:


```powershell
<# 
.SYNOPSIS
This script does a good job.
.DESCRIPTION
This script does a good job by checking for work and then doing other work if work is found.
.NOTES
Created with:  Notepad++/ISE/VSCode
Created on:    2024/05/27
Author:        David Ford
Filename:      SameScriptDifferentDay.ps1
.EXAMPLE
& .\SameScriptDifferentDay.ps1 -parameter UsefulInformation
.Parameter UsefulInformation 
Specifies work to be done and then does work.
.LINK
https://www.fordablescripts.com/posts/powershell-headers/
#>
#blank line
#blank line
function get-gud {
    <#
    .synopsis
    it's what you're here for.
    .description
    don't need much more detail than that!
    #>
    function body
}
```

Writing out documentation is always recommended, even when adventuring solo. Get in the habit of incorporating help comments in your scripts or functions. Future you will be eternally greatful.

_Get-Scripting_

[^1]:Comment-Based Help Keywords. [Microsoft](https://learn.microsoft.com/en-us/powershell/scripting/developer/help/comment-based-help-keywords?view=powershell-7.4)