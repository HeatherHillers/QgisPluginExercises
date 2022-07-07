# Demo 1 Discussion

## A look at the Demo 1 plugin

If you look at the demo_1 plugin, there is a lot of stuff in there you maybe don't need, and only a couple things that you really care about.  Some stuff is only necessary if you are writing a professional plugin that you intend to publish to the world. All of my stuff is custom for my company, so I end up ripping a lot of stuff out.  So, first lets look at these files.

## help directory
- theoretically you could set up some real slick professional sphinx documentation here.  For a professional cool plugin, you should do that.  Or you can delete this directory.

## i18n directory
- if you ever intend to give this plugin to people speaking another language, you should use this directory to store translations of all the strings in your user interface.  You will use the Qt Translator to do this.  I don't need to use translation, so you will have to learn about this somewhere else.  I delete this directory.

## scripts directory
- if you are running in a Windows environment, you can delete this directory.  

## test directory
- This is test directory contains boilerplate unittest classes that are meant to be run from the makefile.  Getting these tests to run in a Windows environment is possible but tricky, and obviously won't work from the makefile if you don't have make.  You will need to make a .bat to run them instead.  And maybe use pytest if you want.  Leave this directory for now, but don't delete it.  You will need to mess with the tests a bit to get them working but they will work and they are useful.

## __init__.py

Contains a bit of system code to help qgis load the plugin.  Leave this file alone unless you have to change the plugin's class name.
If you do change the name of the main module from demo_1.py or if you change the class name from Demo1
you have to adjust the code below accordingly.
```
def classFactory(iface):  # pylint: disable=invalid-name
    """Load Demo1 class from file Demo1.

    :param iface: A QGIS interface instance.
    :type iface: QgsInterface
    """
    #
    from .demo_1 import Demo1
    return Demo1(iface)
```

## resources.qrc, resources.py

Skipping for a moment to resources.qrc, this is a list of png files used for icons and such in the user interface.  The resources.qrc file is compiled using pyrcc5 resulting in the resources.py file.  Have a look at the makefile to see how this is done.  So, just to reiterate, resources.py is a generated file.  You don't have to look at it.  If you change icon.png so it doesn't look like a yellow plug anymore, or if you add an icon, you have to add the new icon to resources.qrc if necessary and in all cases regenerate resources.py.

## demo_1.py

This is important.  This contains all the code in this simple plugin and is the main module for the qgis plugin.  When you start off, you will probably stuff all your code into the run function of this module.  But soon you will figure out that you should actually keep this module as simple as possible and break out most of your code into the dialog class and other classes.  
Lets look closer at the code:
```
...

# Initialize Qt resources from file resources.py
from .resources import *

...

```
The import from resources looks like dead code but it is not.  This imports the compiled icons.  Later, when QIcons are generated in the code, these imported images will be used.  If you delete this line, you won't have icons.
You do have the option of getting rid of the resource files and the pyrcc5 compilation and this import if you load svg files directly to the QIcons.  I started doing this recently and I find it makes my life easier.

```
class Demo1:
    """QGIS Plugin Implementation."""

    def __init__(self, iface):
```

Here is your main class named Demo1.  Important to note, is that this constructor runs at QGIS start up.  In my productive environment, we have a set of plugins that are installed in the main plugin directory under OSGeo4W.  The users may or may not have the installed plugins activated in their profile.  Regardless, the init function will be called.  Whatever you may think about my universe, you don't want to wait for code to execute or worse get a login screen or some sort of interaction on a plugin that you don't have turned on.  So by all means keep this function as empty as possible!   Leave the boiler plate code in there, of course.  I generally rip out the code for translation, but thats me.  
```
    def tr(self, message):
...

    def add_action(
...

```

Tr translates strings, and add_action is a function for creating entries to the toolbars and menus.  add_action is called from init_gui.  You won't have to mess with them.  Just leave them be.

```
    def initGui(self):
        icon_path = ':/plugins/demo_1/icon.png'
        self.add_action(
            icon_path,
            text=self.tr(u'Demo 1'),
            callback=self.run,
            parent=self.iface.mainWindow())

        # will be set False in run()
        self.first_start = True
    
    def unload(self):
    ...
```

initGui runs when the main qgis window loads, and it makes calls to add the plugin to the toolbars and menus.  Unlike the init, it only runs if the plugin is activated.  So if you need a new toolbar button, this is where you put it.  Otherwise, keep this function empty like the constructor.  Otherwise you are making your QGIS load slow.  Keep any code from running until the user actually presses a button.  Do note that the callback parameter is set to self.run, meaning that the run function will be called when the user clicks the toolbar icon.
unload handles toolbar changes when the plugin is unloaded.  You usually don't have to muck with it.
```
    def run(self):
        """Run method that performs all the real work"""

        # Create the dialog with elements (after translation) and keep reference
        # Only create GUI ONCE in callback, so that it will only load when the plugin is started
        if self.first_start == True:
            self.first_start = False
            self.dlg = Demo1Dialog()

        # show the dialog
        self.dlg.show()
        # Run the dialog event loop
        result = self.dlg.exec_()
        # See if OK was pressed
        if result:
            # Do something useful here - delete the line containing pass and
            # substitute with your code.
            pass
```

This is really the only function that you care about at first.  It runs when the toolbar button is clicked, because it is set as the callback in initGui.
All this is doing is run the dialog in demo_1_dialog.py, wait for it to be closed, and then provide a block to do stuff in.  Just read the nice comments in the function.
As I said, beginners will try to stuff all their code in the result block, but for plugins that aren't massively simple, you will want to break your code out to the dialog class or to its own module and leave this class nice and simple.

## demo_1_dialog.py, demo_1_dialog.ui

This file is the code that loads and runs the QDialog itself. The ui file can be edited using Qt Designer and is loaded by the .py file 
```
FORM_CLASS, _ = uic.loadUiType(os.path.join(
    os.path.dirname(__file__), 'demo_1_dialog_base.ui'))


class Demo1Dialog(QtWidgets.QDialog, FORM_CLASS):
    def __init__(self, parent=None):
        """Constructor."""
        super(Demo1Dialog, self).__init__(parent)
        # Set up the user interface from Designer through FORM_CLASS.
        # After self.setupUi() you can access any designer object by doing
        # self.<objectname>, and you can use autoconnect slots - see
        # http://qt-project.org/doc/qt-4.8/designer-using-a-ui-file.html
        # #widgets-and-dialogs-with-auto-connect
        self.setupUi(self)
```

The uic call is what loads the ui file, and returns a FORM_CLASS class that the Demo1Dialog inherits from.  After the setupUI call, the class will know about any widgets defined in the ui.  Don't delete this line, or forget it when making a new dialog or widget class.  

## icon.png

Is your yellow plug button.  you can replace it with something you like.  I use inkscape to make my icons.

## Makefile
As discussed in the instructions.md, if you don't have make you have to find an alternative way of automatically deploying the plugin, unless you just want to keep copying and pasting the whole directory.
DO NOT: Do not Do not solve your deploy problem by just developing in your installation directory.  You are better than that.  Even if you can't be bothered to test or write documentation, you are better than that.
Not having make is a recent development in my life.  I changed my makefiles to json files and made a script to deploy them, which I don't have on me now but will upload later if you need it.

## metadata.txt
This is the documentation that appears in the plugin manager.  It has to be deployed, or the plugin will not be recognised by qgis.  Mostly you can leave this be.

## pb_tool.cfg, plugin_upload.py

These things you need if you are developing for the QGIS Plugin Repository.  That's not me.

## pylintrc

I think this thing is for checking your code for errors and PEP8 Problems.  I use Visual Studio or PyCharm for that.  So, I don't know.
I do know it is massively important to keep your code clean, even as the rest of the world falls apart.  So whatever tool you use, definitely use it.

# README.txt, README.html

These files contain Plugin Builders helpful hints and instructions for deploying your code.  Do with them what you will.


