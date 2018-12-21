# React - some reference

### The virtual DOM

React doesn't use the standard DOM (Document Object Model). That is because, the standard DOM is painful to manipulate and it really was not designed to be used for interactive and dynamic contents. This is why, it is inefficient and slow and not well suited when dynamic contents are in mind. So, Facebook went a step ahead and brought virtual DOM to the playground. Let's have a closer look.

The virtual DOM is a fast, in-memory representation of the real DOM, and it's an abstraction that allows us to treat JavaScript and DOM as if they were reactive. Let's take a look at how it works:

* Whenever the state of your data model changes, the virtual DOM and React will re-render your UI to a virtual DOM representation.
* React then calculates the difference between the two virtual DOM
representations: the previous virtual DOM representation that was computed before the data was changed and the current virtual DOM representation that was computed after the data was changed. This difference between the two virtual DOM representations is what actually needs to be changed in the real DOM.
* React updates only what needs to be updated in the real DOM.

The process of finding a difference between the two representations of the virtual DOM and re-rendering only the updated patches in a real DOM is fast. Also, the best part is, as a React developer, that you don't need to worry about what actually needs to be re-rendered. React allows you to write your code as if you were re-rendering the entire DOM every time your application's state changes.

### Routing

React-Router is a routing solution for React applications. According to react-router, React Router keeps your UI in sync with the URL. It has a simple API with powerful features like lazy code loading, dynamic route matching, and location transition handling built right in.

A typical implementation looks like this,

```javascript
import React from 'react'
import { render } from 'react-dom'
import { Router, Route, Link, browserHistory } from 'react-router'

const App = React.createClass({/*...*/})
const About = React.createClass({/*...*/})
const NoMatch = React.createClass({/*...*/})

const Users = React.createClass({
  render() {
    return (
      <div>
        <h1>Users</h1>
        <div className="master">
          <ul>
            {this.state.users.map(user => (
              <li key={user.id}><Link to={`/user/${user.id}`}>{user.name}</Link></li>
            ))}
          </ul>
        </div>
        <div className="detail">
          {this.props.children}
        </div>
      </div>
    )
  }
})

const User = React.createClass({
  componentDidMount() {
    this.setState({
      user: findUserById(this.props.params.userId)
    })
  },

  render() {
    return (
      <div>
        <h2>{this.state.user.name}</h2>
      </div>
    )
  }
})

render((
  <Router history={browserHistory}>
    <Route path="/" component={App}>
      <Route path="about" component={About}/>
      <Route path="users" component={Users}>
        <Route path="/user/:userId" component={User}/>
      </Route>
      <Route path="*" component={NoMatch}/>
    </Route>
  </Router>
), document.getElementById('root'))
```

*History*: a history knows how to listen to the browser's address bar for changes and parses the URL into a location object that the router can use to match routes and render the correct set of components.

There are three types of histories you'll come across most often, but note that anyone can build a custom history implementation for consumption with React Router.

* `browserHistory`
* `hashHistory`
* `createMemoryHistory`

They are passed into <Router>

```javascript
render(
  <Router history={browserHistory} routes={routes} />,
  document.getElementById('app')
)
```

* `browserHistory` Browser history is the recommended history for browser application with React Router. It uses the History API built into the browser to manipulate the URL, creating real URLs that look like `example.com/some/path`.
* `hashHistory` Hash history uses the hash (`#`) portion of the URL, creating routes that look like `example.com/#/some/path`.
* `createMemoryHistory` Memory history doesn't manipulate or read from the address bar. This is how we implement server rendering. It's also useful for testing and other rendering environments (like React Native).

It's a bit different than the other two histories because you have to create one, it is this way to facilitate testing:

```javascript
const history = createMemoryHistory(location)
```

*Dynamic Routing*: A router is the perfect place to handle code splitting: it's responsible for setting up your views.

React Router does all of its path matching and component fetching asynchronously, which allows you to not only load up the components lazily, but also lazily load the route configuration. You really only need one route definition in your initial bundle, the router can resolve the rest on demand.

Routes may define `getChildRoutes`, `getIndexRoute`, and `getComponents` methods. These are asynchronous and only called when needed. We call it "gradual matching". React Router will gradually match the URL and fetch only the amount of route configuration and components it needs to match the URL and render.

### Testing

Facebook has built its own unit test framework for JavaScript called
Jest. It is built on top of Jasmine; another well-known JavaScript test framework. Jest automatically mocks the dependencies when tests are run. It automatically finds tests to be executed in the repository. Let's test this module

```javascript
function getListOfUnitIds(units) {
  return Object.keys(units);
}

module.exports.getListOfUnitIds = getListOfUnitIds;
```

A Jest test module for the module above should look like this,

```javascript
describe('Unit module', function () {
  it('returns an array of unit ids', function () {
    var UnitUtils = require('./Unit');
    var unitsMock = {
      unit1: {},
      unit2: {},
      unit3: {}
    };
    var expectedListOfUnitIds = [ 'unit1', 'unit2', 'unit3' ];
    var actualListOfUnitIds = UnitUtils.
      getListOfUnitIds(unitsMock);
    expect(actualListOfUnitIds).toEqual(expectedListOfUnitIds);
  });
});
```

The describe function defines a suit and takes these two parameters:
* Suit name: This is the title that describes what is being tested by this 'Unit module' suit
* Suit implementation: This is the function that implements this suit

In Jest, individual tests are called specs.
They are defined by calling another global `it()` Jest function. Just like `describe()`,
the `it()` function takes two parameters:
* Spec name: This is the title that describes what is being tested by this 'returns an array of unit ids' spec
* Spec implementation: This is the function that implements this spec

