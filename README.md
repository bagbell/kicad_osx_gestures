# KiCad_osx_gestures

KiCad nightly release doesn't support two fingers pan on MacBook Pro makes it very difficult to use. Actually, KiCad has everything we want, just not in the release. To enable these good features we need compile the source code by ourself. 

###1. pre-requirements
* XCode Tools
* bzr       - Bazaar version control system
* CMake     - Cross-platform make
* GLEW      - The OpenGL Extension Wrangler Library
* cairo     - 2D graphics library
* wxWidgets - Cross-Platform GUI Library
   OR
  wxPython  - A blending of the wxWidgets C++ class library with the Python
              programming language
* SWIG      - Interface compiler (only needed for scripting/wxPython builds)

bzr, CMake, GLEW, cairo, SWIG can be installed by macport. 
wxWidgets or wxPython has to be downloaded and compiled manually.

###2. get source
* `bzr branch lp:~gcorral/kicad/osx-trackpad-gestures`
* `cd osx-trackpad-gestures`
* `bzr checkout`

this will create ~~osx-trackpad-gestures~~ kicad folder in current folder, we call it $KICAD_DIR folder.


###3. compile wxWidgets
* download source from http://www.wxwidgets.org/downloads/, version 3.0.2.
* unpack it to the $KICAD_DIR/wx-src folder.
* open $KICAD_DIR/scripts/osx_build_wx.sh, add this after line 146:
	
	`doPatch "$1" "$3/patches/wxwidgets-3.0.2_macosx_yosemite.patch"`
	
	Remove "wxwidgets-3.0.2_macosx_retina_opengl.patch", since I don't have retina display.
	
	Make sure the "wxwidgets-3.0.2_macosx_magnify_event.patch" will apply.
 	
* in $KICAD_DIR folder.
 
 	`sh ./scripts/osx_build_wx.sh wx-src wx-bin . 10.10 "-j4"`
 
 	this will take a while.
 	
 
###4. compile kicad
* `mkdir build`
* `cd build`
* <code>cmake ../ 
      -DCMAKE_C_COMPILER=clang 
      -DCMAKE_CXX_COMPILER=clang++ 
      -DCMAKE_OSX_DEPLOYMENT_TARGET=10.10 
      -DwxWidgets_CONFIG_EXECUTABLE=../wx-bin/bin/wx-config 
      -DKICAD_SCRIPTING=OFF 
      -DKICAD_SCRIPTING_MODULES=OFF 
      -DKICAD_SCRIPTING_WXPYTHON=OFF 
      -DCMAKE_INSTALL_PREFIX=../bin 
      -DCMAKE_BUILD_TYPE=Release</code>
            
* `make`
      
	have a cup of coffee, do something else, it takes loooooooooooong time.

* `make install`

###5. know issue
* KiCad use internal boost. It seems that KiCad was *compiled* with bundled boost and then *linked* with system installed boost, and it cause pcbnew crash everytime it starts.
* WORK AROUND, remove system boost.
	
	- uninstall boost from macport: `sudo port -f uninstall boost`
	- `wget http://downloads.sourceforge.net/project/boost/boost/1.54.0/boost_1_54_0.tar.bz2`
	- `cp boost_1_54_0.tar.bz2 $KICAD_DIR/.downloads-by-cmake`
	- AFTER compiled kicad, reinstall boost from macport: 
	
		`sudo port install boost`
