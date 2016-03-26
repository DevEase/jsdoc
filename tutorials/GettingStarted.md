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