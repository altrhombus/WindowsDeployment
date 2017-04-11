# StartLayout

## Synopsis
Customize your Windows 10 Start Menu and taskbar layout

## Description
This lets you customize your Windows 10 start menu and taskbar layout to match your enterprise standard. You'll design the start menu yourself, then do a little XML editing, then you'll be set!

## Example
### To generate your own XML file
To use a different startLayout.xml than what's in this repository, simply drag and drop and organize tiles and applications on the start menu. Just remember that any shortcuts you add need to already exist in the Start Menu folder! (Internet Explorer is an example of one that isn't, so we'll do a little workaround in a bit).

Once you have your start menu all sized up and looking nice, run the following command:

    Export-StartLayout -Path .\startLayout.xml

Now, if you don't want to modify your taskbar, you can skip this section and go right into deploying in ConfigMgr.

To modify your taskbar, you'll have to edit the XML file manually. There's a couple things you need to add:
 * The <LayoutModificationTemplate> needs to have 'xmlns:taskbar="http://schemas.microsoft.com/Start/2014/TaskbarLayout"' added. See the startLayout.xml here as a reference (I organized it a little bit so it wasn't a single line, and I added the ?xml element)
 * Now scroll down to the closing </DefaultLayoutOverride> element. After this, you'll enter your taskbar modifications

    <CustomTaskbarLayoutCollection PinListPlacement="Replace">  
    <defaultlayout:TaskbarLayout>  
    <taskbar:TaskbarPinList>  
    <taskbar:DesktopApp DesktopApplicationLinkPath="%APPDATA%\Microsoft\Windows\Start Menu\Programs\Accessories\Internet Explorer.lnk" />
    <taskbar:DesktopApp DesktopApplicationLinkPath="%APPDATA%\Microsoft\Windows\Start Menu\Programs\System Tools\File Explorer.lnk" />
    <taskbar:DesktopApp DesktopApplicationLinkPath="%ALLUSERSPROFILE%\Microsoft\Windows\Start Menu\Programs\Outlook 2016.lnk" />
    <taskbar:DesktopApp DesktopApplicationLinkPath="%ALLUSERSPROFILE%\Microsoft\Windows\Start Menu\Programs\Skype for Business 2016.lnk" />
    <taskbar:DesktopApp DesktopApplicationLinkPath="%ALLUSERSPROFILE%\Microsoft\Windows\Start Menu\Programs\OneNote 2016.lnk" />
    </taskbar:TaskbarPinList>  
    </defaultlayout:TaskbarLayout>  
    </CustomTaskbarLayoutCollection> 

This layout in particular removes all icons and replaces it with IE, Explorer, Outlook, Skype for Business, and OneNote.

### To use in ConfigMgr during OSD

Create a package for modifying your start menu layout, and specify a source folder (putting the XML file in it).

While you're in that folder, right-click and create a new shortcut to C:\Program Files (x86)\Internet Explorer\iexplore.exe and name it Internet Explorer (like the one included here).

We need this shortcut because there's no shared IE shortcut in the start menu (they're created at user logon). The only way we can add IE to the start menu is to have a shortcut sitting in the Start Menu folder accessible to everyone.

You'll need to add two task sequence steps:

Add a "Run Command Line" step in your OSD task sequence (anytime after the base Windows image has been applied)
 * Name = Install Shared Internet Explorer Shortcut
 * Command line = cmd /c copy ".\Internet Explorer.lnk" "%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs\Accessories\"
 * Check the Package checkbox and specify the package that has your StartLayout and IE.lnk files
 * Check Time-out (minutes) -- just in case something goes wrong, you don't want this to tie up your task sequence!

Add a "Run Command Line" step in your OSD task sequence (anytime after the base Windows image has been applied)
 * Name = Apply Default Start Menu Layout
 * Command line = %SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe -executionpolicy bypass import-startlayout -layoutpath .\startLayout.xml -mountpath %systemdrive%\
 * Check the Package checkbox and specify the package that has your StartLayout and IE.lnk files
 * Check Time-out (minutes) -- just in case something goes wrong, you don't want this to tie up your task sequence!

## Notes
The [Configure Windows 10 taskbar](https://technet.microsoft.com/itpro/windows/configure/configure-windows-10-taskbar) TechNet article was extremely helpful for this process.

Also see the great article by Jörgen Nilsson about the IE trick [at his blog](http://ccmexec.com/2015/09/customizing-the-windows-10-start-menu-and-add-ie-shortcut-during-osd/).

This has been tested on Windows 10 1703.

Made with ❤️ in MKE