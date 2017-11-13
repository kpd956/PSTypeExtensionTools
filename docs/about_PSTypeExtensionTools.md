﻿# PSTypeExtensionTools
## about_PSTypeExtensionTools

# SHORT DESCRIPTION
This PowerShell module contains commands that make it easier to work with type
extensions. Many of these commands are wrappers for built-in tools like 
Get-TypeData or Update-TypeData. The commands in this module simplfy the 
process of finding, creating, exporting and importing type extensions.

# LONG DESCRIPTION
Let's say you want to update a number object, but you have no idea what the 
type name is. Once you have read help for the commands in this module you 
could run a PowerShell command like this:


      PS C:\> 123 | Get-PSType | 
      Add-PSTypeExtension -MemberType ScriptProperty -MemberName SquareRoot `
      -Value { [math]::Sqrt($this)}


Use $this to reference the object instead of $_.  Now you can get the new property.


      PS C:\> $x = 123
      PS C:\> $x.SquareRoot
      11.0905365064094


Once you know the type name you can add other type extensions.

      Add-PSTypeExtension -TypeName system.int32 -MemberType ScriptProperty -MemberName Squared -value { $this*$this}
      Add-PSTypeExtension -TypeName system.int32 -MemberType ScriptProperty -MemberName Cubed -value { [math]::Pow($this,3)}
      Add-PSTypeExtension -TypeName system.int32 -MemberType ScriptProperty -MemberName Value -value { $this}
      Add-PSTypeExtension -TypeName system.int32 -MemberType ScriptMethod -MemberName GetPercent -value {Param([int32]$Total,[int32]$Round=2) [math]::Round(($this/$total)*100,$round)}


Here's how it might look:

      PS C:\> $x = 38
      PS C:\> $x | select *

            SquareRoot Squared Cubed Value
            ---------- ------- ----- -----
      6.16441400296898    1444 54872    38

      PS C:\> $x.GetPercent(50)
      76
      PS C:\> $x.GetPercent(100)
      38
      PS C:\> $x.GetPercent(110,4)
      34.5455

To see what has been defined you can use Get-PSTypeExtension. You can choose to see all extensions or selected ones by member name.

      PS C:\> Get-PSTypeExtension system.int32 -all

      MemberType     MemberName Value                                               TypeName    
      ----------     ---------- -----                                               --------    
      ScriptProperty SquareRoot  [math]::Sqrt($this)                                System.Int32
      ScriptProperty Squared     $this*$this                                        System.Int32
      ScriptProperty Cubed       [math]::Pow($this,3)                               System.Int32
      ScriptProperty Value       $this                                              System.Int32
      ScriptMethod   GetPercent Param([int32]$Total,[int32]$Round=2) [math]::Ro...  System.Int32

If you always want these extensions you would have to put the commands into your PowerShell profile script. Or you can export the extensions to a json or xml file. You can either export all members or selected ones which is helpful if you are extending a type that already has type extensions from PowerShell.

      Get-PSTypeExtension system.int32 -all | 
      Export-PSTypeExtension -TypeName system.int32 -Path c:\work\int32-types.json

In your PowerShell profile script you can then re-import the type extension definitions.

      Import-PSTypeExtension -Path C:\work\int32-types.json

A number of sample files with type extensions can be found in this modules Samples folder or in the GitHub repository at https://github.com/jdhitsolutions/PSTypeExtensionTools/tree/master/samples.

# NOTE
PowerShell type extensions only last for the duration of your PowerShell session. If you make a mistake that is causing problems simply restart PowerShell or use the Remove-TypeData cmdlet.
# TROUBLESHOOTING NOTE
Don't try to append or manually update an export file. If you have changes simply re-run the export command and generate the file anew. 


Remember to use $this to reference the object and NOT $_.


Remember to enclose scriptblocks in {}.

# SEE ALSO
Add-PSTypeExtension

Export-PSTypeExtension

Get-PSType

Get-PSTypeExtension

Import-PSTypeExtension


This project was first described at http://jdhitsolutions.com/blog/powershell/5777/a-powershell-module-for-your-type-extensions

# KEYWORDS
typedata
typeextension