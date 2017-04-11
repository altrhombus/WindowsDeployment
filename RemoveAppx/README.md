# RemoveAppx

## Synopsis
Remove Unnecessary AppX Packages

## Description
This removes AppX packages from a deployment that you don't want in your final image. I didn't write the RemoveApps.ps1 script, but it's here for reference (see the Notes section for credit where credit is due!)

## Example
### To generate your own XML file
The short version of how to use is: place a RemoveApps.xml file in the same folder as RemoveApps.ps1. When you run RemoveApps.ps1, it will remove all apps that you specify in the XML file.

To get a list of all possible appx applications on a computer, run:

    Get-AppxProvisionedPackage -Online | Out-GridView

### To use in ConfigMgr during OSD

Create a package for removing AppX Packages, and specify a source folder (putting both files in it). Then you'll need to add two task sequence steps:

Add a "Run Command Line" step in your OSD task sequence (anytime after the base Windows image has been applied)
 * Name = Cleanup Unnecessary AppX Packages
 * Command line = %SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -executionpolicy bypass -file .\RemoveApps.ps1
 * Check the Package checkbox and specify the package that has your RemoveApps files
 * Check Time-out (minutes) -- just in case something goes wrong, you don't want this to tie up your task sequence!

Now add another "Run Command Line" step in your OSD task sequence, right after the one we just created. This step runs a registry edit to prevent AppX packages from automagically reinstalling as soon as the computer gets an internet connection.
 * Name = Disable Windows Consumer Features
 * Command line = reg add HKLM\Software\Policies\Microsoft\Windows\CloudContent /v DisableWindowsConsumerFeatures /t REG_DWORD /d 1 /f
 * Check Time-out (minutes) -- just in case something goes wrong, you don't want this to tie up your task sequence!

## Notes
I take no credit for the RemoveApps.ps1 file! That genius belongs to Michael Niehaus. Check out the his post where he talks about the script, [Removing Windows 10 in-box apps during a task sequence](https://blogs.technet.microsoft.com/mniehaus/2015/11/11/removing-windows-10-in-box-apps-during-a-task-sequence/)

This has been tested on Windows 10 1703.

Made with ❤️ in MKE