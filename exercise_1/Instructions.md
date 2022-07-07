# Demo 1

The first step to plugin development mastery is obviously just to create a basic boilerplate plugin.  This is so easy you can do it in your sleep.
In fact, there is an excellent plugin for creating plugins.  This plugin requires exactly zero coding on your part.

## What the Plugin should do

- The plugin should open a QDialog with a ok/close button set.
- The plugin should have a basic plug icon in the QGIS Toolbar.

## Basic Instructions

- Open QGIS and install the Plugin Builder plugin. 
- It is not necessary for this exercise but while you are here also install the Plugin Reloader plugin, which is essential.
- Open the Plugin Builder plugin and follow the wizard.  Remember to enter Demo1 for the class name and demo_1 as the module name
- Try to give valid inputs for all fields and pay attention to where the inputs show up in the finished plugin.
- The finished plugin will contain a README.txt.  Follow these instructions to install the plugin.

## Caveats

- The README.txt at time of writing is a bit obsolete for Windows users.  
    - OSGeo Network installer used to include the Bash Terminal MSYS.  Using this you had access to the make utility.  This is no longer the case.  That means the included makefile is useless unless you have a cygwin installation of your own with a make utility.  In my productive environment I don't have it, so I've had to write my owh little install script.  You never have to do much more than selectively copy files from a to b, so bringing in a proper build system isn't much worth it.  This is a relatively recent problem, so I'm still working on a final solution for myself. As a quick solution just copy the whole directory and forget about make. 
    - Use OSGeo Shell to access pyrcc5

## If you followed the Basic instructions

- QGIS has a yellow plug toolbar icon.
- If you click it then an empty dialog opens.
- The plugin directory is demo_1
- The plugin directory includes a file named demo_1.py
- in demo_1.py, there is a class called Demo1

If you found this exercise to be mindnumbingly easy, that is because you are great (go you!) and because Gary Sherman wrote an excellent Plugin Builder.  Do you remember when code was hard?  All hail the PyQGIS Developers and donate to their projects!