# webkit-flatpack-sync-run

## Description

Imagine you have a powerful build machine that compiles
[WebKitGTK](https://webkitgtk.org/)/[WPE](https://wpewebkit.org/) from a [cloned
git
repository](https://trac.webkit.org/wiki/BuildingGtk#BuildingWebKitGTKfromgit). Then
you need to move the produced binaries into another computer to test the output.

The answer is, obviously, `rsync` the output. 

**But**, firstly, the computer running the tests should have exactly the same
dependencies than the build machine; secondly, WebKit uses a custom [Flatpak
SDK](https://trac.webkit.org/wiki/WebKitFlatpakSDK) where all the dependencies
can be kept regardless the distribution in the build machine. So, the SDK should
be installed besides the simple `rsync`.

On top of that, imagine you need to hack GStreamer in the test computer. As
GStreamer a set of dynamic libraries, it's possible to hack it (being careful
enough to avoid to break the ABI) outside of the build machine. A condition for
this possibility is to compile WebKit using
[`gst-build`](https://blogs.igalia.com/vjaquez/2020/06/16/webkit-flatpak-sdk-and-gst-build/)
(or simply `gstreamer` on *monorepo* branch). Then is possible to re-compile
GStreamer only in the testing computer and using it in Flatpak SDK.

All this is a bit complex, so a script to automate it would be nice. And this is
that script.

## Usage

$ ./webkit-flatpak-sync-run --gtk --debug MiniBrowser

The command will execute these tasks:

1. If there's no installed a user-only Flatpak SDK in `~/.cache/wk-sdk`, it will
   installed; otherwise it will be updated.
2. `rsync` the output binaries from the build machine (change it!), from the
   specified WebKit source code directory (change it!), taking the debug
   compilation of WebKitGTK.
3. It will compile GStreamer from the source code directory (change it!).
4. A fairly complex flatpak command is crafted and it will execute
   `MiniBrowser`. If you don't specify any command, a bash shell will be
   launched.

