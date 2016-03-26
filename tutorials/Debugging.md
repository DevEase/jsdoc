ElDewrito provides a special web debugging mode which you can use to debug screens.
It is activated by default in debug builds, and you can activate it in release builds by passing the `-webdebug` command-line argument to eldorado.exe.

When web debugging mode is active, the following features are enabled:

* CEF log verbosity is turned on.
* The CEF remote debugger will listen on port 8884.
* Pressing F5 will reload all screens.
* Pressing F6 will reload the screen layer completely (intended for editing internal files).
* Pressing F7 will open Chrome and point it to the remote debugger.