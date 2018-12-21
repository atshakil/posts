# Inter-SPA and intra-SPA post-load operations management in React Redux

![](resources/a77c6634-b0e9-4bbe-b240-f2fb85d104e7.jpg)

Consider a scenario, where you're working on a SPA. You clicked a button, and in response of the click, you will be redirected to another SPA residing in the same origin or simply you want to reload the page (same SPA). For any of the two cases, if you want to perform some operation (like dispatching an action) immediately after load/reload of the SPA based on the last state of the SPA prior to the reload, there is no direct way to do that. Cause, any change you make to Redux state (or React state) will be lost as soon as you reload the SPA. So, performing an operation (after a reload) that depends on prior React or Redux state, will not be possible. In this article, we'll investigate an approach to address this issue.

### Concept
Since React or Redux state doesn't survive a reload, a short term persistence layer can be utilized to address this issue. The layer must persist data during a reload. `sessionStorage` is the perfect client for this task, cause it persists through an entire browser session, and different tab and window receives their different sets of `sessionStorage`.

All the operations, that need to be performed after a load/reload are stored in the `sessionStorage` in a structured way and gets executed on load/reload event.

A post-load operation lifecycle can be as simple as the steps below.

* A `PostLoadOperations` service is created
* Operation types and execution strategies are specified in the service
* Operations are pushed to `sessionStorage` through `PostLoadOperations` service
* When the target SPA gets loaded/reloaded, a batch processing is performed on accumulated operations (in the `sessionStorage`)
* And, finally, the persistence layer gets flushed.

### Usage

Let's walk through a demonstration project for a better understanding. You may grab the project from my [github](https://github.com/at-shakil/react-redux-post_load_ops) repo., which is a fully operational demo. and contains all the code explained here.

Post-load operations are managed through a service. The operational service takes an identical form like below.

```javascript
# PostLoadOperations.js
import { PostLoadOperationType } from '../constants/Enums';
import { setPostLoadMessage } from '../actions/Home';

export default dispatch => {
  const operations = JSON.parse(sessionStorage.getItem("postLoadOperations"));

  return {
    push(type, data) {
      sessionStorage.setItem("postLoadOperations", JSON.stringify(operations ?
        operations.concat({type, data}) : [{type, data}]));
    },
    run() {
      if(operations) {
        operations.forEach(operation => {
          switch(operation.type) {
            case PostLoadOperationType.MESSAGE:
              dispatch(setPostLoadMessage(operation.data));
              break;
            default:
              return;
          }
        });
        sessionStorage.removeItem("postLoadOperations");
      }
    }
  };
};

```

The service itself is a function that accepts `dispatch` as a parameter, and returns `push` and `run` function in an object.

It manages the persistence layer for the post-load operations, and provides interface to operate on it. Let's dissect and investigate the pieces.

```javascript
const operations = JSON.parse(sessionStorage.getItem("postLoadOperations"));
```

First, when the service is invoked, it checks for a `sessionStorage` entry that contains a key `postLoadOperations`.

```javascript
push(type, data) {
  sessionStorage.setItem("postLoadOperations", JSON.stringify(operations ?
    operations.concat({type, data}) : [{type, data}]));
},
```

The `push` function, like the name suggests, pushes a post-load operation to the operation queue. It checks whether a queue exists or not. If a queue exists, it pushes the operation to the queue, or else, creates a queue and pushes the specified operation to the new queue instead.

```javascript
run() {
  if(operations) {
    operations.forEach(operation => {
      switch(operation.type) {
        case PostLoadOperationType.MESSAGE:
          dispatch(setPostLoadMessage(operation.data));
          break;
        default:
          return;
      }
    });
    sessionStorage.removeItem("postLoadOperations");
  }
}
```

And, `run` contains the instruction for processing the operations. It acts somewhat similar to the Redux reducer. When the function is invoked, each operation is checked for a matching processor. If an operation type matches a processor, then the operation is executed, or else, left alone. Since we're considering a React-Redux app., in most of the cases we'll dispatch a lot's of action in the processor (`case`) blocks. Finally, when all the operations are processed, the queue is removed, since it has a single shot use case.

```javascript
<div className="home">
  <span className="input-label">Message: </span>
  <input className="form-control primary-input"
    value={message}
    onChange={e => this.setState({message: e.target.value})} />
  <Button className="submit-button" onClick={e => onSpaReload(message)}>
    Click me to reload the SPA!
  </Button>
  <div className="post-load-message">
    Post-load Message: {postLoadMessage}
  </div>
</div>
```

Let's focus on our UI. The primary UI can be found in `Home` component. It's an ordinary component with a controlled text `input`, a `Button` and a `div` region to display some text.

When the `Button` is clicked, a callback is fired, that is defined in Redux's `mapDispatchToProps`.

```javascript
const mapDispatchToProps = dispatch => ({
  onSpaReload(message) {
    PostLoadOperations().push(PostLoadOperationType.MESSAGE, message);
    location.reload();
  }
});
```

Here, we are going to set a post-load operation and reload the SPA. `push`, takes operation type as the first argument and operation data (data, that must be provided for a specific operation processor to work) as the second.

And, probably you have already figured out that, the `postLoadMessage` prop is coming from `home.js` reducer, and located in `state.home.postLoadMessage` of the Redux state.

So far, we covered the operation queuing part. And, we know that, if some text is inserted in the `input` and the `Button` is pressed, a post-load operation will queue up and a SPA reload will trigger. But, how are we gonna process the operation then?

Here comes `App.js`, the primary component of our app., that must be loaded in order for any child component in our app. to load (e.g. `Home.js`). We latch a hook to our `PostLoadOperations` service's `run` method with the `App` component's `componentDidMount` lifecycle method.

```javascript
componentDidMount() {
  this.props.performPostLoadOperations();
}
```
```javascript
const mapDispatchToProps = dispatch => ({
  performPostLoadOperations() {
    PostLoadOperations(dispatch).run();
  }
});
```

So, basically what's happening here is, when our target SPA gets loaded / reloaded, it looks for the existence of any post-load operations. If any exists, they get executed. In case of our demo. when a SPA reload is triggered, the inputted text is dispatched along with the action `setPostLoadMessage`, which in turn, gets displayed in the `div` block of our UI.
```javascript
dispatch(setPostLoadMessage(operation.data));
```

###

That's all there is to it. `PostLoadOperations` service usage put negligible impact to the SPA performance, cause it starts batch processing once the `App` component is mounted, not before that. Although, if other subroutines are expected in the `componentDidMount` method of the `App` component, then it would be a good idea to stack them up before the `PostLoadOperations` to avoid any potentially significant execution delay caused by a huge operations queue ;).

