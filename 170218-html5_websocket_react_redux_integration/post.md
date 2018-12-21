# HTML5 WebSocket - React Redux Integration

![](resources/ceab7292-e569-4fbf-b8e8-4751d550a8b1.png)

When developing on a front-end framework like React, a feature like realtime communication (from either the server end or the client end) is often needed (be it a chat room, notification, status update, collaborative document editing or massively multipayer online (MMO) game). WebSocket is the most preferable way these days, for a full duplex server-client communication.

From the client-end perspective, HTML5 WebSocket plays really well and often prefered over SSE (Server Sent Events). Nearabout all the back end framework supports WebSocket, many of them even have their dedicated frameworks which integrates WebSocket with the back end app seamlessly (e.g. Ruby on Rails's ActionCable).

### HTML5 WebSocket

The API Itself is simple . All it takes is,

  * Establishing connection to a server WebSocket endpoint
  * Binding callbacks to manage different lifecycle events of WebSocket (reception and transmission of messages)

Let's go through an example.

First, we are going to create a WebSocket connection instance
```javascript
const socket = new WebSocket("ws://echo.websocket.org");
```
We are using an existing WebSocket echo server.

Now, it's time to bind some events.
```javascript
socket.onopen = event => console.log("Connected")
socket.onclose = event => console.log("Disconnected")
socket.onmessage = event => console.log(`Received data: ${event.data}`)
socket.onerror = event => console.log(`Error: ${event.data}`)
```
The code itself is self explanatory. `onopen` and `onclose` are triggered during a connection initiation and termination consecutively.

`onmessage` is triggered when a transmitted data is intercepted. It receives each and every data that the sever sends, so all of the incoming data processing is performed here.

And, the last part, `onerror`, like you guessed, handles any websocket specific error events.

So far, we managed all the incoming communications and different lifecycle events. But, still we're missing the data transmission part. Well, guess what, it's as simple as
```javascript
const
  message1 = "Hello Server!",
  message2 = {"type": "MESSAGE", "data": "Hello Again!"}
socket.send(message1)
socket.send(JSON.stringify(message2))
```
`send` method accepts data as a string to transmit. So, if a data structure like JSON is to be sent, then it must be converted into a string, beforehand. It is also possible to transmit some datatypes different than string (e.g. ArrayBuffer, Blob), for which we'll have to set the `binaryType` property of the socket.
```javascript
socket.binaryType = "arrayBuffer"
```
Let's not discuss it here, cause, the default type `String` can satisfy our requirements for now.

And, finally, to terminate a WebSocket session, a `close` method is called upon the socket.
```javascript
socket.close()
```

### React-Redux Integration

React is a pretty descent library for frontend development, and when it is integrated with Redux, things becomes better in terms of manageability and modularization. But, WebSocket integration could be a bit tricky. I am going to demonstrate a WebSocket integration method that utilizes React context.

We are going to use a [react redux scaffold](https://github.com/at-shakil/react-redux-websocket). (it already includes all the code demonstrated here, so it might be a good idea to get it first)

Our first step will be to integrate WebSocket with the entire SPA. In our case, it's `App.js`. it works as a base for the SPA, cause `App` component gets mounted along with every other component mount.

```javascript
/* App.js */
import React, { Component, PropTypes } from 'react';
import { connect } from 'react-redux';
import AppConf from '../config/application.json';
import { SocketDataType } from '../constants/Enums';

class App extends Component {
  getChildContext() {
    const socket = new WebSocket(AppConf.websocketOrigin);
    this.props.handleSocketEvents(socket);
    return {socket};
  }

  render() {
    return (
      <div className="page-container">
        {this.props.children}
      </div>
    );
  }
}

App.props = {
  children: PropTypes.element.isRequired
};

App.childContextTypes = {
  socket: PropTypes.object
};

const mapDispatchToProps = dispatch => ({
  handleSocketEvents(socket) {
    socket.onopen = e => console.log("Connected");
    socket.onclose = e => console.log("Disconnected");
    socket.onerror = e => console.log(`Error: ${e.data}`);
    socket.onmessage = e => {
      const { type, data } = JSON.parse(e.data);

      switch(type) {
        case SocketDataType.ALERT:
          alert(data);
          break;
        default:
          return;
      }
    };
  }
});

export default connect(null, mapDispatchToProps)(App);

```

Let's concentrate on WebSocket specific parts. Since context is used, it should be set before it can be used.

```javascript
  getChildContext() {
    const socket = new WebSocket(AppConf.websocketOrigin);
    this.props.handleSocketEvents(socket);
    return {socket};
  }
```

`AppConf.websocketOrigin` stores the WebSocket endpoint URL. A socket is first initialized and then it's fed to an event handler (We'll go through it shortly). And finally, the socket is set as a context, which will be passed down to the child components.

The context type is specified in order to make sure that values get passed to the child component (which is a [React requirement](https://facebook.github.io/react/docs/context.html#how-to-use-context)).

```javascript
App.childContextTypes = {
  socket: PropTypes.object
};
```

The event handler resides in `mapDispatchToProps` function of Redux. We need it here, cause, we'll face the necessity of dispatching actions once in a while.

```javascript
  handleSocketEvents(socket) {
    socket.onopen = e => console.log("Connected");
    socket.onclose = e => console.log("Disconnected");
    socket.onerror = e => console.log(`Error: ${e.data}`);
    socket.onmessage = e => {
      const { type, data } = JSON.parse(e.data);

      switch(type) {
        case SocketDataType.ALERT:
          alert(data);
          break;
        default:
          return;
      }
    };
  }
```

WebSocket accepts string, so we need to send a stringified object, and parse it upon reception.

For shake of simplicity, the entire event handler logic is put in the `handleSocketEvents` function. But, as the application grows, it will get ugly very soon. The situation could be made more manageable by creating a different service and putting all the logics in there.

So far we covered, data reception and event management. The only left out part is transmitting data to the socket. Our most preferable client for this to demonstrate would be `Home.js`.

First, we need to grab the socket from the context.

```javascript
  onMessage() {
    const
      { sendMessage } = this.props,
      { socket } = this.context;
    sendMessage(socket, this.message.value);
  }
```

And, in order to ensure that the context get received, `socket`'s data type is specified in the `contextTypes`.

```javascript
Home.contextTypes = {
  socket: PropTypes.object.isRequired
};
```

Then, a `send` method is invoked on the `socket`, which transmits the message.

```javascript
  sendMessage(socket, message) {
    const data = JSON.stringify({
      type: SocketDataType.ALERT,
      data: message
    });

    socket.send(data);
  }
```


That's what it takes to integrate WebSocket with a React-Redux application. Of course, it's just one way to get things done. But, it makes things easier and relieves the headache of utilizing a third party library, without sacrificing much. Of course, if you are paranoid enough, then you may want to go through [this](https://facebook.github.io/react/docs/context.html#why-not-to-use-context) a little, just to be on the safe side. ;)

