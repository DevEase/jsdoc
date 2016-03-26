## Getting Started

See [Getting Started](tutorial-GettingStarted.html).

## Debugging

See [Debugging](tutorial-Debugging.html).

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
dew.command("Player.Name", {}, function (name) {
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