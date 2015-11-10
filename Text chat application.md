# Text chat application

> Lets start creating a simple Text chatting application using WebRTC. which has one to many communication functionality.

# Step A: Create one HTML file.

> After creating file add following two script

```sh
<script type="text/javascript" src="//ajax.googleapis.com/ajax/libs/jquery/1.8/jquery.min.js"></script>
<script type="text/javascript" src="https://skyway.io/dist/0.3/peer.js"></script>
```

# Step B: To create a peer login into your skyway account. if you do not have skyway account please sign in.

> URL: http://nttcom.github.io/skyway/en/

> after successfully login you will get API key.　Use that API key in following code.

```sh
// Connect to PeerJS, have server assign an ID instead of providing one
// Showing off some of the configs available with PeerJS :).
var peer = new Peer({
  // Set API key for cloud server you do not need this if you are running your
  // own.
  key:`YOUR KEY`,

  // Set highest debug level (log everything!).
  debug: 3,

  // Set a logging function:
  logFunction: function() {
    var copy = Array.prototype.slice.call(arguments).join(' ');
    $('.log').append(copy + '<br>');
  }
});
```

# Step C: Connections.

### Step C.1: Add Connections

> To connect with multiple peers add following code in to your html file.

```sh
// Connect to a peer
$('#connect').click(function() {
  var requestedPeer = $('#rid').val();
  if (!connectedPeers[requestedPeer]) {
    // Create 2 connections, one labelled chat and another labelled file.
    var c = peer.connect(requestedPeer, {
      label: 'chat',
      serialization: 'none',
      metadata: {message: 'hi i want to chat with you!'}
    });
    c.on('open', function() {
      connect(c);
    });
    c.on('error', function(err) { alert(err); });
    var f = peer.connect(requestedPeer, { label: 'file', reliable: true });
    f.on('open', function() {
      connect(f);
    });
    f.on('error', function(err) { alert(err); });
  }
  connectedPeers[requestedPeer] = 1;
});
```

### Step C.2: Close Connections

> To close selected collection add following function which will help you to close single selected connection at a time.

```sh
  // Close a connection.
  $('#close').click(function() {
    eachActiveConnection(function(c) {
      c.close();
    });
  });
```

### Step C.3: Active Connections

> To check the active connection between to peer are there or not¥. following function has been called each time.

```sh
  // Goes through each active peer and calls FN on its connections.
  function eachActiveConnection(fn) {
    var actives = $('.active');
    var checkedIds = {};
    actives.each(function() {
      var peerId = $(this).attr('id');
      if (!checkedIds[peerId]) {
        var conns = peer.connections[peerId];
        for (var i = 0, ii = conns.length; i < ii; i += 1) {
          var conn = conns[i];
          fn(conn, $(this));
        }
      }
      checkedIds[peerId] = 1;
    });
  }
```

### Step C.4: Await Connections

> Following  function is wait to other peer to connect

```sh
// Await connections from others
peer.on('connection', connect);

peer.on('error', function(err) {
  console.log(err);
})
```

> After managing small part regarding connections. let`s  create a function which will handle all connections.

# Step D: Handle connections

> to handle the connections please create a function called "Connect"

```sh
function connect(c) {

}
```

> Insert the following code into connect function which will help to handle a chat connection and select connection handler. Apart from this it will create a chat box for each connections

```sh
// Handle a chat connection.
  if (c.label === 'chat') {
  	var chatbox = $('<div></div>')
  		.addClass('connection')
  		.addClass('active')
  		.attr('id', c.peer);
  	var header = $('<h1></h1>')
  		.html('Chat with <strong>' + c.peer + '</strong>');
  	var messages = $('<div><em>Peer connected.</em></div>')
  		.addClass('messages');
  	chatbox.append(header);
  	chatbox.append(messages);
  	// Select connection handler.
  	chatbox.on('click', function() {
  		if ($(this)
  			.attr('class')
  			.indexOf('active') === -1) {
  			$(this)
  				.addClass('active');
  		} else {
  			$(this)
  				.removeClass('active');
  		}
  	});
  	$('.filler')
  		.hide();
  	$('#connections')
  		.append(chatbox);
  	c.on('data', function(data) {
  		messages.append('<div><span class="peer">' + c.peer + '</span>: ' + data +
  			'</div>');
  	});
  	c.on('close', function() {
  		alert(c.peer + ' has left the chat.');
  		chatbox.remove();
  		if ($('.connection')
  			.length === 0) {
  			$('.filler')
  				.show();
  		}
  		delete connectedPeers[c.peer];
  	});
  }

   connectedPeers[c.peer] = 1;
```

# Step E: Send messages

> Send message to selected connections following function is used. this function will call "eachActiveConnection"  then get all active connections to send messages.

```sh
// Send a chat message to all active connections.
$('#send')
	.submit(function(e) {
		e.preventDefault();
		// For each active connection, send the message.
		var msg = $('#text').val();
		eachActiveConnection(function(c, $c) {
			if (c.label === 'chat') {
				c.send(msg);
				$c.find('.messages').append('<div><span class="you">You: </span>' + msg + '</div>');
			}
		});
		$('#text').val('');
		$('#text').focus();
	});
```

# Step F: Display Peer ID

> To display peer ID use following three line of code.

```sh
peer.on('open', function(id) {
	$('#pid').text(id);
});
```

# Step G: Adding GUI

> Once you done with script part. Lets include GUI. To add GUI insert following code which includes connections and chatBox container.

```sh
<body>
	<div id="actions">
		Your PeerJS ID is <span id="pid"></span><br>
		Connect to a peer: <input id="rid" placeholder="Someone else's id" type="text"><input class="button" id="connect" type="button" value="Connect"><br>
		<br>

		<form id="send" name="send">
			<input id="text" placeholder="Enter message" type="text"><input class="button" type="submit" value="Send to selected peers">
		</form>
		<button id="close">Close selected connections</button>
	</div>

	<div id="wrap">
		<div id="connections">
			<span class="filler">You have not yet made any connections.</span>
		</div>

		<div class="clear"></div>
	</div>

	<div id="box" style="background: #fff; font-size: 18px;padding:40px 30px; text-align: center;">
		Drag file here to send to active connections.
	</div>
</body>
```

- Save your file and run your project.
- for CSS and HTML files please check source code.
- For more support please feel free to drop a mail to our support team.
