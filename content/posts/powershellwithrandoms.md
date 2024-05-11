+++
authors = "David Ford"
title = "PowerShell with Randoms: Passwords"
date = "2024-05-10T23:12:56-04:00"
description = "Having fun with randomness."
tags = [
    "powershell",
]
categories = [
    "tutorial",
]
+++

<!--Intro-->

Playing with Randoms can have its perks. This article goes over implementing a random object generator into your PowerShell scripts. 

The PowerShell version that we'll be using in this article:

```PowerShell
PS /home/fordablescripts> $PSVersionTable

Name                           Value
----                           -----
PSVersion                      7.4.2
PSEdition                      Core
GitCommitId                    7.4.2
OS                             Ubuntu 24.04 LTS
Platform                       Unix
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
WSManStackVersion              3.0
```

<!--Into the Rabbit Hole-->

## How is a Raven like a Writing Desk?

Oxford defines Randomness as _the quality or state of lacking a pattern or principle of organization._ PowerShell has it's own builtin cmdlet to assist in implementing randomness within a script. A great example of implementing randomness is a password generator!

Let's go over `Get-Random` and start with the information at hand:

```PowerShell
NAME
    Get-Random
    
SYNOPSIS
    Gets a random number, or selects objects randomly from a collection.
    
DESCRIPTION
    The `Get-Random` cmdlet gets a randomly selected number. If you submit a 
    collection of objects to `Get-Random`, it gets one or more randomly 
    selected objects from the collection.
    
    Without parameters or input, a `Get-Random` command returns a randomly 
    selected 32-bit unsigned integer between 0 (zero) and `[int32]::MaxValue`.
    
    You can use the parameters of `Get-Random` to specify the minimum and 
    maximum values, the number of objects returned from a collection, or a 
    seed number.
    
    > [!CAUTION] > `Get-Random` doesn't ensure cryptographically secure 
    randomness. The seed value is used for the > current command and for all 
    subsequent `Get-Random` commands in the current session until you use > 
    SetSeed again or close the session. You can't reset the seed to its 
    default value. > > Deliberately setting the seed results in non-random, 
    repeatable behavior. It should only be used > when trying to reproduce 
    behavior, such as when debugging or analyzing a script that includes > 
    `Get-Random` commands. Be aware that the seed value could be set by other 
    code in the same > session, such as an imported module. > > PowerShell 7.4 
    includes `Get-SecureRandom`, which ensures cryptographically secure 
    randomness.
```
Did you catch the Caution paragraph?  

`Get-Random doesn't ensure cryptographically secure randomness.`

At this point, a new question emerges; _Do I need cryptographically secure randomness and instead utilize Get-SecureRandom for my script?_ That will depend on your use case. `Get-Random` by default uses the system tick count (in milliseconds) of the elapsed since the system started as a seed number from 0 to [Int32]::MaxValue (Roughly 2 Billion). If you have a script that runs on a schedule, then the chances of generating that same seed number increases which would mean you could create duplicate values.

`Get-SecureRandom` on the other hand, with PowerShell 7.4, uses the RandomNumberGenerator Class to ensure an exclusive integer and replaces the previously used "System.Security.Cryptography.RNGCryptoServiceProvider" .NET class.

Matt Graeber wrote a post back in 2014 that covered the effectiveness of `Get-Random` in comparison to using `System.Security.Cryptography.RNGCryptoServiceProvider` .NET class.

Please visit Matt's post for more information on those differences:  
  https://powershellmagazine.com/2014/07/28/testing-the-effectiveness-of-get-random/


For now, let's use `Get-SecureRandom` and implement it into a simple XKCD passphrase generator. For this Passphrase generator, we'll need a short script and a wordlist that you can find easily enough online. For this example, we're using a wordlist that's separating words by newline or `` `n ``.

## Supercalifragilisticexpialidocious!

Once we have our wordlist, let's start on the script. In this example, I'm creating a Function and setting parameters for the passphrase requirements. 

```PowerShell
Function Get-Passphrase {
    Param(
        [int]$words = 4,
        [string]$delimiter = " ",
        [switch]$FirstLetterUpper  
    )
```
The `$words` parameter denotes the number of words we want with a default of 4. `$delimiter` will be our separator between words, and `$FirstLetterUpper` capitalizes the first letter of each word.  

```PowerShell
    $password = $null
    $wordlist = get-content "Path\To\WordList.txt"
    
    switch($words) {
        {$_ -ge 6 } { throw "Word parameter cannot be greater or equal 6." }
        5 { $range = (3,4) }
        4 { $range = (4,5) }
        3 { $range = (5,6) }
        2 { $range = (7,8) }
        {$_ -le 1 } { throw "Word parameter cannot be less or equal 1." }
    }
    $list = $wordlist -split "`n" | ForEach-Object { 
        New-Object PSObject -Property @{
            Value = $_.ToLower()
            Length = $_.length
            }
            } | Where-Object {
                ($_.Length -eq ($range[0] + 1)) -or ($_.Length -eq ($range[1] + 1))
                }
```
Here we're setting the `$password` to `$null` for now and getting the `$wordlist` content from the saved file path. For some extra sillies, we'll use a switch for `$words` so that the number of `$words` set will also determine the length of characters of those words. For example, a `-words 2` password will be 8 or 9 characters in length per word and a `-words 5` passphrase will be 4 to 5 characters per word. This helps to ensure that the total number of characters for the passphrase stays within a normal password length.

```PowerShell
1..$words | ForEach-Object {
        $part =  (Get-SecureRandom $list).Value.Trim()

        if($FirstLetterUpper) {
             $password += ((Get-Culture).TextInfo).ToTitleCase($part)
        } else {
            $password += $part
        }

        if($_ -lt $words){ 
            $password += $delimiter 
        }
    }

    return $password
```
The script now sets a range between 1 and the set `$words` count. Then for each, we use `Get-SecureRandom` to select a random word from the wordlist. `if($FirstLetterUpper)` parameter is used, the first letter of each word is capitalized using `Get-Culture`. You can also swap this out for `$part.ToUpper().SubString(0,1) + $part.ToLower().SubString(1)`. After, we add a delimiter and then call the `$password` to finish.

All together now:

```PowerShell
Function Get-Passphrase {
    Param(
        [int]$words = 4,
        [string]$delimiter = " ",
        [switch]$FirstLetterUpper  
    )
    $password = $Null
    $wordlist = get-content "Path\To\WordList.txt"
    switch($words) {
        {$_ -ge 6 } { throw "Word parameter cannot be greater or equal 6." }
        5 { $range = (3,4) }
        4 { $range = (4,5) }
        3 { $range = (5,6) }
        2 { $range = (7,8) }
        {$_ -le 1 } { throw "Word parameter cannot be less or equal 1." }
    }
    $list = $wordlist -split "`n" | ForEach-Object { 
        New-Object PSObject -Property @{
            Value = $_.ToLower()
            Length=$_.length
        }
        } | Where-Object {
            ($_.Length -eq ($range[0] + 1)) -or ($_.Length -eq ($range[1] + 1))
            }
    1..$words | ForEach-Object {
        $part =  (Get-SecureRandom $list).Value.Trim()
        if($FirstLetterUpper) {
             $password += ((Get-Culture).TextInfo).ToTitleCase($part)
        } else {
            $password += $part
        }
        if($_ -lt $words){ 
            $password += $delimiter 
        }
    }
    return $password
}
```
Here is what an example call would look like:
```Powershell
PS /home/fordablescripts> get-passphrase -words 5 -delimiter "-" -firstletterupper  
Fold-Jawed-Style-Plane-Shove
```
## Conclusion

PowerShell can be a great tool to create random generating scripts and with PowerShell 7.4, `Get-SecureRandom` takes that security a step further. Of course, I recommend implementing an MFA service for truly secure access. Have fun coding!
