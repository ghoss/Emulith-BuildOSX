# Emulith-BuildOSX
This page contains step-by-step build instructions for Jos Dreesen's **Emulith** Lilith Emulator (version 1.3) on Mac OSX 10.13 (and later).

The process might also work for OSX versions down to 10.9 and possibly beyond, but I haven't tested this.

## Prerequisites
* **Mac OSX 10.13+**
* **Apple XCode runtime tools.** This process uses the **clang** toolchain installed by XCode instead of the GCC toolchain as per Jos' original instructions. You can verify that the toolchain is properly installed by typing `clang++ -v` in a shell. The output should be something like

    ```
    Apple LLVM version 10.0.0 (clang-1000.11.45.5)
    Target: x86_64-apple-darwin17.7.0
    Thread model: posix
    InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
    ```
    
* **XQuartz** (www.xquartz.org), which provides the X11 libraries required by the build. I used version 2.8.1, which was the latest version at the time of writing. Download and run the DMG installer from

    https://github.com/XQuartz/XQuartz/releases/download/XQuartz-2.8.1/XQuartz-2.8.1.dmg

The following components will be downloaded and compiled during the build process (see below):

* **FLTK** (www.fltk.org). I used version 1.3.8 (the latest version at the time).
* **Emulith** source code (ftp://ftp.dreesen.ch/Emulith). My build process is tailored to version 1.3.

## Build Instructions
1. Open a **Terminal** window. Decide on some convenient location for the build files, e.g. `~/Desktop`.
2. Create the build directories:

    ```
    $ cd convenient_location
    $ mkdir build build/fltk
    ```   
3. Download and expand the **Emulith** source files into the build directory:

    ```
    $ curl ftp://ftp.dreesen.ch/Emulith/Emulith_v13.tgz | tar xzf - -C build
    ```   
4. Download the modified Makefile (`Makefile_modified`) from this repository:

    ```
    $ curl -O https://github.com/good-sushi/Emulith-BuildOSX/raw/main/Makefile_modified
    $ mv Makefile Makefile_original; cp Makefile_modified Makefile
    ```

5. Download and expand the **FLTK** source files into the build subdirectory:

    ```
    curl -k https://www.fltk.org/pub/fltk/1.3.8/fltk-1.3.8-source.tar.gz | tar xzf - -C build
    ```

6. Build **FLTK** (installs into `build/fltk`, where it is expected by Emulith's Makefile):

    ```
    $ cd build/fltk-1.3.8
    $ ./configure --prefix=`pwd`/../fltk
    $ make clean; make install
    ```
    
7. Build **Emulith**

    ```
    $ cd ..
    $ make clean; make osx
    ```
    
8. Start the emulator and enjoy!

    ```
    $ ./emulith
    ```
## Appendix: My Changes To Jos' Original Makefile
```
$ diff Makefile Makefile_modified

31c31
< includes    = -Iinclude    -Isupport      -Ifltk      
---
> includes    = -Iinclude    -Isupport      -Ifltk/include
49c49
< osxstuff    = -framework Cocoa   -arch i386 -arch ppc
---
> osxstuff    = -framework Cocoa
58,59c58
< 	g++ -Wall  -O3 -o emulith $(libdirs) $(includes) $(corefiles) $(norm_io) $(fltklib) $(defines) $(osxlibs) $(osxstuff) -D__APPLE__ -D__CYGWIN__ 
< #	fltk-config --post emulith
---
> 	clang++ -Wall  -O3 -o emulith $(libdirs) $(includes) $(corefiles) $(norm_io) $(fltklib) $(defines) $(osxlibs) $(osxstuff)
```
