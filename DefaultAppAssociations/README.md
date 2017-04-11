# DefaultAppAssociations

## Synopsis
Example XML file for assigning default application associations for a Windows 10 deployment.

## Description
This XML file is used during OSD in conjunction with dism.exe to set default application associations for a Windows 10 deployment.

This file in particular just sets the default browser to Internet Explorer and sets the default PDF reader to Adobe Acrobat Reader DC.

## Example
### To generate your own XML file
On a freshly-installed machine, go to Control Panel > Programs > Default Programs and set your preferred application defaults.

You can use "Set your default programs" or "Associate a file type or protocol with a program" -- the second option is useful if you have specific requirements.

Once you've got everything set the way you want, run the following command (modifying the path at your leisure):

    dism /Online /Export-DefaultAppAssociations:C:\AppAssoc.xml

### To use in ConfigMgr during OSD

Create a package for setting Default Application Associations, and specify a source folder (putting this XML file in it).

Now add a "Run Command Line" step in your OSD task sequence (after any dependent applications have been installed)
 * Name = Set Default Application Associations
 * Command line = %SystemRoot%\system32\dism.exe /online /Import-DefaultAppAssociations:.\AppAssoc.xml
 * Check the Package checkbox and specify the package that has your AppAssoc.xml file
 * Check Time-out (minutes) -- just in case something goes wrong, you don't want this to tie up your task sequence!

## Notes
Here's some reference materials I've found that were useful:

[http://deploymentresearch.com/Research/Post/507/Setting-Internet-Explorer-as-your-default-browser-in-Windows-10-during-OS-Deployment](http://deploymentresearch.com/Research/Post/507/Setting-Internet-Explorer-as-your-default-browser-in-Windows-10-during-OS-Deployment)

This has been tested on Windows 10 1703.

Made with ❤️ in MKE