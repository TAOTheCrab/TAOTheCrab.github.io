---
layout: "post"
title: "Compiling OpenMW on macOS 10.12"
---
So I wanted to change the fullscreen behavior of OpenMW to use SDL's fullscreen desktop rather than using exclusive, second-monitor-ruining fullscreen. Out of curiosity, I threw in a one-line change and attempted to compile. Then I made it harder for myself, and now here I am documenting what I did.

Following the [official build instructions](https://wiki.openmw.org/index.php?title=Development_Environment_Setup#macOS),
I ran into various problems with linking, which after some fiddling ended up stemming from my earlier choice to install the latest version of Qt5 rather than what the instructions called for.
Turns out the instructions were right, newer Qt versions needed C++11, which doesn't quite mesh with the provided instructions.

DISCLAIMER: I haven't run into problems with this build method yet, but I haven't tested it thoroughly either. So, FAIR WARNING.

This is what I did to build it:

* Installed the dependencies with Homebrew instead of using the OpenMW deps repo the instructions use
    * MyGUI needs to be downloaded separately.
        * OpenMW has a patch in the deps repo that disables building MyGUI as a framework, which I applied.
        * Note that OpenMW has instructions for a [minimal install](https://wiki.openmw.org/index.php?title=Development_Environment_Setup#MyGUI)
        * I installed to Homebrew for convenience with these [instructions](https://github.com/Homebrew/brew/blob/master/docs/FAQ.md#can-i-install-my-own-stuff-to-usrlocal) (CMAKE_INSTALL_PREFIX:PATH=/usr/local/Cellar/MyGUI/*version*)
    * OpenMW has a [fork](https://github.com/OpenMW/osg) of OpenSceneGraph focusing on speed, which you may want. I just used the Homebrew-provided version.
    * (the reason I skipped the OpenMW deps repo is that it forces libstdc++ for the dependencies, which will cause symbol errors with Homebrew's Qt5. I had installed all the dependencies with Homebrew when I tried building OpenMW the first time, so I didn't try very hard to salvage the deps repo that recompiled everything when I tried out changes. Long term, someone with more knowledge of CMake might be better served by modifying the deps repo instead.)
* CMake settings
    * CMAKE_PREFIX_PATH=PATH:/usr/local/opt/qt5
    * CMAKE_OSX_SYSROOT="macosx10.12"
    * DESIRED_QT_VERSION=5
    * CMAKE_CXX_FLAGS="-stdlib=libc++ -std=c++11"
    * BUILD_MYGUI_PLUGIN=FALSE

Doing that, I had a working build. Well, it seems to anyway, aside from the fullscreen behavior that I changed (of course it wouldn't be as easy as one line, heh...).
