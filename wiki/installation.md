# installing kivyMaps

There is no official release yet. 
However, kivy is distributed as a single archive file, including all dependencies (e.g. a full Python installation). 
You can copy your version of kivyMaps into that environment. 

1.  Get kivy from <http://www.kivy.org>

2.  To get the latest development version of kivyMaps via git, use:

    `git clone <git://github.com/relet/kivy.git/kvMaps>`
 
    This will create a folder kvMaps in your current working directory.

    NOTE: the example applications in the kvMaps folder use a sidepanel.py widget that is not yet in the main distribution of kivy. 
    You can find it here: <https://github.com/relet/kivy/blob/master/kivy/uix/sidepanel.py> - copy this into your own `kivy/uix` folder.

3.  Run kivyMaps like any other kivy application within the kivy environment

    e.g. `kivy.bat kvMaps.py` on Windows

4.  Read the [configuration](configuration.html) guide to set up your own maps and services in kivyMaps.

See also: [Installation on android](android.html)
