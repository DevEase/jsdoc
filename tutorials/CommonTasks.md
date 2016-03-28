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

### Calling asynchronous methods

Some methods, such as `dew.command`, will need to communicate with the game in order to operate.
Because JavaScript code is executed in a separate process, these methods will immediately return a `Promise` that will be fulfilled once the operation completes.
If you aren't familiar with promises, here is very a basic skeleton for you to use:

```
dew.methodName(arguments).then(function (result) {

	// This will be called once the method completes.
	// result is the value returned by the method.

}).catch(function (error) {

	// This will be called if an error occurs.
	// If you don't care to handle errors, then there is no need to use catch().

});
```

### Running console commands

**Do not use RCON to run console commands. It is slow, unreliable, and the protocol is subject to change in the future.**

Basic usage if you don't need output:

```
dew.command("Game.Start");
```

Using the promise if you do need output:

```
dew.command("Player.Name").then(function (name) {
	alert("Your name is " + name + "!");
});
```

Reading the value of an internal variable (such as a client-side one):

```
dew.command("Server.SprintEnabledClient", { internal: true }).then(function (val) {
	if (val === "1") {
		alert("Sprint is enabled.");
	} else {
		alert("Sprint is disabled.");
	}
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

You can use `dew.getVersion` to get the current version of ElDewrito. Remember that this runs asynchronously.

```
dew.getVersion().then(function (version) {
	alert(version);
});
```