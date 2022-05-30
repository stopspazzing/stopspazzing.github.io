---
layout: post
title: 'Log off current user from other computers on domain on sign in'
date: 2019-03-16 12:21:39 -0700
tags: windows-server active-directory rsat powershell
color: rgb(255,90,90)
cover: '../assets/active-directory.png'
subtitle: ''
---
![active directory logo](/assets/active-directory.png)

# The Why

Needed a script for a client to log off same username that was just logged in, off any other computers on a domain. Client didn’t want to use rdp licenses to save money cause was only a few people using many computers.

After long searching and reading new documentation, seems prior to Windows 10, you couldn’t do it properly. Also prior to Windows 10 v1809 you would have to install RSAT to get the current command to even work, which is annoying because there is no reason this should be built into Windows by default and the script should be a simple group policy, not requiring powershell script with Windows 10 to get it working.

Thankfully, as long as you are running Windows 10 v1809 or higher, this command will work out of the box if including via group policy to start once logged in.

## Windows 8, Windows 8.1, Windows 10
All you have to do is download and install RSAT ([Windows 8][w8], [Windows 8.1][w8.1], [Windows 10][w10]). The installation enables all tools by default, and you also don’t have to import the module. You can use the AD module right away after you install RSAT.

## Windows Server 2012, Windows Server 2012 R2, Windows Server 2016
As on Windows Server 2008 R2, the AD module is already installed on domain controllers on Windows Server 2012, Windows Server 2012 R2, and Windows Server 2016. On member servers, you can add the module as a feature in Server Manager.

1. Start **Server Manager**.
2. Click **Manage** > **Add Roles and Features**.
3. Click **Next** until you reach **Features**.
4. Enable **Active Directory module for Windows PowerShell** in **Remote Server Administration Tools** > **Role Administration Tools** > **AD DS and AD LDS Tools**.

Alternatively, you can install the module from a PowerShell console:

```powershell
Add-WindowsFeature RSAT-AD-PowerShell
```

There’s no need to import the Server Manager module first, as on Windows Server 2008 R2. You also don’t have to import the AD module after the installation.

## The Script

Well without further ado, here is the powershell script I came up with:

```powershell
<# Copyright 2019 Jeremy Zimmerman (stopspazzing.com) - MIT License: https://opensource.org/licenses/MIT #>
import-module activedirectory
$notcomputer = $env:COMPUTERNAME
$cmd = {
         param($a)
         $session = ((& quser /server:$a | ? { $_ -match $env:username }) -split ' +')[2]
         logoff $session /server:$a
}
$computers = (Get-ADComputer -Filter *).Name 
ForEach ($computer in $computers)
{
    if ($computer -ne $notcomputer)
    {
        Start-Job -ScriptBlock $cmd -ArgumentList $computer
    }
}
Start-Sleep -Seconds 30
Get-Job | Stop-Job
Get-Job | Remove-Job
```

Let me breakdown the script for easier understanding.

## The Breakdown

```powershell
import-module activedirectory
```
The first line imports the needed module activedirectory; This line isn’t necessarily needed but we added it to cover all bases.

```powershell
$notcomputer = $env:COMPUTERNAME
```
This sets the current computer the user is logging into as a variable to be used later.

```powershell
$cmd = {
         param($a)
         $session = ((& quser /server:$a | ? { $_ -match $env:username }) -split ' +')[2]
         logoff $session /server:$a
}
```
We prep the code that will run as a command when logging into another computer. The current user that just logged in is saved and then sends the log off command to the other computers in the active directory.

```powershell
$computers = (Get-ADComputer -Filter *).Name 
ForEach ($computer in $computers)
{
    if ($computer -ne $notcomputer)
    {
        Start-Job -ScriptBlock $cmd -ArgumentList $computer
    }
}
```
First variable gets all the computers connected to the domain. Then we run a command that queries all computers and checks our previously set current computer that the user is logging into, excluding it from the Start-Job command which queues up the command we set earlier.

```powershell
Start-Sleep -Seconds 30
Get-Job | Stop-Job
Get-Job | Remove-Job
```
Finally, we found that slow computers need some time to process the command to log off, so we set a cool-down timer of 30 seconds to get all jobs then stop and remove them, otherwise they will run for about 5 minutes before timing out. Rather clean up any jobs that failed or aren’t valid for whatever reason.

I'm including a download link to make everyone's life easier.

[Download Zip][dlz] | [Download Powershell][dlp]

The final thing that needs to be done is add this as a logon script. This should be a self explainitory GPO policy for users. 

[w8]: https://www.microsoft.com/en-us/download/details.aspx?id=28972
[w8.1]: https://www.microsoft.com/en-us/download/details.aspx?id=39296
[w10]: https://www.microsoft.com/en-us/download/details.aspx?id=45520
[dlz]: https://github.com/stopspazzing/AD-log-off-your-other-sign-ins/archive/master.zip
[dlp]: https://raw.githubusercontent.com/stopspazzing/AD-log-off-your-other-sign-ins/master/logoff-other-sessions.ps1