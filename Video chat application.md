# Video chat application

> Lets start creating a simple video chatting application using WebRTC. which has one to one communication functionality.
> please see following image to understand basic GUI of application which we are going to develop.

![Image of videochat]
(https://github.com/ktpatel11/Chat/blob/gh-pages/images/videochat.png)

# Step A: Create one HTML file.

> to add HTML tag and css file start your coding with following code.

```sh
<html>
<head>
  <title>PeerJS - Video chat example</title>
  <link rel="stylesheet" href="style.css">
```

>After adding HTML tag and CSS file, add following two script

```sh
<script type="text/javascript" src="//ajax.googleapis.com/ajax/libs/jquery/1.8/jquery.min.js"></script>
<script type="text/javascript" src="https://skyway.io/dist/0.3/peer.js"></script>
```

# Step B: To create a peer login into your skyway account. if you do not have skyway account please sign in.

> URL: http://nttcom.github.io/skyway/en/

> after successfully login you will get API key.　Use that API key in following code in script part of code.

```sh
// PeerJS object
var peer = new Peer({ key: 'YOUR KEY', debug: 3});

peer.on('open', function(){
  $('#my-id').text(peer.id);
});
```

# Step C: Receiving a call

> to receive a call form caller without asking permission form a user add following function into your script.

```sh
// Receiving a call
  peer.on('call', function(call){
    // Answer the call automatically (instead of prompting user) for demo purposes
    call.answer(window.localStream);
    step3(call);
  });
  peer.on('error', function(err){
    alert(err.message);
    // Return to step 2 if error occurs
    step2();
  });
```

# Step D: To call some user.

> Whenever user type a peers id into text box and press make-call button then it is calling the other user whose id is typed in text box. to implement this functionality please have a look of following code.

```sh
// Click handlers setup
$(function(){
  $('#make-call').click(function(){
    // Initiate a call!
    var call = peer.call($('#callto-id').val(), window.localStream);

    step3(call);
  });

  $('#end-call').click(function(){
    window.existingCall.close();
    step2();
  });
```

# Step E: to check different constrains we have divided process into three steps.

### Step E.1: Get local audio/video stream
> check the access of local audio/ video stream. if the user do not have access then it will show error message and asked user to allow access from browser.

```sh
function step1 () {
  // Get audio/video stream
  navigator.getUserMedia({audio: true, video: true}, function(stream){
    // Set your video displays
    $('#my-video').prop('src', URL.createObjectURL(stream));

    window.localStream = stream;
    step2();
  }, function(){ $('#step1-error').show(); });
}
```

### Step E.2: Make calls to others

> Once two user are in call at that time. this functionality hide step E:1 and Step E:3. apart from this this functionality show the current user ID and end call button.

```sh
function step2 () {
  $('#step1, #step3').hide();
  $('#step2').show();
}
```

### Step E.3: Call in progress

> when two users are in call at that time any third user can not call to user which are in call. if the third user call at that time following function will show error message to the third user.

```sh
function step3 (call) {
  // Hang up on an existing call if present
  if (window.existingCall) {
    window.existingCall.close();
  }

  // Wait for stream on the call, then set peer video display
  call.on('stream', function(stream){
    $('#their-video').prop('src', URL.createObjectURL(stream));
  });

  // UI stuff
  window.existingCall = call;
  $('#their-id').text(call.peer);
  call.on('close', step2);
  $('#step1, #step2').hide();
  $('#step3').show();
}
```

# Step F: Adding GUI

> Once you done with script part. Lets include GUI. To add GUI insert following code which includes two video container and three division for maintain different constraint steps.

```sh
<body>
	<div class="pure-g">
		<!-- Video area -->
		<div class="pure-u-2-3" id="video-container">
        	<video id="their-video" autoplay></video>
        	<video id="my-video" muted="true" autoplay></video>
    </div>

		<!-- Steps -->
		<div class="pure-u-1-3">
			<h2>PeerJS Video Chat</h2>
			<!-- Get local audio/video stream -->


			<div id="step1">
				<p>Please click `allow` on the top of the screen so we can access your
				webcam and microphone for calls.</p>


				<div id="step1-error">
					<p>Failed to access the webcam and microphone. Make sure to run this demo on an http server and click allow when asked for permission by the browser.</p>
					<a class="pure-button pure-button-error" href="#" id="step1-retry">Try again</a>
				</div>
			</div>

			<!-- Make calls to others -->
			<div id="step2">
				<p>Your id: <span id="my-id">...</span></p>


				<p>Share this id with others so they can call you.</p>


				<h3>Make a call</h3>


				<div class="pure-form">
					<input id="callto-id" placeholder="Call user id..." type="text"> <a class="pure-button pure-button-success" href="#" id="make-call">Call</a>
				</div>
			</div>

			<!-- Call in progress -->
			<div id="step3">
				<p>Currently in call with <span id="their-id">...</span></p>
				<p><a class="pure-button pure-button-error" href="#" id="end-call">End call</a></p>
			</div>

		</div>
	</div>
</body>
</html>
```

- Save your file and run your project.
- for CSS and HTML files please check source code.
- For more support please feel free to drop a mail to our support team.
