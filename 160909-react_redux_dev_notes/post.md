# React Redux - development notes

Among the JavaScript frameworks React is becoming popular these days. Apparently the ease of maintenance (due to markup and view logic unification), isomorphism (the ability to run on both client and server end) and certainly the concept of virtual DOM is behind it's popularity. JSX is a OOP language that can be used with React which speeds up the development a lot. It's not a must and can be avoided with the penalty of some extra hours of coding every time.

Redux on the other hand is a nice fit for React, which handles the React states efficiently with a single state tree architecture. While the combination of React and Redux is great, it's learning curve is quite steep. In the wild, there are informations available on them both, but, none of them provides a smooth guided path. Some are too advanced while others make it difficult to understand the simple thing by using ES6, package management modules and dependencies and a lot's of files.

This article will provide some note on React, Redux, ES6 and package management in a concise manner. It will not show a route in understanding React or Redux from the scratch, but will act as a very concise reference.

### React

The formal definition expresses React as,
> A JS library that provides view for data rendered as HTML

#### React in general

  - Component based: Components are encapsulated thingies.
  - Can render on server using Node. (potentially this feature can be used in React Native)

#### React building blocks

  - `render(component, mountNode)` method takes input and returns displayable. To simplify, `render` takes the ELEMENT(s) that needs to be displayed, and the LOCATION (mountNode) where to display
  - **Element** is the reacts representation of a tag and it's contents. In pure JS, the element could be created using `React.createElement` function, where in JSX a simplified and custom tag like representation is used.
  - *Controlled* vs *Uncontrolled* components:
    - **Controlled component** includes a `value` prop which reflect the element value. An `onChange` event could be used to update `value` in response to user input through `setState`.
    - **Uncontrolled component** doesn't have a `value` prop and any input will immediately reflect the rendered element. But, nonetheless, updates could be listened the same way using `onChange` and and a default value can be set with `defaultValue` or `defaultValue` prop.
    - These components (e.g. `<input>`, `<select>`) can be of different types, that can be expressed to React using `type` prop. (e.g. `type="(text|checkbox|radio)"`)
  - **State identification**: In order to add interactivity to the React components it is necessary to identify states. And, later it is needed to identify the components that owns (mutates) these state. [This](https://facebook.github.io/react/docs/thinking-in-react.html#step-4-identify-where-your-state-should-live) is probably a good guideline for state owner preparation.
  - By design, react props are considered read only. They (`this.props` keys) should never be manipulated.
  - **Single root**: React components can only have a single root.
  - By convention, custom React class names begin with an uppercase letter, where native HTML element names start with a lowercase letter.
  - Some significant React specific methods (including lifecycle methods) for fast startup:
    - Mounting
      - `getInitialState`
      - `componentWillMount`
      - `componentDidMount`
    - Updating
      - `componentWillReceiveProps`
      - `shouldComponentUpdate`
      - `componentWillUpdate`
      - `componentDidUpdate`
    - Unmounting
      - `componentWillUnmount`
    - Helpers
      - `forceUpdate`
      - `getInitialState`
      - `setState(data, callback)`
      - `propTypes`
      -`getDefaultProps`

  - `ref` is a special attribute that can be attached to anything returned from `render`.
    - Value of `ref` can be a "callback function" or a "string"
      - *callback function*: executes as soon as the associated component is mounted. The referenced component is passed as a parameter of the function.
      - *string*: can be used as a reference to the associated component. An usage should look like `this.refs.comp_name`. It is a considered legacy and should be avoided in new codes. A compatible way of extracting reference can be performed with "callback function" using the following syntax.

        ```javascript
        ref={ref => this.comp_name = ref}
        ```

  - `props.children`: This holds all the children components that the reference component may have. When there is a single child, the child itself it returned, not the array. For example, calling `this.props.children` over `<CompX>Data</CompX>` will return `Data` which is of type `String`.
  - **Single responsibility principle** is a development principle that suggests that a single class (or in our case, component) should do a single thing only. This also implies that when necessity arises, it should be decomposed into smaller sub-components.
  - **State** in React is the explicit representation of an app. It makes an app predictable. State may include a large variety of data from a single input element's state to the state of an entire cloud spreadsheet.
  - **"state" should contain** data that event handlers may change to trigger a UI update. All data that can be *computed* or *retrievable* from current state should be excluded.
  - **Owner-ownee and parent-child relationship**: [#link](https://facebook.github.io/react/docs/multiple-components.html#ownership)

  - **React class definition** can be accomplished in three different ways
    - **`React.createClass` function**

      Example

        ```javascript
          // Simple way
          React.createClass({
            render: function() {
              return (
                <div>A simple react component</div>
              );
            }
          });

          // Using ES6 object shorthand
          React.createClass({
            render() {
              return (
                <div>Another react component</div>
              );
            }
          });
        ```

    - **ES6 class**

      Example

        ```javascript
          class MyComponent extends Component {
            render() {
              return (
                <div>Yet another react component</div>
              );
            }
          }
        ```

    - **JS function / ES6 arrow**

      Example

        ```javascript
        const MyComponent = () => <div>A tiny component</div>;
        ```

### Redux

#### It can be expressed as

  - Predictable state container
  - State: Different phases of the UI that cannot be calculated, duplicated or generated from the available dataset. State may include different *user interactions* and environment changes (e.g. network request) that may occur during the life-cycle of an application.

#### Redux building blocks

  - **Reducer** is the pure function of previous state and action that returns next state. Reducers are accumulated in store. Store methods,
    - `getState` - Returns current state
    - `dispatch` - Dispatches an action
    - `subscribe` - Subscribes a callback for the store. When an action is dispatched, the subscribed callback will be called.
  - **Pure function**
    - Calculates a result in response to the provided arguments
    - Doesn't modify the arguments
    - Doesn't modify any other components (e.g. make network request or DB update)
    - Predictable (same set of results for the same set of arguments)
    - Example: Reducer
  - **State** is an object tree, representing different states of an application. The state of an application is stored in **an object tree** within **a single store**. State should be considered read only and should only be manipulated through action dispatch.
  - **Action** is a plain object that includes, `action_type` and `changes` or `action specifics` (e.g. entry index, filter_name). It must have a `type` property.
  - **Action Creators** are the functions that create actions. There is also **bound action creator** that automatically dispatches.
  - **Store** Only a single store is possible. For multiple data handling logics (reducers) reducer composition can be used with `combineReducers()` function which in turn can be passed to the store. Store can be created using `createStore()` function where a second argument can be optionally passed as initial state.
    - The `subscribe()` method returns a function for unregistering the listener.
    - Calling `rootReducer.dispatch(action)` invokes all the reducers.
    - When state changed, every subscribed listeners to the store are called.
  - **Presentational and Container Components**
  This distinction between component types is for several benefit, where the prime cause is concern separation between view and data and extending re-usability.
    - **Presentational Component**: Like the name suggests, these are usually the components that are used to represent UI blocks. These are not aware of Redux.
      - Interacts with the application by means of props. Any intent of changing state is expressed by callbacks.
    - **Container Component**: These components usually provide data to the presentational components (tasks like `data fetching`, `state updates`).
      - Subscribing listeners and dispatching actions are the responsibility of container components
  - **What Redux basically does** is, it provides objects called "action" to mutate data instead of letting app. codes directly mutate the data.
  - **Redux implementation** is possible not only in React but also in Angular, Amber, jQuery or Vanilla JS. But since, it's about state, it works well with React (cause React represents UI as a function of state, and Redux emits state updates in response to actions).
  - **Dispatching actions** can be done using `store.dispatch()`, and also with `connect()`. Multiple action creators can be bound together using `bindActionCreators()`.
  - **Resolving complexities**
    - Presentational components are totally independent from Redux. They receives data via props and utilizes them. They are connected to React through Containers.
    - **Container components** are technically just React components that uses `store.subscribe()` to *read a part of the state tree* and *supply props to a presentational component it renders*.
      - All the container component needs access to the Redux store. There are two ways
        - **Manual way** is to pass store as props to all the parent components along with the target container component
        - **Redux way** is to use Redux component `<Provider>` during root component render.

            ```javascript
            <Provider store={store}>
              <App />
            </Provider>
            ```

#### Testing and method generation in redux

  - Testing libraries: [Expect](https://github.com/mjackson/expect)
  - In order to generate pure (non-mutating) method, write test case (using [Expect](https://github.com/mjackson/expect)) and *[deep freeze](https://github.com/substack/deep-freeze) arguments prior to any ops in the test case*.

### Package Management and ES6 support

#### Babel

  - **Necessity**: The new Javascript ( ES2015 / ES6 ) features are not readily available on browsers. If ES6 code is used, then it need to be compiled to the standard JS code that browsers support. Babel is the compiler that does this job.
  - **Modes**: Babel compiles new JS to old JS but provides a large scale flexibility in doing so. It supports
    - On the fly compilation
    - Pre-compilation
  - **Configuration**: Babel expects a `.babelrc` file in the project root, that includes preset and options.
  - **Installation**
  Installation can be global or local (project specific), although, global installation is discouraged (cause, different project may use different babel version which will increase the chance of a clash).
    - **Local installation**

        ```sh
        // In a npm project folder
        npm install babel
        ```

    - **Global installation**
      - `npm install -g babel`
    - **Manual compilation**
      - `babel to_convert.js -o output.js`

#### Webpack

  - An open-source JS module bundler. It receives modules with dependencies and generates static assets that represents those modules.
  - Webpack can also generate dependency graph to use in the development phases.
  - Webpack natively can only handle JS. For css support `css-loader` (to process CSS files) and `style-loader` (to apply CSS) loaders are required.
  - Webpack basic Configurations

    Installation

    ```sh
    npm install webpack -g
    ```

    Considering current project has `entry.js` (compilable) and `index.html`, where `index` expects `bundle.js` (compiled)

    The simplest build can be produced as shown below

    ```sh
    webpack ./entry.js bundle.js
    ```

    CSS support is not built-in in Webpack, so support can be achieved through `css-loader` and `style-loader` modules like this:

    Installation

    ```sh
    npm install css-loader style-loader
    ```

    Preprocessing,

    ```javascript
    require("!style!css!./style.css");
    ```

    This is a piping syntax that implies that specified CSS is to be processed through `css-loader` first and then `style-loader`.

    It is also possible to bind loaders with regex as follows,

    Method 1: CLI

    ```sh
    webpack ./entry.js bundle.js --module-bind 'css=style!css'
    ```

    Method 2: Config file

    ```javascript
    # TODO: PROVIDE EXAMPLE
    ```

  - **Configuration file**: Webpack expects a `webpack.config.js` in CWD.
  - **Auto-compilation** can be achieved through the CLI `--watch` switch.
  - **Development server support** is available which can be achieved by installing through `npm install webpack-dev-server -g` and running as ordinary CLI tools. It features,
    - Auto-compilation
    - Browser reload on server (client server) updates.

#### CommonJS

  - **CommonJS** is a runtime ecosystem specification that resolves the dependency issues of JS running on environments other than browser.
  - CommonJS provides,
    - `require()`: Which is a function that allows to import a module in the referring scope.

        ```javascript
        var SOMETHING = require("./something")
        ```

    - `module`: It's an object that allows to export something from the current scope.

        ```javascript
        module.exports = SOMETHING;
        ```

### JS primers

#### Scattered info

  - `Object.assign(target, ...sources)` method can be used to create a manipulated version of the source objects.

    ```javascript
    var src_obj = {x: 1, y: 2, z: 3};
    var ref_to_tgt = Object.assign({}, src_obj, {y: 5}, {z: 55});
    // ref_to_tgt
    // {x: 1, y: 5, z: 55};
    ```

  - `Concern seperation`: When a single function appears to be handling more than one concern, extract different functions from it to handle specific actions.

#### ES2015 Features

  Major feature includes,
  - **Arrow function and lexical this**: Arrow functions share the same lexical `this` as their surrounding code.
  - **Class**: Class supports prototype-based inheritance, super calls, instance and static methods and constructors
  - **Enhanced object literals**
  - **Template string**
  - **Destructuring**
  - **Computed object properties**
  - **`let` and `const`**

### Lot's of info

  - https://github.com/xgrommx/awesome-redux
  - https://github.com/markerikson/react-redux-links

