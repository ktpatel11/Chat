# File Transfer Applications.

> to add the file transfer functionality in text chat application perform following steps.
> please see following image to understand basic GUI of application which we are going to develop.

![Image of file]
(https://github.com/ktpatel11/Chat/blob/gh-pages/images/file.png)


# Step A: Prepare file drop box.

> to create a box on which whenever a file is dropped. it will send that file URL to all active connections. to check all active connection here we are using "eachActiveConnection" function

```sh
$(document).ready(function() {
  // Prepare file drop box.
  var box = $('#box');
  box.on('dragenter', doNothing);
  box.on('dragover', doNothing);
  box.on('drop', function(e){
    e.originalEvent.preventDefault();
    var file = e.originalEvent.dataTransfer.files[0];
    eachActiveConnection(function(c, $c) {
      if (c.label === 'file') {
        c.send(file);
        $c.find('.messages').append('<div><span class="file">You sent a file.</span></div>');
      }
    });
  });
  function doNothing(e){
    e.preventDefault();
    e.stopPropagation();
  }
```

# Step B: Create File URL for receiver

> add following line of code in connect function of text chat application. here we are dividing connect function into two parts. 1 chat and 2 file shar.

```sh
else if (c.label === 'file') {
 c.on('data', function(data) {
   // If we're getting a file, create a URL for it.
   if (data.constructor === ArrayBuffer) {
     var dataView = new Uint8Array(data);
     var dataBlob = new Blob([dataView]);
     var url = window.URL.createObjectURL(dataBlob);
     $('#' + c.peer)
       .find('.messages')
       .append('<div><span class="file">' +
         c.peer + ' has sent you a <a target="_blank" href="' + url +
         '">file</a>.</span></div>');
   }
 });
}
```

# Step C: Drop file GUI area

> add following code into body part of HTML file.

```sh

	<div id="box" style="background: #fff; font-size: 18px;padding:40px 30px; text-align: center;">
		Drag file here to send to active connections.
	</div>
```

- Save your file and run your project.
- for CSS and HTML files please check source code.
- For more support please feel free to drop a mail to our support team.
