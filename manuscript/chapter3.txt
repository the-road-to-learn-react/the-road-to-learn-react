# Getting Real with an API

Now it's time to get real with an API. In the end it can get boring to deal with artificial data. Do you know [Hacker News](https://news.ycombinator.com/)? It's a great news aggregator. You will use the Hacker News API to fetch trending stories from the platform. There is a [basic](https://github.com/HackerNews/API) and [search](https://hn.algolia.com/api) API. The latter one makes sense in your case to search stories on Hacker News. You can visit the [API specification](https://hn.algolia.com/api) to get a glimpse of the data structure.

## Lifecycle Methods

You will need the knowledge about React lifecycle methods before you can start to fetch data. These methods are a hook into the lifecycle of a React component which you can overwrite. There are a few to learn.

You already know two lifecycle methods in your ES6 class component: `constructor()` and `render()`.

The constructor is only called when an instance of the component is created and inserted in the DOM. That process is called mounting of the component.

The `render()` method is called during the mount process too, but also when the component updates. Each time when the state of a component changes the `render()` method is called. It also renders when there are incoming props from the component above.

Now you know two lifecycle methods and when they are called. You already used them as well. But there are more of them.

The mounting of a component has two more lifecycle methods: `componentWillMount()` and `componentDidMount()`. The constructor is called first, `componentWillMount()` gets called before the `render()` method, and `componentDidMount()` is called after the `render()` method.

Overall the mounting process has 4 lifecycle methods. They are invoked in the following order:

* constructor()
* componentWillMount()
* render()
* componentDidMount()

But what about the update lifecycle of a component? Overall it has 5 lifecycle methods in the following order:

* componentWillReceiveProps()
* shouldComponentUpdate()
* componentWillUpdate()
* render()
* componentDidUpdate()

You don't need to know all of them from the beginning. But still it is good to know that each lifecycle method can be used for specific use cases:

* componentDidMount() method can be used to get data from an API when the component mounts
* shouldComponentUpdate() can be used in a mature React app to prevent a component from updating for performance optimizations

So far you know that there is a mounting and updating lifecycle. But every lifecycle ends at some time. The third lifecycle is the unmounting lifecycle. It has only one lifecycle method: `componentWillUnmount()`. It is used to cleanup stuff when you are about to destroy your component.

The `constructor()` and `render()` lifecycle methods are already used by you. These are the commonly used lifecycle methods for ES6 class components. Actually the `render()` method is required - otherwise you wouldn't return a component instance.

### Exercises:

* read more about [lifecycle methods in React](https://facebook.github.io/react/docs/react-component.html)
* read more about [the state and lifecycle in React](https://facebook.github.io/react/docs/state-and-lifecycle.html)

## Fetch Data from an API

Now you are prepared to fetch data from the Hacker News API. I mentioned one lifecycle method that can be used to fetch data: componentDidMount(). Before we can use it, let's set up the url constants and default parameters to breakup the API request into chunks.

{lang=javascript}
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

In ES6 JavaScript you can use [template strings](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals) to concatenate strings. You will use it to concatenate your url for the API endpoint.

{lang=javascript}
~~~~~~~~
// ES6
var url = `${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${DEFAULT_QUERY}`;

// ES5
var url = PATH_BASE + PATH_SEARCH + '?' + PARAM_SEARCH + DEFAULT_QUERY;

// output
console.log(url);
// https://hn.algolia.com/api/v1/search?query=redux
~~~~~~~~

That will keep your url composition flexible in the future.

But let's get to the API fetch where we use the url. The whole data fetch process will be presented at once, but each step will get explained afterwards.

{lang=javascript}
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
    this.setSearchTopstories = this.setSearchTopstories.bind(this);
    this.fetchSearchTopstories = this.fetchSearchTopstories.bind(this);
# leanpub-end-insert
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

# leanpub-start-insert
  setSearchTopstories(result) {
    this.setState({ result });
  }

  fetchSearchTopstories(searchTerm) {
    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}`)
      .then(response => response.json())
      .then(result => this.setSearchTopstories(result));
  }

  componentDidMount() {
    const { searchTerm } = this.state;
    this.fetchSearchTopstories(searchTerm);
  }
# leanpub-end-insert

  ...
}
~~~~~~~~

A lot of things happened. I thought about it to break it into smaller pieces. Then again it would be difficult to grasp the relations of each piece to each other. Let me explain each step in detail.

First you can remove the artificial list of items, because you return a result from the Hacker News API. The initial state of your component has an empty result and default search term. The same default search term is used in the search field and in your first request.

Second you use the `componentDidMount()` lifecycle method to fetch the data after the component did mount. In the very first fetch the default search term from the component state is used. It will fetch "redux" related stories, because that is the default parameter.

Third the native fetch is used. ES6 string template strings allow it to compose the url with the `searchTerm`. The url is the argument for the native fetch API function. The response needs to get transformed to Json and can finally be set in the internal component state.

Don't forget to bind your new component methods.

Now you can use the fetched data instead of the artificial list of items. However you have to be careful again. The result is not only a list of data. [It's a complex object with meta information and a list of hits (stories).](https://hn.algolia.com/api) You can output the internal state with `console.log(this.state);` in your `render()` method to visualize it.

Let's use the result and render it. But we will prevent to render anything - return null - when there is no result. Once the request to the API succeeded, the result is saved to the state, the App component will render the component instance.

{lang=javascript}
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

Let's recap what happens during the component lifecycle. Your component gets initialized by the constructor. After that it renders for the first time. But we prevent to display it, because the result is empty. Then the `componentDidMount()` lifecycle method runs. In that method you fetch the data from the Hacker News API asynchronously. Once the data arrives, it changes your internal component state. After that the update lifecycle comes into play. The component runs the `render()` method again, but this time with populated data in your internal component state. The component and thus the Table gets rendered.

The list of hits should be visible now. But the "Dismiss" button is broken. We will fix that soon.

### Exercises:

* read more about [ES6 template strings](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)
* read more about [the native fetch API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API)
* experiment with the [Hacker News API](https://hn.algolia.com/api)

## ES6 spread operator

The "Dismiss" button doesn't work because the `onDismiss()` method is not aware of the result. Let's change that:

{lang=javascript}
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

But what happens in `setState()` now? Unfortunately the result is a complex object. The list of hits is only one of multiple properties in the object. However only the list gets updated - when an item gets removed - in the result object while the other properties stay the same.

One approach could be to mutate the hits in the result object. I will demonstrate it, but we won't do it that way.

{lang=javascript}
~~~~~~~~
this.state.result.hits = updatedHits;
~~~~~~~~

React embraces functional programming. Thus you shouldn't mutate an object (or mutate the state directly). A better approach is to generate a new object based on the old object and updated object. Thereby none of the objects get altered. These data structures in React are called immutable data structures. They always return a new object and never alter an object.

Let's do it in JavaScript ES5. `Object.assign()` takes as first argument a target object. All following arguments are source objects. These objects are merged into the target object. The target object can be an empty object - it embraces immutability, because no source objects get mutated. It would look similar to the following:

{lang=javascript}
~~~~~~~~
const updatedHits = { hits: updatedHits };
const updatedResult = Object.assign({}, this.state.result, updatedHits);
~~~~~~~~

Now let's do it in the `onDismiss()` method:

{lang=javascript}
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

That's it in ES5. There is a simpler solution in ES6 and future JavaScript releases. May I introduce the spread operator to you? It only consists of three dots: ... When it is used, every value from an array or object gets copied to another array or object.

Let's examine the ES6 **array** spread operator even though you don't need it yet.

{lang=javascript}
~~~~~~~~
const userList = ['Robin', 'Andrew', 'Dan'];
const additionalUser = 'Jordan';
const allUsers = [ ...userList, additionalUser ];

console.log(allUsers);
// ['Robin', 'Andrew', 'Dan', 'Jordan']
~~~~~~~~

The `allUsers` variable is a completely new array. The other variables `userList` and `additionalUser` are the same. You can even merge two arrays that way into a new array.

{lang=javascript}
~~~~~~~~
const oldUsers = ['Robin', 'Andrew'];
const newUsers = ['Dan', 'Jordan'];
const allUsers = [ ...oldUsers, ...newUsers ];

console.log(allUsers);
// ['Robin', 'Andrew', 'Dan', 'Jordan']
~~~~~~~~

Now let's have a look at the object spread operator. It is not ES6! It is a [proposal for a future ES version](https://github.com/sebmarkbage/ecmascript-rest-spread) yet already used by the React community. That's why create-react-app incorporated the feature in the configuration.

Basically it is the same as the ES6 array spread operator but with objects. It copies each key value pair into a new object.

{lang=javascript}
~~~~~~~~
const userNames = { firstname: 'Robin', lastname: 'Wieruch' };
const age = 28;
const user = { ...userNames, age };

console.log(user);
// { firstname: 'Robin', lastname: 'Wieruch', age: 28 }
~~~~~~~~

Multiple objects can be spread like in the array spread example.

{lang=javascript}
~~~~~~~~
const userNames = { firstname: 'Robin', lastname: 'Wieruch' };
const userAge = { age: 28 };
const user = { ...userNames, ...userAge };

console.log(user);
// { firstname: 'Robin', lastname: 'Wieruch', age: 28 }
~~~~~~~~

After all it can be used to replace ES5 `Object.assign()`.

{lang=javascript}
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

The "Dismiss" button should work again.

### Exercises:

* read more about [Object.assign()](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
* read more about the [ES6 array spread operator](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Spread_operator)
  * the object spread operator is briefly mentioned

## Conditional Rendering

The conditional rendering is introduced pretty early in React applications. It happens when you want to make a decision to render either one or another element. Sometimes it means to render an element or nothing. After all a conditional rendering can be expressed by a if-else condition in JSX.

The result in the internal component state is null in the beginning. So far the App component returned no component instance when the result hasn't arrived from the API. That's already a conditional rendering! But it makes more sense to wrap the Table component - which depends solely on the result - in an independent conditional rendering. Everything else should be displayed even though there is no result yet. You can simply use a ternary expression in your JSX.

{lang=javascript}
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

That's only one option to express a conditional rendering. Another option is the logical `&&` operator. In JavaScript a `true && 'Hello World'` always evaluates to 'Hello World'. A `false && 'Hello World'` always evaluates to false.

In React you can see it similar. If the condition is true, the expression right after && will be the output. If the condition is false, React ignores and skips the expression. It is applicable in the Table conditional rendering case, because it should return a Table or nothing.

{lang=javascript}
~~~~~~~~
{ result &&
  <Table
    list={result.hits}
    pattern={searchTerm}
    onDismiss={this.onDismiss}
  />
}
~~~~~~~~

These were a few approaches to use conditional rendering in React. After all you should be able to see the fetched data in your list. Everything except the Table is displayed when the data fetching is pending.

### Exercises:

* read more about [React conditional rendering](https://facebook.github.io/react/docs/conditional-rendering.html)

## Client- or Server-side Interaction: Search

When you use the search input field now, you will filter the list. That's happening on the client-side though. Now you are going to use the Hacker News API to search on the server-side. Otherwise you would deal only with the first API response which you got on `componentDidMount()` with the default search term parameter.

You can define an `onSubmit()` method in your ES6 class component, which fetches results from the Hacker News API. It will be the same fetch like in your `componentDidMount()` lifecycle method. But it fetches it with the modified search term from the search field input.

{lang=javascript}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      result: null,
      searchTerm: DEFAULT_QUERY,
    };

    this.setSearchTopstories = this.setSearchTopstories.bind(this);
    this.fetchSearchTopstories = this.fetchSearchTopstories.bind(this);
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
    this.fetchSearchTopstories(searchTerm);
  }
# leanpub-end-insert

  ...
}
~~~~~~~~

The Search component gets extended with a button. The button has to explicitly trigger the search. Otherwise you would fetch data every time from the Hacker News API when your input changes. As alternative you could debounce (delay) the `onChange()` function and spare the button, but it would add more complexity at this time. Let's keep it without debounce.

First pass the `onSearchSubmit()` method to your Search component.

{lang=javascript}
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

Second introduce a button in your Search component. The button has the `type="submit"` and the form uses its `onSubmit()` attribute to pass the `onSubmit()` method. You can reuse the children property, but this time in the button.

{lang=javascript}
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

In the Table you can remove the filter functionality, because there will be no client-side filter anymore. The result comes directly from the Hacker News API after you have clicked the Search button.

{lang=javascript}
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
    { list.map(item =>
# leanpub-end-insert
      ...
    )}
  </div>
~~~~~~~~

When you try to search now, you will experience that the browser reloads. That's a native browser behavior of submit in a form. In React you will often come across the `preventDefault()` event method to suppress the native browser behavior.

{lang=javascript}
~~~~~~~~
# leanpub-start-insert
onSearchSubmit(event) {
# leanpub-end-insert
  const { searchTerm } = this.state;
  this.fetchSearchTopstories(searchTerm);
# leanpub-start-insert
  event.preventDefault();
# leanpub-end-insert
}
~~~~~~~~

Now you should be able to search different Hacker News stories. You interact with a real world API. There should be no client-sided search anymore.

### Exercises:

* read more about [synthetic events in React](https://facebook.github.io/react/docs/events.html)

## Paginated Fetch

Did you have a closer look at the returned data structure yet? The [Hacker News API](https://hn.algolia.com/api) returns more than a list of hits. The page property - which is 0 in the first response - can be used to fetch more paginated data. You only need to pass the next page with the same search term to the API.

First let's extend the composeable API constants that it can deal with pages of data.

{lang=javascript}
~~~~~~~~
const DEFAULT_QUERY = 'redux';
# leanpub-start-insert
const DEFAULT_PAGE = 0;
# leanpub-end-insert

const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
# leanpub-start-insert
const PARAM_PAGE = 'page=';
# leanpub-end-insert
~~~~~~~~

Now you can use these constants to add the page parameter to your API request.

{lang=javascript}
~~~~~~~~
var url = `${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}`;

// output
console.log(url);
// https://hn.algolia.com/api/v1/search?query=redux&page=
~~~~~~~~

The `fetchSearchTopstories()` method will take the page as second argument. The `componentDidMount()` and `onSearchSubmit()` methods take the `DEFAULT_PAGE` for the initial API calls. They should fetch the first page on the first load. Every additional fetch should fetch the next page.

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  componentDidMount() {
    const { searchTerm } = this.state;
# leanpub-start-insert
    this.fetchSearchTopstories(searchTerm, DEFAULT_PAGE);
# leanpub-end-insert
  }

  # leanpub-start-insert
  fetchSearchTopstories(searchTerm, page) {
    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}`)
# leanpub-end-insert
      .then(response => response.json())
      .then(result => this.setSearchTopstories(result));
  }

  onSearchSubmit(event) {
    const { searchTerm } = this.state;
# leanpub-start-insert
    this.fetchSearchTopstories(searchTerm, DEFAULT_PAGE);
# leanpub-end-insert
    event.preventDefault();
  }

  ...

}
~~~~~~~~

Now you can use the current page from the API response in `fetchSearchTopstories()`. You want to this method in a button. Let's use the Button to fetch more paginated data from the Hacker News API. You only need to define the `onClick()` function which takes the current search term and the current page + 1. The result will be the next page.

{lang=javascript}
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
          <Button onClick={() => this.fetchSearchTopstories(searchTerm, page + 1)}>
            More
          </Button>
        </div>
# leanpub-end-insert
      </div>
    );
  }
}
~~~~~~~~

Make sure to default to page 0 when there is no result.

There is one step missing. You fetch the next page of data, but it will overwrite your old data. You want to concatenate the old and new data. Let's adjust the functionality to add the new data rather than to overwrite it.

{lang=javascript}
~~~~~~~~
setSearchTopstories(result) {
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

First you get the hits and page from the result.

Second you have to check if there are already old hits. When the page is 0, it is a new search request from `componentDidMount()` or `onSearchSubmit()`. The hits are empty. But when you click the "More" button to fetch paginated data the page isn't 0. It is the next page. The old hits are already stored in your state and thus can be used.

Third you don't want to overwrite the old hits. You can merge old and new hits from the recent API request. The merge of both lists can be done with the ES6 array spread operator.

Fourth you set the merged hits and page in the internal component state.

You can make one last adjustment. When you try the "More" button it only fetches a few items. The API url can be extended to fetch more data with each request. Again you can add more composeable path constants.

{lang=javascript}
~~~~~~~~
const DEFAULT_QUERY = 'redux';
const DEFAULT_PAGE = 0;
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

Now you can use the constants to extend the API url.

{lang=javascript}
~~~~~~~~
fetchSearchTopstories(searchTerm, page) {
# leanpub-start-insert
  fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
# leanpub-end-insert
    .then(response => response.json())
    .then(result => this.setSearchTopstories(result));
}
~~~~~~~~

Afterwards the request to the Hacker News API fetches more data than before.

### Exercises:

* experiment with the [Hacker News API parameters](https://hn.algolia.com/api)

## Client Cache

Each search submit makes a request to the Hacker News API. You might search for "redux", followed by "react" and eventually "redux" again. In total it makes 3 requests. But you searched for "redux" twice and both times it took a whole asynchronous roundtrip to fetch the data. In a client-sided cache you would store each result. When a request to the API is made, it checks if a result is already there. If it is there, the cache is used. Otherwise an API request is made to fetch the data.

In order to have a client cache for each result, you have to store multiple `results` rather than one `result` in your internal component state. The results object will be a map with the search term as key and the result as value. Each result from the API will be saved by search term (key).

At the moment your result in the component state looks similar to the following:

{lang=javascript}
~~~~~~~~
result: {
  hits: [ ... ],
  page: 2,
}
~~~~~~~~

Imagine you have made two API requests. One for the search term "redux" and another one for "react". The results map should look like the following:

{lang=javascript}
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

Let's implement a client-side cache with React `setState()`. First rename the result object to results in the initial component state. Second define a temporary `searchKey` which is used to store each result.

{lang=javascript}
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

The `searchKey` has to be set before each request is made. It reflects the `searchTerm`. But why don't we use the `searchTerm` in the first place? The `searchTerm` is a fluctuant variable, because it get changed every time you type into the Search input field. However in the end you will need a non fluctuant variable. It determines the recent submitted search term to the API and can be used to retrieve the correct result from the map of results.

{lang=javascript}
~~~~~~~~
componentDidMount() {
  const { searchTerm } = this.state;
# leanpub-start-insert
  this.setState({ searchKey: searchTerm });
# leanpub-end-insert
  this.fetchSearchTopstories(searchTerm, DEFAULT_PAGE);
}

onSearchSubmit(event) {
  const { searchTerm } = this.state;
# leanpub-start-insert
  this.setState({ searchKey: searchTerm });
# leanpub-end-insert
  this.fetchSearchTopstories(searchTerm, DEFAULT_PAGE);
  event.preventDefault();
}
~~~~~~~~

Now you have to adjust the functionality where the result is stored to the internal component state. It should store each result by `searchKey`.

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  setSearchTopstories(result) {
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

The `searchKey` will be used as key to save the updated hits and page in a results map.

First you have to retrieve the `searchKey` from the component state. Remember that the `searchKey` gets set on `componentDidMount()` and `onSearchSubmit()`.

Second the old hits have to get merged with the new hits as before. But this time the old hits get retrieved from the results map with the `searchKey` as key.

Third a new result can be set in the results map in the state. Let's examine the results object in `setState()`.

{lang=javascript}
~~~~~~~~
results: {
  ...results,
  [searchKey]: { hits: updatedHits, page }
}
~~~~~~~~

The bottom part makes sure to store the updated result by `searchKey` in the results map. The value is an object with a hits and page property. The `searchKey` is the search term. You already learned the `[searchKey]` syntax. It is an ES6 computed property name. It helps you to allocate values dynamically in an object.

The upper part needs to object spread all other results by `searchKey` in the state. Otherwise you would lose all results you stored before.

Now you store all results by search term. That's the first step to enable your cache. In the next step you can retrieve the result depending on the search term from your map of results.

{lang=javascript}
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
          <Search
            value={searchTerm}
            onChange={this.onSearchChange}
            onSubmit={this.onSearchSubmit}
          >
            Search
          </Search>
        </div>
# leanpub-start-insert
        <Table
          list={list}
          onDismiss={this.onDismiss}
        />
# leanpub-end-insert
        <div className="interactions">
# leanpub-start-insert
          <Button onClick={() => this.fetchSearchTopstories(searchKey, page + 1)}>
# leanpub-end-insert
            More
          </Button>
        </div>
      </div>
    );
  }
}
~~~~~~~~

Since you default to an empty list when there is no result by `searchKey`, you can spare the conditional rendering for the Table component. Additionally you will need to pass the `searchKey` rather than the `searchTerm` to the "More" button. Otherwise your paginated fetch depends on the `searchTerm` value that is fluctuant. Moreover make sure to keep the fluctuant `searchTerm` property for the input field in the "Search" component.

The search functionality should work again. It stores all results from the Hacker News API.

Additionally the `onDismiss()` method needs to get improved. It still deals with the result object. Now it has to deal with multiple results.

{lang=javascript}
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

The "Dismiss" button should work again.

However, nothing stops the app from sending an API request on each submit. Even though there might be already a result, there is no check that prevents the request. The cache functionality is not complete yet. The last step would be to prevent the call when a result is available in the state.

{lang=javascript}
~~~~~~~~
class App extends Component {

  constructor(props) {

    ...

# leanpub-start-insert
    this.needsToSearchTopstories = this.needsToSearchTopstories.bind(this);
# leanpub-end-insert
    this.setSearchTopstories = this.setSearchTopstories.bind(this);
    this.fetchSearchTopstories = this.fetchSearchTopstories.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

# leanpub-start-insert
  needsToSearchTopstories(searchTerm) {
    return !this.state.results[searchTerm];
  }
# leanpub-end-insert

  onSearchSubmit(event) {
    const { searchTerm } = this.state;
    this.setState({ searchKey: searchTerm });
# leanpub-start-insert

    if (this.needsToSearchTopstories(searchTerm)) {
      this.fetchSearchTopstories(searchTerm, DEFAULT_PAGE);
    }

# leanpub-end-insert
    event.preventDefault();
  }

  ...

}
~~~~~~~~

Now your client makes a request to the API only once although you search for a search term twice. Even paginated data with several pages gets cached that way, because you always save the last page for each result in the results map.

{pagebreak}

Your *src/App.js* should look like the following by now:

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

const DEFAULT_QUERY = 'redux';
const DEFAULT_PAGE = 0;
const DEFAULT_HPP = '100';

const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
const PARAM_PAGE = 'page=';
const PARAM_HPP = 'hitsPerPage=';

class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      results: null,
      searchKey: '',
      searchTerm: DEFAULT_QUERY,
    };

    this.needsToSearchTopstories = this.needsToSearchTopstories.bind(this);
    this.setSearchTopstories = this.setSearchTopstories.bind(this);
    this.fetchSearchTopstories = this.fetchSearchTopstories.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

  componentDidMount() {
    const { searchTerm } = this.state;
    this.setState({ searchKey: searchTerm });
    this.fetchSearchTopstories(searchTerm, DEFAULT_PAGE);
  }

  setSearchTopstories(result) {
    const { hits, page } = result;
    const { searchKey, results } = this.state;

    const oldHits = results && results[searchKey]
      ? results[searchKey].hits
      : [];

    const updatedHits = [
      ...oldHits,
      ...hits
    ];

    this.setState({
      results: {
        ...results,
        [searchKey]: { hits: updatedHits, page }
      }
    });
  }

  fetchSearchTopstories(searchTerm, page) {
    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
      .then(response => response.json())
      .then(result => this.setSearchTopstories(result));
  }

  needsToSearchTopstories(searchTerm) {
    return !this.state.results[searchTerm];
  }

  onSearchChange(event) {
    this.setState({ searchTerm: event.target.value });
  }

  onSearchSubmit(event) {
    const { searchTerm } = this.state;
    this.setState({ searchKey: searchTerm });

    if (this.needsToSearchTopstories(searchTerm)) {
      this.fetchSearchTopstories(searchTerm, DEFAULT_PAGE);
    }

    event.preventDefault();
  }

  onDismiss(id) {
    const { searchKey, results } = this.state;
    const { hits, page } = results[searchKey];

    const isNotId = item => item.objectID !== id;
    const updatedHits = hits.filter(isNotId);

    this.setState({
      results: {
        ...results,
        [searchKey]: { hits: updatedHits, page }
      }
    });
  }

  render() {
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

    return (
      <div className="page">
        <div className="interactions">
          <Search
            value={searchTerm}
            onChange={this.onSearchChange}
            onSubmit={this.onSearchSubmit}
          >
            Search
          </Search>
        </div>
        <Table
          list={list}
          onDismiss={this.onDismiss}
        />
        <div className="interactions">
          <Button onClick={() => this.fetchSearchTopstories(searchKey, page + 1)}>
            More
          </Button>
        </div>
      </div>
    );
  }
}

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

const Table = ({ list, onDismiss }) =>
  <div className="table">
    { list.map(item =>
      <div key={item.objectID} className="table-row">
        <span style={{ width: '40%' }}>
          <a href={item.url}>{item.title}</a>
        </span>
        <span style={{ width: '30%' }}>
          {item.author}
        </span>
        <span style={{ width: '10%' }}>
          {item.num_comments}
        </span>
        <span style={{ width: '10%' }}>
          {item.points}
        </span>
        <span style={{ width: '10%' }}>
          <Button
            onClick={() => onDismiss(item.objectID)}
            className="button-inline"
          >
            Dismiss
          </Button>
        </span>
      </div>
    )}
  </div>

const Button = ({ onClick, className = '', children }) =>
  <button
    onClick={onClick}
    className={className}
    type="button"
  >
    {children}
  </button>

export default App;
~~~~~~~~

You have learned to interact with an API in React! Let's recap the last chapters:

* React
  * ES6 class component lifecycle methods for different use cases
  * componentDidMount() for API interactions
  * conditional renderings
  * synthetic events on forms
* ES6
  * template strings
  * spread operator
  * computed property names
* General
  * Hacker News API interaction
  * native fetch browser API
  * client- and server-side search
  * pagination of data
  * client-side caching

Again it makes sense to take a break. Internalize the learnings and apply them on your own. You can experiment with the source code you have written so far.
