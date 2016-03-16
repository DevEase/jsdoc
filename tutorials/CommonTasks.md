## Setup

The Web UI files are located in the mods\ui\web folder in your ElDewrito install. The dew:// protocol is mounted here. Create a new directory inside the screens folder and place all of your screen files in there. Your main HTML page should be named index.html.

Your screen itself will be loaded into its own iframe that fills the page. It is your responsibility to ensure that your screen contents are displayed at the correct position. Make sure that your screen correctly responds to the window being resized.

You always need to import dew.js:

```
<script type="text/javascript" src="dew://lib/dew.js"></script>
```

Optionally, feel free to use local jQuery as well:

```
<script type="text/javascript" src="dew://lib/jquery-2.2.1.min.js"></script>
```

## screens.json

screens.json lists all of the screens to load. You must add your screen to it in order to be able to use it. Each screen has the following structure:

* `id` - A unique ID string to assign to the screen.
* `url` - The screen's URL. This is not required as long as you set `var` instead.
* `var` - If `url` is not set, the name of a console variable to get the screen URL from (e.g. "Game.MenuURL").
* `fallbackUrl` - A URL to use if the main URL can't be accessed. This should always point to a dew:// URL.
* `captureInput` - Set this to `true` to make your screen capture game input. Use `false` to create an overlay.
* `zIndex` - Screens with larger `zIndex` values will be displayed on top.

For example, this is what the console screen definition looks like:

```
{
	"id": "console",
	"url": "dew://screens/console/",
	"captureInput": true,
	"zIndex": 10
}
```

## Debugging

ElDewrito provides a special web debugging mode which you can use to debug screens.
It is activated by default in debug builds, and you can activate it in release builds by passing the `-webdebug` command-line argument to eldorado.exe.

When web debugging mode is active, the following features are enabled:

* CEF log verbosity is turned on.
* The CEF remote debugger will listen on port 8884.
* Pressing F5 will reload all screens.
* Pressing F6 will reload the screen layer completely (intended for editing internal files).
* Pressing F7 will open Chrome and point it to the remote debugger.

## Detecting when your screen is shown or hidden

Make sure to avoid performing any intensive activities while your screen is hidden. Screens are always hidden by default. You can use the `show` and `hide` events to detect when your screen is shown or hidden:

```
dew.on("show", function (event) {
	// This code will be run when your screen is shown.
	// Use event.data to access any data passed to your screen.
});

dew.on("hide", function (event) {
	// This code will be run when your screen is hidden.
});
```

## Showing and hiding screens

Show the current screen:

```
dew.show();
```

Hide the current screen:

```
dew.hide();
```

Show the server browser:

```
dew.show("browser");
```

Hide the server browser:

```
dew.hide("browser");
```

### Running console commands

**Do not use RCON to run console commands. It is slow, unreliable, and the protocol is subject to change in the future.**

Basic usage if you don't need output:

```
dew.command("Game.Start");
```

Using a success callback if you do need output:

```
dew.command("Player.Name", function (name) {
	alert("Your name is " + name + "!");
});
```

### Pinging a server

**Note that the ping method is currently broken and reports incorrect ping times. It will be fixed soon.**

To ping a server, you first need to register a global `pong` event handler. Make sure to check the IP address in the event object, because pong events are broadcasted to all screens.

```
dew.on("pong", function (event) {
	// event.data.address contains the IPv4 address
	// event.data.latency contains the latency in milliseconds
});
```

Next, send the server a ping. If it responds, your `pong` handler will be triggered.

```
dew.ping("12.34.56.78");
```

### Getting the ElDewrito version

Since all communications with ElDewrito are asynchronous, you have to call the getVersion function with a success callback:

```
dew.getVersion(function (version) {
	alert(version);
});
```