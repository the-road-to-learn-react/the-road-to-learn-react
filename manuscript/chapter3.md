# Getting Real with APIs

Now it's time to get real with APIs and move past sample data. If you are not familiar, I encourage you [to read my article on how I got to know APIs](https://www.robinwieruch.de/what-is-an-api-javascript/).

For our first foray into the concept, we will be using [Hacker News](https://news.ycombinator.com/), a solid news aggregator about tech topics. In this exercise, we will use the Hacker News API to fetch trending stories. There are [basic](https://github.com/HackerNews/API) and [search](https://hn.algolia.com/api) APIs to get data from the platform. Search makes sense in this application, because we want to be able to search Hacker News stories. Visit the API specification to get an understanding of the data structure.

## Lifecycle Methods

You may remember lifecycle methods were mentioned briefly in the last chapter, as a hook into the lifecycle of a React component. They can be used in ES6 class components, but not in functional stateless components. Besides the `render()` method, there are several methods that can be overridden in a React ES6 class component. All of these are the lifecycle methods.

We have already covered two lifecycle methods that can be used in an ES6 class component:

* The constructor is only called when an instance of the component is created and inserted in the DOM. The component gets instantiated in a process called mounting.
* The `render()` method is called during the mount process too, but also when the component updates. Each time the state or the props of a component changes, the `render()` method is called.

There are two more lifecycle methods when mounting a component: `getDerivedStateFromProps()` and `componentDidMount()`. The constructor is called first, `getDerivedStateFromProps()` is called before the `render()` method, and `componentDidMount()` is called after the `render()` method.

Overall, the mounting process has 4 lifecycle methods, invoked in the following order:

* constructor()
* getDerivedStateFromProps()
* render()
* componentDidMount()

For the update lifecycle of a component when the state or the props change, there are 5 lifecycle methods, in the following order:

* getDerivedStateFromProps()
* shouldComponentUpdate()
* render()
* getSnapshotBeforeUpdate()
* componentDidUpdate()

Lastly, there is the unmounting lifecycle. It has only one lifecycle method: `componentWillUnmount()`.

You don't need to know all the lifecycle methods from the beginning, and even in a large React application you'll only use a few of them besides the `constructor()` and the `render()` methods. Still, it is good to know each lifecycle method can be used for specific purposes:

* **constructor(props)** is called when the component gets initialized. You can set an initial component state and bind class methods during that lifecycle method.

* **static getDerivedStateFromProps(props, state)** is called before the `render()` lifecycle method, both on the initial mount and on the subsequent updates. It should return an object to update the state, or null to update nothing. It exists for **rare** use cases where the state depends on changes in props over time. It is important to know that this is a static method and it doesn't have access to the component instance.

* **render()** is a mandatory lifecycle method that returns elements as an output of the component. The method should be pure, so it shouldn't modify the component state. It gets an input as props and state, and returns an element.

* **componentDidMount()** is called once, when the component mounted. That's the perfect time to do an asynchronous request to fetch data from an API. The fetched data is stored in the local component state to display it in the `render()` lifecycle method.

* **shouldComponentUpdate(nextProps, nextState)** is always called when the component updates due to state or props changes. You will use it in mature React applications for performance optimization. Depending on a boolean that you return from this lifecycle method, the component and all its children will render or will not render on an update lifecycle. You can prevent the render lifecycle method of a component.

* **getSnapshotBeforeUpdate(prevProps, prevState)** is a lifecycle method, invoked before the most recently rendered output is committed to the DOM. In rare cases, the component needs to capture information from the DOM before it is potentially changed. This lifecycle method enables the component to do it. Another method (`componentDidUpdate()`) will receive any value returned by `getSnapshotBeforeUpdate()` as a parameter.

* **componentDidUpdate(prevProps, prevState, snapshot)** is a lifecycle method that is invoked immediately after updating, but not for the initial render. You can use it as to perform DOM operations or to perform more asynchronous requests. If your component implements the `getSnapshotBeforeUpdate()` method, the value it returns will be received as the `snapshot` parameter.

* **componentWillUnmount()** is called before you destroy your component. You can use this lifecycle method to perform any clean up tasks.

As you may have gathered, the `constructor()` and `render()` lifecycle methods are the most commonly used lifecycle methods for ES6 class components. The `render()` method is always required to return a component instance.

Lastly, `componentDidCatch(error, info)` was introduced in [React 16](https://www.robinwieruch.de/what-is-new-in-react-16/) as a way to catch errors in components. For instance, displaying the sample list in your application works fine, but there could be a time when a list in the local state is set to `null` by accident (e.g. when fetching the list from an external API, but the request failed and you set the local state of the list to null). It becomes impossible to filter and map the list, because it is `null` and not an empty list. The component would be broken, and the whole application would fail. Using `componentDidCatch()`, you can catch the error, store it in your local state, and show a message to the user.

### Exercises:

* Read about [lifecycle methods in React](https://reactjs.org/docs/react-component.html)
* Read about [the state related to lifecycle methods in React](https://reactjs.org/docs/state-and-lifecycle.html)
* Read about [error handling in components](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)

## Fetching Data

Now we're prepared to fetch data from the Hacker News API. There was one lifecycle method mentioned that can be used to fetch data: `componentDidMount()`. Before we use it, let's set up the URL constants and default parameters to break the URL endpoint for the API request into smaller pieces.

{title="src/App.js",lang="javascript"}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

# leanpub-start-insert
const DEFAULT_QUERY = 'redux';

const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
# leanpub-end-insert

...
~~~~~~~~

In JavaScript ES6, you can use [template literals](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals) for string concatenation or interpolation. You will use it to concatenate your URL for the API endpoint.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES6
const url = `${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${DEFAULT_QUERY}`;

// ES5
var url = PATH_BASE + PATH_SEARCH + '?' + PARAM_SEARCH + DEFAULT_QUERY;

console.log(url);
// output: https://hn.algolia.com/api/v1/search?query=redux
~~~~~~~~

This will keep your URL composition flexible in the future. Below, the entire data fetch process will be presented, and each step will be explained afterward.

{title="src/App.js",lang="javascript"}
~~~~~~~~
...

class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
# leanpub-start-insert
      result: null,
      searchTerm: DEFAULT_QUERY,
# leanpub-end-insert
    };

# leanpub-start-insert
    this.setSearchTopStories = this.setSearchTopStories.bind(this);
# leanpub-end-insert
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

# leanpub-start-insert
  setSearchTopStories(result) {
    this.setState({ result });
  }

  componentDidMount() {
    const { searchTerm } = this.state;

    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}`)
      .then(response => response.json())
      .then(result => this.setSearchTopStories(result))
      .catch(error => error);
  }
# leanpub-end-insert

  ...
}
~~~~~~~~

First, we remove the sample list of items, because we will return a real list from the Hacker News API, so the sample data is no longer used. The initial state of your component has an empty result and default search term now. The same default search term is used in the input field of the Search component, and in your first request.

Second, you use the `componentDidMount()` lifecycle method to fetch the data after the component mounted. The first fetch uses default search term from the local state. It will fetch "redux" related stories, because that is the default parameter.

Third, the native fetch API is used. The JavaScript ES6 template strings allow it to compose the URL with the `searchTerm`. The URL is the argument for the native fetch API function. The response is transformed to a JSON data structure, a mandatory step in a native fetch with JSON data structures, after which it can be set as result in the local component state. If an error occurs during the request, the function will run into the catch block instead of the then block.

Last, remember to bind your new component method in the constructor.

Now you can use the fetched data instead of the sample list. Note that the result is not only a list of data, [but a complex object with meta information and a list of hits that are news stories](https://hn.algolia.com/api). You can output the local state with `console.log(this.state);` in your `render()` method to visualize it.

In the next step, we use the result to render it. But we will prevent it from rendering anything, so we will return null, when there is no result in the first place. Once the request to the API has succeeded, the result is saved to the state and the App component will re-render with the updated state.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
# leanpub-start-insert
    const { searchTerm, result } = this.state;

    if (!result) { return null; }

# leanpub-end-insert
    return (
      <div className="page">
        ...
        <Table
# leanpub-start-insert
          list={result.hits}
# leanpub-end-insert
          pattern={searchTerm}
          onDismiss={this.onDismiss}
        />
      </div>
    );
  }
}
~~~~~~~~

Now, let's recap what happens during the component lifecycle. Your component is initialized by the constructor, after which it renders for the first time. We prevented it from displaying anything, because the result in the local state is null. It is allowed to return null for a component to display nothing. Then the `componentDidMount()` lifecycle method fetches the data from the Hacker News API asynchronously. Once the data arrives, it changes your local component state in `setSearchTopStories()`. The update lifecycle activates because the local state was updated. The component runs the `render()` method again, but this time with populated result in your local component state. The component and the Table component will be rendered with its content.

We used the native fetch API supported by most browsers to perform an asynchronous request to an API. The *create-react-app* configuration makes sure it is supported by all browsers. There are also third-party node packages that you can use to substitute the native fetch API: [axios](https://github.com/mzabriskie/axios). You will use axios later in this book.

In this book, we build on JavaScript's shorthand notation for truthfulness checks. In the previous example, `if (!result)` was used in favor of `if (result === null)`. The same applies for other cases as well. For instance, `if (!list.length)` is used in favor of `if (list.length === 0)` or `if (someString)` is used in favor of `if (someString !== '')`.

The list of hits should now be visible in our application; however, two regression bugs have appeared. First, the "Dismiss" button is broken, because it doesn't know about the complex result object, but it still operates on the plain list from the sample data when dismissing an item. Second, when the list is displayed and you try to search for something else, it gets filtered on the client-side, though the initial search was made by searching for stories on the server-side. The perfect behavior would be to fetch another result object from the API when using the Search component. Both regression bugs will be fixed in the following chapters.

### Exercises:

* Read about [ES6 template literals](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)
* Read about [the native fetch API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API)
* Read about [data fetching in React](https://www.robinwieruch.de/react-fetching-data/)

## ES6 Spread Operators

The "Dismiss" button doesn't work because the `onDismiss()` method is not aware of the complex result object. It only knows about a plain list in the local state. But it isn't a plain list anymore. Let's change it to operate on the result object instead of the list itself.

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
  const isNotId = item => item.objectID !== id;
# leanpub-start-insert
  const updatedHits = this.state.result.hits.filter(isNotId);
  this.setState({
    ...
  });
# leanpub-end-insert
}
~~~~~~~~

In `setState()`, the result is now a complex object, and the list of hits is only one of multiple properties in the object. Only the list gets updated when an item gets removed in the result object, though, while the other properties stay the same.

We could alleviate this challenge by mutating the hits in the result object. It is demonstrated below for the sake of understanding, but we'll end up using another way.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// don't do this
this.state.result.hits = updatedHits;
~~~~~~~~

As we know, React embraces immutable data structures, so we don't want to mutate an object (or mutate the state directly). We want to generate a new object based on the information given, so none of the objects get altered and we keep the immutable data structures. You will always return a new object, but never alter the original object.

For this, we use JavaScript ES6's `Object.assign()`. It takes a target object as first argument. All following arguments are source objects, which are merged into the target object. The target object can be an empty object. It embraces immutability, because no source object gets mutated.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const updatedHits = { hits: updatedHits };
const updatedResult = Object.assign({}, this.state.result, updatedHits);
~~~~~~~~

Source objects will override previously merged objects with the same property names. Let's do this on the `onDismiss()` method:

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
  const isNotId = item => item.objectID !== id;
  const updatedHits = this.state.result.hits.filter(isNotId);
  this.setState({
# leanpub-start-insert
    result: Object.assign({}, this.state.result, { hits: updatedHits })
# leanpub-end-insert
  });
}
~~~~~~~~

That's one solution, but there is a simpler way in JavaScript ES6, called the *spread operator*. The spread operator is shown with three dots: `...` When it is used, every value from an array or object gets copied to another array or object.

We'll examine the ES6 **array** spread operator even though you don't need it yet:

{title="Code Playground",lang="javascript"}
~~~~~~~~
const userList = ['Robin', 'Andrew', 'Dan'];
const additionalUser = 'Jordan';
const allUsers = [ ...userList, additionalUser ];

console.log(allUsers);
// output: ['Robin', 'Andrew', 'Dan', 'Jordan']
~~~~~~~~

The `allUsers` variable is a completely new array. The other variables `userList` and `additionalUser` stay the same. You can even merge two arrays into a new array.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const oldUsers = ['Robin', 'Andrew'];
const newUsers = ['Dan', 'Jordan'];
const allUsers = [ ...oldUsers, ...newUsers ];

console.log(allUsers);
// output: ['Robin', 'Andrew', 'Dan', 'Jordan']
~~~~~~~~

Now let's have a look at the object spread operator, which is not JavaScript ES6. It is part of a [proposal for a next JavaScript version](https://github.com/sebmarkbage/ecmascript-rest-spread) that is already being used by the React community, which is why *create-react-app* incorporated the feature in the configuration. The object spread operator works just like the JavaScript ES6 array spread operator, except with objects. Each key value pair is copied into a new object:

{title="Code Playground",lang="javascript"}
~~~~~~~~
const userNames = { firstname: 'Robin', lastname: 'Wieruch' };
const age = 28;
const user = { ...userNames, age };

console.log(user);
// output: { firstname: 'Robin', lastname: 'Wieruch', age: 28 }
~~~~~~~~

Multiple objects can be spread, as with the array spread example.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const userNames = { firstname: 'Robin', lastname: 'Wieruch' };
const userAge = { age: 28 };
const user = { ...userNames, ...userAge };

console.log(user);
// output: { firstname: 'Robin', lastname: 'Wieruch', age: 28 }
~~~~~~~~

It can be used to replace `Object.assign()`:

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
  const isNotId = item => item.objectID !== id;
  const updatedHits = this.state.result.hits.filter(isNotId);
  this.setState({
# leanpub-start-insert
    result: { ...this.state.result, hits: updatedHits }
# leanpub-end-insert
  });
}
~~~~~~~~

The "Dismiss" button should work now, because the `onDismiss()` method is aware of the complex result object, and how to update it after dismissing an item from the list.

### Exercises:

* Read about the [ES6 Object.assign()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
* Read about the [ES6 array (and object) spread operator](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Spread_operator)

## Conditional Rendering

Conditional rendering is usually introduced early in React applications, though not in our working example. It happens when you decide to render either of two elements, which sometimes is a choice between rendering an element or nothing. The simplest usage of a conditional rendering can be expressed by an if-else statement in JSX.

The `result` object in the local component state is `null` in the beginning. So far, the App component returned no elements when the `result` hasn't arrived from the API. That's already a conditional rendering, because you return earlier from the `render()` lifecycle method for a certain condition. The App component either renders nothing or its elements.

But let's go one step further. It makes more sense to wrap the Table component, which is the only component that depends on `result` in an independent conditional rendering. Everything else should be displayed, even though there is no `result` yet. You can simply use a [ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) in the JSX:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
# leanpub-start-insert
    const { searchTerm, result } = this.state;
# leanpub-end-insert
    return (
      <div className="page">
        <div className="interactions">
          <Search
            value={searchTerm}
            onChange={this.onSearchChange}
          >
            Search
          </Search>
        </div>
# leanpub-start-insert
        { result
          ? <Table
            list={result.hits}
            pattern={searchTerm}
            onDismiss={this.onDismiss}
          />
          : null
        }
# leanpub-end-insert
      </div>
    );
  }
}
~~~~~~~~

That's your second option to express a conditional rendering. A third option is the logical `&&` operator. In JavaScript a `true && 'Hello World'` always evaluates to 'Hello World'. A `false && 'Hello World'` always evaluates to false.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const result = true && 'Hello World';
console.log(result);
// output: Hello World

const result = false && 'Hello World';
console.log(result);
// output: false
~~~~~~~~

In React, you can make use of that behavior. If the condition is true, the expression after the logical `&&` operator will be the output. If the condition is false, React ignores the expression. It is applicable in the Table component's conditional rendering case, because it should either return a Table or nothing.

{title="src/App.js",lang="javascript"}
~~~~~~~~
{ result &&
  <Table
    list={result.hits}
    pattern={searchTerm}
    onDismiss={this.onDismiss}
  />
}
~~~~~~~~

These were a few approaches to use conditional rendering in React. You can read about [more alternatives in an exhaustive list of examples](https://www.robinwieruch.de/conditional-rendering-react/). Moreover, you will get to know their different uses and when to apply them.

You should be able to see the fetched data in your application by now. Everything except the Table is displayed when data fetching is pending. Once the request resolves the result and stores it into the local state, the Table is displayed because the `render()` method runs again, and the condition in the conditional rendering resolves in favor of displaying the Table component.

### Exercises:

* Read about [different ways for conditional renderings](https://www.robinwieruch.de/conditional-rendering-react/)
* Read about [React conditional rendering](https://reactjs.org/docs/conditional-rendering.html)

## Client- or Server-side Search

Now, when you use the Search component with its input field, you will filter the list. That's happening on the client-side, though. We want to use the Hacker News API to search on the server-side. Otherwise you would only deal with the first API response you got on `componentDidMount()`, the default search term parameter.

You can define an `onSearchSubmit()` method in your App component, which fetches results from the Hacker News API when executing a search in the Search component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      result: null,
      searchTerm: DEFAULT_QUERY,
    };

    this.setSearchTopStories = this.setSearchTopStories.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
# leanpub-start-insert
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
# leanpub-end-insert
    this.onDismiss = this.onDismiss.bind(this);
  }

  ...

# leanpub-start-insert
  onSearchSubmit() {
    const { searchTerm } = this.state;
  }
# leanpub-end-insert

  ...
}
~~~~~~~~

The `onSearchSubmit()` method should use the same functionality as the `componentDidMount()` lifecycle method, but this time with a modified search term from the local state and not with the initial default search term. Thus you can extract the functionality as a reusable class method.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      result: null,
      searchTerm: DEFAULT_QUERY,
    };

    this.setSearchTopStories = this.setSearchTopStories.bind(this);
# leanpub-start-insert
    this.fetchSearchTopStories = this.fetchSearchTopStories.bind(this);
# leanpub-end-insert
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

  ...

# leanpub-start-insert
  fetchSearchTopStories(searchTerm) {
    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}`)
      .then(response => response.json())
      .then(result => this.setSearchTopStories(result))
      .catch(error => error);
  }
# leanpub-end-insert

  componentDidMount() {
    const { searchTerm } = this.state;
# leanpub-start-insert
    this.fetchSearchTopStories(searchTerm);
# leanpub-end-insert
  }

  ...

  onSearchSubmit() {
    const { searchTerm } = this.state;
# leanpub-start-insert
    this.fetchSearchTopStories(searchTerm);
# leanpub-end-insert
  }

  ...
}
~~~~~~~~

Now the Search component has to add an additional button to trigger the search request explicitly. Otherwise, we'd fetch data from the Hacker News API every time the input field changes. We want to do it explicitly in a `onClick()` handler.

As an alternative, you could debounce (delay) the `onChange()` function and spare the button, but it would add more complexity, and we want to keep things simple for now.

First, pass the `onSearchSubmit()` method to your Search component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, result } = this.state;
    return (
      <div className="page">
        <div className="interactions">
          <Search
            value={searchTerm}
            onChange={this.onSearchChange}
# leanpub-start-insert
            onSubmit={this.onSearchSubmit}
# leanpub-end-insert
          >
            Search
          </Search>
        </div>
        { result &&
          <Table
            list={result.hits}
            pattern={searchTerm}
            onDismiss={this.onDismiss}
          />
        }
      </div>
    );
  }
}
~~~~~~~~

Second, introduce a button in your Search component. The button has the `type="submit"` and the form uses its `onSubmit` attribute to pass the `onSubmit()` method. You can reuse the `children` property, but this time it will be used as the content of the button.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const Search = ({
  value,
  onChange,
  onSubmit,
  children
}) =>
  <form onSubmit={onSubmit}>
    <input
      type="text"
      value={value}
      onChange={onChange}
    />
    <button type="submit">
      {children}
    </button>
  </form>
# leanpub-end-insert
~~~~~~~~

In the Table, you can remove the filter functionality, because there will be no client-side filter (search) anymore. Don't forget to remove the `isSearched()` function as well, as it won't be used anymore. Now, the result comes directly from the Hacker News API when the user clicks the "Search" button.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, result } = this.state;
    return (
      <div className="page">
        ...
        { result &&
          <Table
# leanpub-start-insert
            list={result.hits}
            onDismiss={this.onDismiss}
# leanpub-end-insert
          />
        }
      </div>
    );
  }
}

...

# leanpub-start-insert
const Table = ({ list, onDismiss }) =>
# leanpub-end-insert
  <div className="table">
# leanpub-start-insert
    {list.map(item =>
# leanpub-end-insert
      ...
    )}
  </div>
~~~~~~~~

Now when you try to search, you will notice the browser reloads. That's a native browser behavior for a submit callback in an HTML form. In React, you will often come across the `preventDefault()` event method to suppress the native browser behavior.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
onSearchSubmit(event) {
# leanpub-end-insert
  const { searchTerm } = this.state;
  this.fetchSearchTopStories(searchTerm);
# leanpub-start-insert
  event.preventDefault();
# leanpub-end-insert
}
~~~~~~~~

You should be able to search different Hacker News stories now. We have interacted with a real API, and there should be no more client-side searches.

### Exercises:

* Read about [synthetic events in React](https://reactjs.org/docs/events.html)
* Experiment with the [Hacker News API](https://hn.algolia.com/api)

## Paginated Fetch

Take a closer look at the data structure and observe how the [Hacker News API](https://hn.algolia.com/api) returns more than a list of hits. Precisely it returns a paginated list. The page property, which is `0` in the first response, can be used to fetch more paginated sublists as results. You only need to pass the next page with the same search term to the API.

Let's extend the composable API constants so it can deal with paginated data:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const DEFAULT_QUERY = 'redux';

const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
# leanpub-start-insert
const PARAM_PAGE = 'page=';
# leanpub-end-insert
~~~~~~~~

Now you can use the new constant to add the page parameter to your API request:

{title="Code Playground",lang="javascript"}
~~~~~~~~
const url = `${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${DEFAULT_QUERY}&${PARAM_PAGE}`;

console.log(url);
// output: https://hn.algolia.com/api/v1/search?query=redux&page=
~~~~~~~~

The `fetchSearchTopStories()` method will take the page as second argument. If you don't provide the second argument, it will fallback to the `0` page for the initial request. Thus the `componentDidMount()` and `onSearchSubmit()` methods fetch the first page on the first request. Every additional fetch should fetch the next page by providing the second argument.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

# leanpub-start-insert
  fetchSearchTopStories(searchTerm, page = 0) {
    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}`)
# leanpub-end-insert
      .then(response => response.json())
      .then(result => this.setSearchTopStories(result))
      .catch(error => error);
  }

  ...

}
~~~~~~~~

The page argument uses the JavaScript ES6 default parameter to introduce the fallback to page `0` in case no defined page argument is provided for the function.

Now you can use the current page from the API response in `fetchSearchTopStories()`. You can use this method in a button to fetch more stories with an `onClick` button handler. Let's use the Button to fetch more paginated data from the Hacker News API. For this, we'll define the `onClick()` handler, which takes the current search term and the next page (current page + 1).

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, result } = this.state;
# leanpub-start-insert
    const page = (result && result.page) || 0;
# leanpub-end-insert
    return (
      <div className="page">
        <div className="interactions">
        ...
        { result &&
          <Table
            list={result.hits}
            onDismiss={this.onDismiss}
          />
        }
# leanpub-start-insert
        <div className="interactions">
          <Button onClick={() => this.fetchSearchTopStories(searchTerm, page + 1)}>
            More
          </Button>
        </div>
# leanpub-end-insert
      </div>
    );
  }
}
~~~~~~~~

In your `render()` method, make sure to default to page 0 when there is no result. Remember, the `render()` method is called before the data is fetched asynchronously in the `componentDidMount()` lifecycle method.

There is still one step missing, because fetching the next page of data will override your previous page of data. We want to concatenate the old and new list of hits from the local state and new result object, so we'll adjust its functionality to add new data rather than override it.

{title="src/App.js",lang="javascript"}
~~~~~~~~
setSearchTopStories(result) {
# leanpub-start-insert
  const { hits, page } = result;

  const oldHits = page !== 0
    ? this.state.result.hits
    : [];

  const updatedHits = [
    ...oldHits,
    ...hits
  ];

  this.setState({
    result: { hits: updatedHits, page }
  });
# leanpub-end-insert
}
~~~~~~~~

A couple things happen in the `setSearchTopStories()` method now. First, you get the hits and page from the result.

Second, you have to check if there are already old hits. When the page is 0, it is a new search request from `componentDidMount()` or `onSearchSubmit()`. The hits are empty. But when you click the "More" button to fetch paginated data the page isn't 0. The old hits are already stored in your state and thus can be used.

Third, you don't want to override the old hits. You can merge old and new hits from the recent API request, which can be done with a JavaScript ES6 array spread operator.

Fourth, you set the merged hits and page in the local component state.

Now we'll make one last adjustment. When you try the "More" button it only fetches a few list items. The API URL can be extended to fetch more list items with each request, so we add even more composable path constants:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const DEFAULT_QUERY = 'redux';
# leanpub-start-insert
const DEFAULT_HPP = '100';
# leanpub-end-insert

const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
const PARAM_PAGE = 'page=';
# leanpub-start-insert
const PARAM_HPP = 'hitsPerPage=';
# leanpub-end-insert
~~~~~~~~

Now you can use the constants to extend the API URL.

{title="src/App.js",lang="javascript"}
~~~~~~~~
fetchSearchTopStories(searchTerm, page = 0) {
# leanpub-start-insert
  fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
# leanpub-end-insert
    .then(response => response.json())
    .then(result => this.setSearchTopStories(result))
    .catch(error => error);
}
~~~~~~~~

The request to the Hacker News API fetches more list items in one request than before. As you can see, a powerful API such as the Hacker News API gives plenty of ways to experiment with real world data. You should make use of it to make your endeavours when learning something new more exciting. That's [how I learned about the empowerment that APIs provide](https://www.robinwieruch.de/what-is-an-api-javascript/) when learning a new programming language or library.

### Exercises:

* Read about [ES6 default parameters](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Default_parameters)
* Experiment with the [Hacker News API parameters](https://hn.algolia.com/api)

## Client Cache

Each search submit makes a request to the Hacker News API. You might search for "redux", followed by "react" and eventually "redux" again. In total it makes 3 requests. But you searched for "redux" twice and both times it took a whole asynchronous roundtrip to fetch the data. In a client-sided cache, you would store each result. When a request to the API is made, it checks if a result is already there and uses the cache if it is. Otherwise an API request is made to fetch the data.

To have a client cache for each result, you have to store multiple `results` rather than one `result` in your local component state. The results object will be a map with the search term as key and the result as value. Each result from the API will be saved by the search term (key).

At the moment, your result in the local state looks like this:

{title="Code Playground",lang="javascript"}
~~~~~~~~
result: {
  hits: [ ... ],
  page: 2,
}
~~~~~~~~

Imagine you have made two API requests. One for the search term "redux" and another one for "react". The results object should look like the following:

{title="Code Playground",lang="javascript"}
~~~~~~~~
results: {
  redux: {
    hits: [ ... ],
    page: 2,
  },
  react: {
    hits: [ ... ],
    page: 1,
  },
  ...
}
~~~~~~~~

Let's implement a client-side cache with React `setState()`. First, rename the `result` object to `results` in the initial component state. Second, define a temporary `searchKey` to store each `result`.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
# leanpub-start-insert
      results: null,
      searchKey: '',
# leanpub-end-insert
      searchTerm: DEFAULT_QUERY,
    };

    ...

  }

  ...

}
~~~~~~~~

The `searchKey` has to be set before each request is made. It reflects the `searchTerm`. Understanding why we don't use `searchTerm` here is a crucial part to understand before continuing with the implementation. The `searchTerm` is a fluctuant variable, because it gets changed every time you type into the search input field. We want a non fluctuant variable that determines the recent submitted search term to the API and can be used to retrieve the correct result from the map of results. It is a pointer to your current result in the cache, which can be used to display the current result in the `render()` method.

{title="src/App.js",lang="javascript"}
~~~~~~~~
componentDidMount() {
  const { searchTerm } = this.state;
# leanpub-start-insert
  this.setState({ searchKey: searchTerm });
# leanpub-end-insert
  this.fetchSearchTopStories(searchTerm);
}

onSearchSubmit(event) {
  const { searchTerm } = this.state;
# leanpub-start-insert
  this.setState({ searchKey: searchTerm });
# leanpub-end-insert
  this.fetchSearchTopStories(searchTerm);
  event.preventDefault();
}
~~~~~~~~

Now we will change where the result is stored to the local component state. It should store each result by `searchKey`.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  setSearchTopStories(result) {
    const { hits, page } = result;
# leanpub-start-insert
    const { searchKey, results } = this.state;

    const oldHits = results && results[searchKey]
      ? results[searchKey].hits
      : [];
# leanpub-end-insert

    const updatedHits = [
      ...oldHits,
      ...hits
    ];

    this.setState({
# leanpub-start-insert
      results: {
        ...results,
        [searchKey]: { hits: updatedHits, page }
      }
# leanpub-end-insert
    });
  }

  ...

}
~~~~~~~~

The `searchKey` will be used as the key to save the updated hits and page in a `results` map.

First, you have to retrieve the `searchKey` from the component state. Remember that the `searchKey` gets set on `componentDidMount()` and `onSearchSubmit()`.

Second, the old hits have to get merged with the new hits as before. But this time the old hits get retrieved from the `results` map with the `searchKey` as key.

Third, a new result can be set in the `results` map in the state. Let's examine the `results` object in `setState()`.

{title="src/App.js",lang="javascript"}
~~~~~~~~
results: {
  ...results,
  [searchKey]: { hits: updatedHits, page }
}
~~~~~~~~

The bottom part makes sure to store the updated result by `searchKey` in the results map. The value is an object with a hits and page property. The `searchKey` is the search term. You already learned the `[searchKey]: ...` syntax. It is an ES6 computed property name. It helps you allocate values dynamically in an object.

The upper part needs to spread all other results by `searchKey` in the state using the object spread operator. Otherwise, you would lose all results that you have stored before.

Now you store all results by search term. That's the first step to enable your cache. In the next step, you can retrieve the result depending on the non fluctuant `searchKey` from your map of results. That's why you had to introduce the `searchKey` in the first place as non fluctuant variable. Otherwise, the retrieval would be broken when you would use the fluctuant `searchTerm` to retrieve the current result, because this value might change when we use the Search component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
# leanpub-start-insert
    const {
      searchTerm,
      results,
      searchKey
    } = this.state;

    const page = (
      results &&
      results[searchKey] &&
      results[searchKey].page
    ) || 0;

    const list = (
      results &&
      results[searchKey] &&
      results[searchKey].hits
    ) || [];

# leanpub-end-insert
    return (
      <div className="page">
        <div className="interactions">
          ...
        </div>
# leanpub-start-insert
        <Table
          list={list}
          onDismiss={this.onDismiss}
        />
# leanpub-end-insert
        <div className="interactions">
# leanpub-start-insert
          <Button onClick={() => this.fetchSearchTopStories(searchKey, page + 1)}>
# leanpub-end-insert
            More
          </Button>
        </div>
      </div>
    );
  }
}
~~~~~~~~

Since you default to an empty list when there is no result by `searchKey`, you can spare the conditional rendering for the Table component. Additionally, we need to pass the `searchKey` rather than the `searchTerm` to the "More" button. Otherwise, your paginated fetch depends on the `searchTerm` value which is fluctuant. Moreover make sure to keep the fluctuant `searchTerm` property for the input field in the Search component.

The search functionality should store all results from the Hacker News API now.

Now, we can see the `onDismiss()` method needs improvement, as it still deals with the `result` object. We want it deal with multiple `results`:

{title="src/App.js",lang="javascript"}
~~~~~~~~
  onDismiss(id) {
# leanpub-start-insert
    const { searchKey, results } = this.state;
    const { hits, page } = results[searchKey];
# leanpub-end-insert

    const isNotId = item => item.objectID !== id;
# leanpub-start-insert
    const updatedHits = hits.filter(isNotId);

    this.setState({
      results: {
        ...results,
        [searchKey]: { hits: updatedHits, page }
      }
    });
# leanpub-end-insert
  }
~~~~~~~~

Check to make sure the "Dismiss" button is working again.

Note that nothing will stop the application from sending an API request on each search submit. Though there might be already a result, there is no check that prevents the request, so the cache functionality is not complete yet. It caches the results, but it doesn't make use of them. The last step is to prevent the API request when a result is available in the cache:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {

    ...

# leanpub-start-insert
    this.needsToSearchTopStories = this.needsToSearchTopStories.bind(this);
# leanpub-end-insert
    this.setSearchTopStories = this.setSearchTopStories.bind(this);
    this.fetchSearchTopStories = this.fetchSearchTopStories.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

# leanpub-start-insert
  needsToSearchTopStories(searchTerm) {
    return !this.state.results[searchTerm];
  }
# leanpub-end-insert

  ...

  onSearchSubmit(event) {
    const { searchTerm } = this.state;
    this.setState({ searchKey: searchTerm });
# leanpub-start-insert

    if (this.needsToSearchTopStories(searchTerm)) {
      this.fetchSearchTopStories(searchTerm);
    }

# leanpub-end-insert
    event.preventDefault();
  }

  ...

}
~~~~~~~~

Now your client only makes a request to the API once, though you searched for a term twice. Even paginated data with several pages gets cached that way, because you always save the last page for each result in the `results` map. This is a powerful approach to introduce caching into an application. The Hacker News API provides you with everything you need to cache paginated data effectively.

## Error Handling

Now we've taken care of interactions with the Hacker News API. We've introduced an elegant way to cache results from the API and make use of its paginated list functionality to fetch an endless list of sublists of stories from the API. But no application is complete without error handling.

In this chapter, we introduce an efficient solution to add error handling for your application in case of an erroneous API request. We have learned the necessary building block to introduce error handling in React: local state and conditional rendering. The error is just another state, which we store in the local state and display with conditional rendering in the component.

Now, we'll implement it in the App component, since that's where we fetch data from the Hacker News API. First, introduce the error in the local state. It is initialized as null, but will be set to the error object in case of an error.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      results: null,
      searchKey: '',
      searchTerm: DEFAULT_QUERY,
# leanpub-start-insert
      error: null,
# leanpub-end-insert
    };

    ...
  }

...

}
~~~~~~~~

Second, use the catch block in your native fetch to store the error object in the local state by using `setState()`. Every time the API request isn't successful, the catch block is executed.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  fetchSearchTopStories(searchTerm, page = 0) {
    // be careful with the "\" which shows up in the PDF/print version of the book
    // it's only a line break a should not be in the actual code
    // https://github.com/the-road-to-learn-react/the-road-to-learn-react/issues/43
    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
      .then(response => response.json())
      .then(result => this.setSearchTopStories(result))
# leanpub-start-insert
      .catch(error => this.setState({ error }));
# leanpub-end-insert
  }

  ...

}
~~~~~~~~

Third, retrieve the error object from your local state in the `render()` method, and display a message in case of an error using React's conditional rendering.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const {
      searchTerm,
      results,
      searchKey,
# leanpub-start-insert
      error
# leanpub-end-insert
    } = this.state;

    ...

# leanpub-start-insert
    if (error) {
      return <p>Something went wrong.</p>;
    }
# leanpub-end-insert

    return (
      <div className="page">
        ...
      </div>
    );
  }
}
~~~~~~~~

If you want to test that your error handling is working, change the API URL to something non-existent to force an error.

{title="src/App.js",lang="javascript"}
~~~~~~~~
const PATH_BASE = 'https://hn.mydomain.com/api/v1';
~~~~~~~~

Now you should see an error message instead of your application. It's up to you where you want to place the conditional rendering for the error message. In this case, the app is overridden by the error message, but that wouldn't be the best user experience. The remaining application would still be visible so the user knows it's still running:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const {
      searchTerm,
      results,
      searchKey,
      error
    } = this.state;

    const page = (
      results &&
      results[searchKey] &&
      results[searchKey].page
    ) || 0;

    const list = (
      results &&
      results[searchKey] &&
      results[searchKey].hits
    ) || [];

    return (
      <div className="page">
        <div className="interactions">
          ...
        </div>
# leanpub-start-insert
        { error
          ? <div className="interactions">
            <p>Something went wrong.</p>
          </div>
          : <Table
            list={list}
            onDismiss={this.onDismiss}
          />
        }
# leanpub-end-insert
        ...
      </div>
    );
  }
}
~~~~~~~~

**Remember** to revert the URL for the API to the existent one:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const PATH_BASE = 'https://hn.algolia.com/api/v1';
~~~~~~~~

The application now has error handling in case the API request fails.

### Exercises:

* Read about [React's Error Handling for Components](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)

## Axios instead of Fetch

Before, we introduced the native fetch API to perform a request to the Hacker News platform. The browser allows you to use this native fetch API. However, not all browsers support this, older browsers especially. Once you start testing your application in a headless browser environment (where there is no browser, it is mocked), there can be issues with the fetch API. There are a couple of ways to make fetch work in older browsers (polyfills) and in tests ([isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch)), but these concepts are bit off-task for the purpose of learning React.

One alternative is to substitute the native fetch API with a stable library such as [axios](https://github.com/axios/axios), which performs asynchronous requests to remote APIs. In this chapter, we will discover how to substitute a library, a native API of the browser in this case, with another library.

Below, the native fetch API is substituted with axios. First, we install axios on the command line:

{title="Command Line",lang="text"}
~~~~~~~~
npm install axios
~~~~~~~~

Second, import axios in your App component's file:

{title="src/App.js",lang="javascript"}
~~~~~~~~
import React, { Component } from 'react';
# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert
import './App.css';

...
~~~~~~~~

You can use axios instead of `fetch()`, and its usage looks almost identical to the native fetch API. It takes the URL as argument and returns a promise. You don't have to transform the returned response to JSON anymore, since axios wraps the result into a `data` object in JavaScript. Just make sure to adapt your code to the returned data structure:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  fetchSearchTopStories(searchTerm, page = 0) {
# leanpub-start-insert
    axios(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
      .then(result => this.setSearchTopStories(result.data))
# leanpub-end-insert
      .catch(error => this.setState({ error }));
  }

  ...

}
~~~~~~~~

In this code, we call `axios()`, which uses an HTTP GET request by default. You can make the GET request explicit by calling `axios.get()`, or you can use another HTTP method such as HTTP POST with `axios.post()`. With these examples alone, we can see that axios is a powerful library to perform requests to remote APIs. I recommend you use it instead of the native fetch API when requests become complex, or you have to deal with promises.

There exists another improvement for the Hacker News request in the App component. Imagine the component mounts when the page is rendered for the first time in the browser. In `componentDidMount()` the component starts to make the request, but then the user navigates away from the page with this rendered component. Then the App component unmounts, but there is still a pending request from `componentDidMount()` lifecycle method. It will attempt to use `this.setState()` eventually in the `then()` or `catch()` block of the promise. You will likely see the following warning(s) on your command line, or in your browser's developer output:

* *Warning: Can only update a mounted or mounting component. This usually means you called setState, replaceState, or forceUpdate on an unmounted component. This is a no-op.*

* *Warning: Can’t call setState (or forceUpdate) on an unmounted component. This is a no-op, but it indicates a memory leak in your application. To fix, cancel all subscriptions and asynchronous tasks in the componentWillUnmount method.*

If you encounter one of them in your browser's developer tools, checkout [this walkthrough on how to prevent memory leaks in React](https://www.robinwieruch.de/react-warning-cant-call-setstate-on-an-unmounted-component/). It isn't too important for starting out with React, but I have seen many React beginners being confused about this warning.

This chapter has shown you how you can replace one library with another in React. If you run into any issues, you can use the vast library ecosystem in JavaScript to find a solution.

### Exercises:

* Read about [why frameworks matter](https://www.robinwieruch.de/why-frameworks-matter/)

{pagebreak}

Now you've learned to interact with an API in React! Let's recap the last chapter:

* **React**
  * ES6 class component lifecycle methods for different use cases
  * `componentDidMount()` for API interactions
  * Conditional renderings
  * Synthetic events on forms
  * Error handling
  * Preventing `this.setState` in unmounted components
* **ES6 and beyond**
  * Template strings to compose strings
  * Spread operator for immutable data structures
  * Computed property names
  * Class fields
* **General**
  * Hacker News API interaction
  * Native fetch browser API
  * Client and server-side search
  * Pagination of data
  * Client-side caching
  * Axios as an alternative for the native fetch API

Again, it makes sense to take a break, internalize the lessons and apply them on your own. Experiment with the parameters for the API endpoint to query different results. You can find the source code in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.3.1).
