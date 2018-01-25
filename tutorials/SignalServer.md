## Usage

The signal server is for communicating with other player's screens with small amounts of data. If you need to move a lot of information or stream data to a peer, use the signal server to setup an rtc connection and send it directly to them instead of through the signal server.

## First steps

To begin, your screen should wait for the signal-ready event.

This is emitted after connecting to a server and recieving a packet with the necessary information to use the signal server

```
dew.on("signal-ready", function (info) {
	//Your client has now been assigned a password to connect to the signal server with
	//info.data.server and info.data.password are needed
});
```

If you do not listen for the signal-ready event you can also retrieve this information using the server.websocketinfo command

This is useful for debugging since you can refresh the screen instead of needing to disconnect from the server and reconnecting.

```
dew.command("server.websocketinfo").then(function (socketInfo) {
	var info = JSON.parse(socketInfo);
});
```

The info object above contains: 
* `server` - The server to connect to in the format ip:port
* `password` - Used for authenticating with the signal server

## Connecting to the signal server

Connection is done with a websocket and a subprotocol that is unique to your screen.

Your websocket should implement these functions at the time of creation

* 'onmessage' - For communication with other peers as well as possible disconnect reasons
* 'onopen' - So we can send our password at the first chance
* 'onclose' - For cleanup after the player leaves the server

```
var MyWebSocket = new WebSocket("ws://" + info.server, "MySubprotocol");
MyWebSocket.onopen = function(){
	MyWebSocket.send(info.password);
}
MyWebSocket.onmessage = function(msg){
	if(msg.data == "try again later"){} // The client was not been fully established and should wait a moment before sending the password again
	else if(msg.data == "bad password"){} // Your password was incorrect
	else{
		//parse messages from other players
	}
}
MyWebSocket.onclose = function(){
	//We should do any cleanup that needs to happen, here.
}
```

The first message sent after connecting should be your password.

```
dew.on("signal-ready", function (info) {
	var MyWebSocket = new WebSocket("ws://" + info.data.server, "MySubprotocol");
	MyWebSocket.onopen = function(){
		MyWebSocket.send(info.data.password);
	}
});
```

Once you have sent your password, you will be free to submit messages to the signal server. 

## Subprotocol

**The subprotocol is important.** Only peers that implement the same subprotocol when connecting to the signal server will be able to communicate with each other.

This is defined in the websocket's constructor.
```
var MyWebSocket = new WebSocket("ws://" + info.server, "MySubprotocol");
```
With the above snippet, only other websockets that use the subprotocol `MySubprotocol` will be able to receive messages from this socket.


# Talking to other screens
## Sending data

The signal server requires that all data sent has either of the below objects in json format

* `broadcast` - The data sent will be sent to all connected peers on the same subbprotocol
* `sendTo` - The data sent will only be sent to the peer specific

An example of sending a broadcast

```
MyWebSocket.send(JSON.stringify({
	'broadcast': 'This is a message that other peers will see',
	'extraData': 'We can send other things as long as broadcast or sendTo is used'
}));
```

Sending data to a specific peer is similar but uses the player's displayname + uid in the format name|uid attached to the sendTo object

```
MyWebSocket.send(JSON.stringify({
	'sendTo': 'Maximumgame|8ff4e9111f9089c2',
	'extraData': 'We can send other things as long as broadcast or sendTo is used'
}));
```

## Receiving data

When receiving data, the signal server will attach a peer's uid with their data automatically

```
MyWebSocket.onmessage = function(msg){
	var data = JSON.parse(msg.data);
	console.log(data.uid); // Maximumgame|8ff4e9111f9089c2 - Automatically created by the signal server
}
```

With this, your screen can uniquely identify where a message came from

### Signal server specific events

* `leave` - emitted when a player leaves or the socket disconnects. The leave object only contains a string of the uid of the player that has left. Similar to the uid object sent with other messages.



## Blacklist

The signal server will reject your message if anything below exists in the json sent
* `uid` - The server will create this for you. There is no need to attach your own.
* `leave` - The server will generate this message for you when your socket disconnects or the player leaves the game.

## Example

An implementation of a screen sending `Hello world!` when joining a server and a client responding with `Hi there!`

```
var MyWebSocket = null;
$(document).ready(function () {
	dew.on("signal-ready", function (info) {
        MyWebSocket = new WebSocket("ws://" + info.data.server, "HelloWorldExample");
		MyWebSocket.onmessage = ParseMessage;
		MyWebSocket.onopen = function(){
			MyWebSocket.send(info.data.password); //send does not block so we need to wait a moment before sending our broadcast
			setTimeout(function(){
				MyWebSocket.send(JSON.stringify({
					'broadcast': 'Hello world!'
				}));
			}, 1000);
		}
    });
});

function ParseMessage(msg){
	var data = JSON.parse(msg.data);
	if(data.broadcast && data.broadcast == 'Hello world!'){
		MyWebSocket.send(JSON.stringify({
			'sendTo': data.uid,
			'customMessage': 'Hi there!'
		}));
	}
	else if(data.sendTo && data.customMessage){ //The message was directed to us, we also check that the customMessage object exists
		console.log("Received: " + data.customMessage + " from: " + data.uid);
	}
	else if(data.leave){
		console.log(data.leave " has left");
	}
}
```