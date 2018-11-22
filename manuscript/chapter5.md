# Advanced React Components

This chapter focuses on implementing advanced React components. You will learn how to implement higher-order components, and we will dive into more advanced topics in React.

## Ref a DOM Element

Sometimes you need to interact with your DOM nodes in React. The `ref` attribute gives you access to a node in your elements. That is usually an anti pattern in React, because you should use its declarative way of doing things and its unidirectional data flow. We learned about it when we introduced the first search input field, but there are certain cases where you need access to the DOM node. The official documentation mentions three:

* to use the DOM API (focus, media playback etc.)
* to invoke imperative DOM node animations
* to integrate with a third-party library that needs the DOM node (e.g. [D3.js](https://d3js.org/))

We'll use the Search component as an example. When the application renders for the first time, the input field should be focused. This is one case where we need access to the DOM API. The `ref` attribute can be used in both functional stateless components and ES6 class components. In this example, we need a lifecycle method, so the approach is showcased using the `ref` attribute with an ES6 class component.

The initial step is to refactor the functional stateless component to an ES6 class component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
class Search extends Component {
  render() {
    const {
      value,
      onChange,
      onSubmit,
      children
    } = this.props;

    return (
# leanpub-end-insert
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
# leanpub-start-insert
    );
  }
}
# leanpub-end-insert
~~~~~~~~

The `this` object of an ES6 class component helps us to reference the DOM element with the `ref` attribute.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Search extends Component {
  render() {
    const {
      value,
      onChange,
      onSubmit,
      children
    } = this.props;

    return (
      <form onSubmit={onSubmit}>
        <input
          type="text"
          value={value}
          onChange={onChange}
# leanpub-start-insert
          ref={el => this.input = el}
# leanpub-end-insert
        />
        <button type="submit">
          {children}
        </button>
      </form>
    );
  }
}
~~~~~~~~

Now you can focus the input field when the component mounted by using the `this` object, the appropriate lifecycle method, and the DOM API.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Search extends Component {
# leanpub-start-insert
  componentDidMount() {
    if (this.input) {
      this.input.focus();
    }
  }
# leanpub-end-insert

  render() {
    const {
      value,
      onChange,
      onSubmit,
      children
    } = this.props;

    return (
      <form onSubmit={onSubmit}>
        <input
          type="text"
          value={value}
          onChange={onChange}
          ref={el => this.input = el}
        />
        <button type="submit">
          {children}
        </button>
      </form>
    );
  }
}
~~~~~~~~

The input field should be focused when the application renders. We access to the `ref` in a functional stateless component without the `this` object using the following functional stateless component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Search = ({
  value,
  onChange,
  onSubmit,
  children
}) => {
# leanpub-start-insert
  let input;
# leanpub-end-insert
  return (
    <form onSubmit={onSubmit}>
      <input
        type="text"
        value={value}
        onChange={onChange}
# leanpub-start-insert
        ref={el => this.input = el}
# leanpub-end-insert
      />
      <button type="submit">
        {children}
      </button>
    </form>
  );
}
~~~~~~~~

Now we can access the input DOM element. In our case it wouldn't help much since there's no lifecycle method in a functional stateless component to trigger the focus. So we are not going to make use of the input variable here. In the future, though, you may encounter cases where it makes sense to use a functional stateless component with the `ref` attribute.

### Exercises

* Read about [the usage of the ref attribute in React](https://www.robinwieruch.de/react-ref-attribute-dom-node/)
* Read about [the ref attribute in general in React](https://reactjs.org/docs/refs-and-the-dom.html)

## Loading ...

Now we get back to the application, where we'll show a loading indicator when a search request submits to the Hacker News API. The request is asynchronous, so you should show your user feedback that something is happening. Let's define a reusable Loading component in your *src/App.js* file.

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Loading = () =>
  <div>Loading ...</div>
~~~~~~~~

Now we need a property to store the loading state. Based on the loading state, we can decide to show the Loading component later.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {
  _isMounted = false;

  constructor(props) {
    super(props);

    this.state = {
      results: null,
      searchKey: '',
      searchTerm: DEFAULT_QUERY,
      error: null,
# leanpub-start-insert
      isLoading: false,
# leanpub-end-insert
    };

    ...
  }

  ...

}
~~~~~~~~

The initial value of that `isLoading` property is false. We don't load anything before the App component is mounted. When the request is made, the loading state is set to true. The request will succeed eventually, and you can set the loading state to false.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  setSearchTopStories(result) {
    ...

    this.setState({
      results: {
        ...results,
        [searchKey]: { hits: updatedHits, page }
      },
# leanpub-start-insert
      isLoading: false
# leanpub-end-insert
    });
  }

  fetchSearchTopStories(searchTerm, page = 0) {
# leanpub-start-insert
    this.setState({ isLoading: true });
# leanpub-end-insert

    axios(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
      .then(result => this._isMounted && this.setSearchTopStories(result.data))
      .catch(error => this._isMounted && this.setState({ error }));
  }

  ...

}
~~~~~~~~

In the last step, we used the Loading component in the App component. A conditional rendering based on the loading state will decide whether to show a Loading component or the Button component. The latter is the button to fetch more data.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const {
      searchTerm,
      results,
      searchKey,
      error,
# leanpub-start-insert
      isLoading
# leanpub-end-insert
    } = this.state;

    ...

    return (
      <div className="page">
        ...
        <div className="interactions">
# leanpub-start-insert
          { isLoading
            ? <Loading />
            : <Button
                onClick={() => this.fetchSearchTopStories(searchKey, page + 1)}
              >
              More
            </Button>
          }
# leanpub-end-insert
        </div>
      </div>
    );
  }
}
~~~~~~~~

Initially, the Loading component will show when you start your application, because you make a request on `componentDidMount()`. There is no Table component, because the list is empty. When the response returns from the Hacker News API, the result is shown, the loading state is set to false, and the Loading component disappears. Instead, the "More" button to fetch more data appears. Once you fetch more data, the button will disappear again and the Loading component will appear.

### Exercises:

* Use a library such as [Font Awesome](https://fontawesome.com/) to show a loading icon instead of the "Loading ..." text

## Higher-Order Components

Higher-order components (HOC) are an advanced concept in React. HOCs are an equivalent to higher-order functions. They take any input, usually a component, but also optional arguments, and return a component as output. The returned component is an enhanced version of the input component, and it can be used in your JSX.

HOCs are used for different use cases. They can prepare properties, manage state, or alter the representation of a component. One case is to use a HOC as a helper for a conditional rendering. Imagine you have a List component that renders a list of items or nothing, because the list is empty or null. The HOC could shield away that the list would render nothing when there is no list. On the other hand, the plain List component doesn't need to bother anymore about a non existent list, as it only cares about rendering the list.

Let's do a simple HOC that takes a component as input and returns a component. You can place it in your *src/App.js* file.

{title="src/App.js",lang="javascript"}
~~~~~~~~
function withFoo(Component) {
  return function(props) {
    return <Component { ...props } />;
  }
}
~~~~~~~~

It is a useful convention to prefix a HOC with `with`. Since you are using JavaScript ES6, you can express the HOC better with an ES6 arrow function.

{title="src/App.js",lang="javascript"}
~~~~~~~~
const withEnhancement = (Component) => (props) =>
  <Component { ...props } />
~~~~~~~~

In our example, the input component stays the same as the output, so nothing happens. The output component should show the Loading component when the loading state is true, otherwise it should show the input component. A conditional rendering is a great use case for an HOC.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const withLoading = (Component) => (props) =>
  props.isLoading
    ? <Loading />
    : <Component { ...props } />
# leanpub-end-insert
~~~~~~~~

Based on the loading property, you can apply a conditional rendering. The function will return the Loading component or the input component. In general, it can be very efficient to spread an object like the props object in the previous example as input for a component. See the difference in the following code snippet:

{title="Code Playground",lang="javascript"}
~~~~~~~~
// before you would have to destructure the props before passing them
const { firstname, lastname } = props;
<UserProfile firstname={firstname} lastname={lastname} />

// but you can use the object spread operator to pass all object properties
<UserProfile { ...props } />
~~~~~~~~

We passed all the props including the `isLoading` property by spreading the object into the input component. The input component may not care about the `isLoading` property. You can use the ES6 rest destructuring to avoid it:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const withLoading = (Component) => ({ isLoading, ...rest }) =>
  isLoading
    ? <Loading />
    : <Component { ...rest } />
# leanpub-end-insert
~~~~~~~~

It takes one property out of the object, but keeps the remaining object, which also works with multiple properties. More can be read about it in Mozilla's [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

Now you can use the HOC in JSX. Maybe you want to show either the "More" button or the Loading component. The Loading component is already encapsulated in the HOC, but an input component is missing. For showing either a Button component or a Loading component, the Button is the input component of the HOC. The enhanced output component is a ButtonWithLoading component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Button = ({
  onClick,
  className = '',
  children,
}) =>
  <button
    onClick={onClick}
    className={className}
    type="button"
  >
    {children}
  </button>

const Loading = () =>
  <div>Loading ...</div>

const withLoading = (Component) => ({ isLoading, ...rest }) =>
  isLoading
    ? <Loading />
    : <Component { ...rest } />

# leanpub-start-insert
const ButtonWithLoading = withLoading(Button);
# leanpub-end-insert
~~~~~~~~

Everything is defined now. As a last step, you have to use the ButtonWithLoading component, which receives the loading state as an additional property. While the HOC consumes the loading property, all other props get passed to the Button component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    ...
    return (
      <div className="page">
        ...
        <div className="interactions">
# leanpub-start-insert
          <ButtonWithLoading
            isLoading={isLoading}
            onClick={() => this.fetchSearchTopStories(searchKey, page + 1)}
          >
            More
          </ButtonWithLoading>
# leanpub-end-insert
        </div>
      </div>
    );
  }
}
~~~~~~~~

Note that when you run your tests again, your snapshot test for the App component fails. The diff might look like the following on the command line:

{title="Command Line",lang="text"}
~~~~~~~~
-    <button
-      className=""
-      onClick={[Function]}
-      type="button"
-    >
-      More
-    </button>
+    <div>
+      Loading ...
+    </div>
~~~~~~~~

You can either fix the component now, when you think there is something wrong about it, or can accept the new snapshot of it. Because you introduced the Loading component in this chapter, you can accept the altered snapshot test on the command line in the interactive test.

Higher-order components are an advanced pattern in React. They have multiple purposes: improved reusability of components, greater abstraction, composability of components, and manipulations of props, state and view. I encourage you to read [gentle introduction to higher-order components](https://www.robinwieruch.de/gentle-introduction-higher-order-components/). It gives you another approach to learn them, shows you an elegant way to use them in a functional programming way, and solves the problem of conditional rendering with higher-order components.

### Exercises:

* Read [a gentle introduction to higher-order components](https://www.robinwieruch.de/gentle-introduction-higher-order-components/)
* Experiment with the HOC you have created
* Think about a use case where another HOC would make sense
  * Implement the HOC, if there is a use case

## Advanced Sorting

We implemented a client and server-side search interaction earlier. Since you have a Table component, it makes sense to enhance it with advanced interactions. Next, we'll introduce a sort functionality for each column by using the column headers of the Table.

It is possible to write your own sort function, but I prefer to use a utility library like [Lodash](https://lodash.com/) for these cases. There are other options, but we'll install Lodash for our sort function:

{title="Command Line",lang="text"}
~~~~~~~~
npm install lodash
~~~~~~~~

Now we import the sort functionality of Lodash in your *src/App.js* file:

{title="src/App.js",lang="javascript"}
~~~~~~~~
import React, { Component } from 'react';
import axios from 'axios';
# leanpub-start-insert
import { sortBy } from 'lodash';
# leanpub-end-insert
import './App.css';
~~~~~~~~

Now we have several columns in Table: title, author, comments and points columns. You can define sort functions where each takes a list and returns a list of items sorted by a specific property. Additionally, you will need a default sort function that doesn't sort, but returns the unsorted list. This will be the initial state.

{title="src/App.js",lang="javascript"}
~~~~~~~~
...

# leanpub-start-insert
const SORTS = {
  NONE: list => list,
  TITLE: list => sortBy(list, 'title'),
  AUTHOR: list => sortBy(list, 'author'),
  COMMENTS: list => sortBy(list, 'num_comments').reverse(),
  POINTS: list => sortBy(list, 'points').reverse(),
};
# leanpub-end-insert

class App extends Component {
  ...
}
...
~~~~~~~~

Two of the sort functions return a reversed list. That's to see the items with the highest comments and points, rather than the items with the lowest counts when the list is sorted for the first time.

The `SORTS` object allows you to reference any sort function now.

Again, the App component is responsible for storing the state of the sort. The initial state will be the default sort function, which doesn't sort at all and returns the input list as output.

{title="src/App.js",lang="javascript"}
~~~~~~~~
this.state = {
  results: null,
  searchKey: '',
  searchTerm: DEFAULT_QUERY,
  error: null,
  isLoading: false,
# leanpub-start-insert
  sortKey: 'NONE',
# leanpub-end-insert
};
~~~~~~~~

Once we choose a different `sortKey`, like the `AUTHOR` key, we sort the list with the appropriate sort function from the `SORTS` object.

Now we define a new class method in App component that sets a `sortKey` to the local component state, then `sortKey` can be used to retrieve the sorting function to apply it to the list:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {
  _isMounted = false;

  constructor(props) {

    ...

    this.needsToSearchTopStories = this.needsToSearchTopStories.bind(this);
    this.setSearchTopStories = this.setSearchTopStories.bind(this);
    this.fetchSearchTopStories = this.fetchSearchTopStories.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
# leanpub-start-insert
    this.onSort = this.onSort.bind(this);
# leanpub-end-insert
  }

  ...

# leanpub-start-insert
  onSort(sortKey) {
    this.setState({ sortKey });
  }
# leanpub-end-insert

  ...

}
~~~~~~~~

The next step is to pass the method and `sortKey` to the Table component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const {
      searchTerm,
      results,
      searchKey,
      error,
      isLoading,
# leanpub-start-insert
      sortKey
# leanpub-end-insert
    } = this.state;

    ...

    return (
      <div className="page">
        ...
        <Table
          list={list}
# leanpub-start-insert
          sortKey={sortKey}
          onSort={this.onSort}
# leanpub-end-insert
          onDismiss={this.onDismiss}
        />
        ...
      </div>
    );
  }
}
~~~~~~~~

The Table component is responsible for sorting the list. It takes one of the `SORT` functions by `sortKey` and passes the list as input, after which it keeps mapping over the sorted list.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const Table = ({
  list,
  sortKey,
  onSort,
  onDismiss
}) =>
# leanpub-end-insert
  <div className="table">
# leanpub-start-insert
    {SORTS[sortKey](list).map(item =>
# leanpub-end-insert
      <div key={item.objectID} className="table-row">
        ...
      </div>
    )}
  </div>
~~~~~~~~

In theory, the list should get sorted by one of the functions. But the default sort is set to `NONE`, so nothing is sorted yet, as nothing executes the `onSort()` method to change the `sortKey`. We extend the Table with a row of column headers that use Sort components in columns to sort each column:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Table = ({
  list,
  sortKey,
  onSort,
  onDismiss
}) =>
  <div className="table">
# leanpub-start-insert
    <div className="table-header">
      <span style={{ width: '40%' }}>
        <Sort
          sortKey={'TITLE'}
          onSort={onSort}
        >
          Title
        </Sort>
      </span>
      <span style={{ width: '30%' }}>
        <Sort
          sortKey={'AUTHOR'}
          onSort={onSort}
        >
          Author
        </Sort>
      </span>
      <span style={{ width: '10%' }}>
        <Sort
          sortKey={'COMMENTS'}
          onSort={onSort}
        >
          Comments
        </Sort>
      </span>
      <span style={{ width: '10%' }}>
        <Sort
          sortKey={'POINTS'}
          onSort={onSort}
        >
          Points
        </Sort>
      </span>
      <span style={{ width: '10%' }}>
        Archive
      </span>
    </div>
# leanpub-end-insert
    {SORTS[sortKey](list).map(item =>
      ...
    )}
  </div>
~~~~~~~~

Each Sort component gets a specific `sortKey` and the general `onSort()` function. Internally, it calls the method with the `sortKey` to set the specific key.

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Sort = ({ sortKey, onSort, children }) =>
  <Button onClick={() => onSort(sortKey)}>
    {children}
  </Button>
~~~~~~~~

As you can see, the Sort component reuses your common Button component. On a button click, each individual passed `sortKey` is set by the `onSort()` method, so the list is sorted when column headers are selected.

Now we'll improve the look of the button in the column header. Let's give it a proper `className`:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Sort = ({ sortKey, onSort, children }) =>
# leanpub-start-insert
  <Button
    onClick={() => onSort(sortKey)}
    className="button-inline"
  >
# leanpub-end-insert
    {children}
  </Button>
~~~~~~~~

This was done to improve the UI. The next goal is to implement a reverse sort. The list should perform a reverse sort once you click a Sort component twice. First, you need to define the reverse state with a boolean. The sort can be either reversed or non-reversed.

{title="src/App.js",lang="javascript"}
~~~~~~~~
this.state = {
  results: null,
  searchKey: '',
  searchTerm: DEFAULT_QUERY,
  error: null,
  isLoading: false,
  sortKey: 'NONE',
# leanpub-start-insert
  isSortReverse: false,
# leanpub-end-insert
};
~~~~~~~~

Now in your sort method, you can evaluate if the list is reverse sorted. It is reverse if the `sortKey` in the state is the same as the incoming `sortKey` and the reverse state is not already set to true.

{title="src/App.js",lang="javascript"}
~~~~~~~~
onSort(sortKey) {
# leanpub-start-insert
  const isSortReverse = this.state.sortKey === sortKey && !this.state.isSortReverse;
  this.setState({ sortKey, isSortReverse });
# leanpub-end-insert
}
~~~~~~~~

Again, we pass the reverse prop to your Table component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const {
      searchTerm,
      results,
      searchKey,
      error,
      isLoading,
      sortKey,
# leanpub-start-insert
      isSortReverse
# leanpub-end-insert
    } = this.state;

    ...

    return (
      <div className="page">
        ...
        <Table
          list={list}
          sortKey={sortKey}
# leanpub-start-insert
          isSortReverse={isSortReverse}
# leanpub-end-insert
          onSort={this.onSort}
          onDismiss={this.onDismiss}
        />
        ...
      </div>
    );
  }
}
~~~~~~~~

The Table has to have an arrow function block body to compute the data now:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const Table = ({
  list,
  sortKey,
  isSortReverse,
  onSort,
  onDismiss
}) => {
  const sortedList = SORTS[sortKey](list);
  const reverseSortedList = isSortReverse
    ? sortedList.reverse()
    : sortedList;

  return(
# leanpub-end-insert
    <div className="table">
      <div className="table-header">
        ...
      </div>
# leanpub-start-insert
      {reverseSortedList.map(item =>
# leanpub-end-insert
        ...
      )}
    </div>
# leanpub-start-insert
  );
}
# leanpub-end-insert
~~~~~~~~

Finally, we want to give the user visual feedback to distinguish which column is actively sorted. Each Sort component has its specific `sortKey` already, which can be used to identify the activated sort. We pass the `sortKey` from the internal component state as active sort key to your Sort component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Table = ({
  list,
  sortKey,
  isSortReverse,
  onSort,
  onDismiss
}) => {
  const sortedList = SORTS[sortKey](list);
  const reverseSortedList = isSortReverse
    ? sortedList.reverse()
    : sortedList;

  return(
    <div className="table">
      <div className="table-header">
        <span style={{ width: '40%' }}>
          <Sort
            sortKey={'TITLE'}
            onSort={onSort}
# leanpub-start-insert
            activeSortKey={sortKey}
# leanpub-end-insert
          >
            Title
          </Sort>
        </span>
        <span style={{ width: '30%' }}>
          <Sort
            sortKey={'AUTHOR'}
            onSort={onSort}
# leanpub-start-insert
            activeSortKey={sortKey}
# leanpub-end-insert
          >
            Author
          </Sort>
        </span>
        <span style={{ width: '10%' }}>
          <Sort
            sortKey={'COMMENTS'}
            onSort={onSort}
# leanpub-start-insert
            activeSortKey={sortKey}
# leanpub-end-insert
          >
            Comments
          </Sort>
        </span>
        <span style={{ width: '10%' }}>
          <Sort
            sortKey={'POINTS'}
            onSort={onSort}
# leanpub-start-insert
            activeSortKey={sortKey}
# leanpub-end-insert
          >
            Points
          </Sort>
        </span>
        <span style={{ width: '10%' }}>
          Archive
        </span>
      </div>
      {reverseSortedList.map(item =>
          ...
      )}
    </div>
  );
}
~~~~~~~~

Now the user will know whether sort is active based on the `sortKey` and `activeSortKey` . Give your Sort component an extra `className` attribute, in case it is sorted, to give visual feedback:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const Sort = ({
  sortKey,
  activeSortKey,
  onSort,
  children
}) => {
  const sortClass = ['button-inline'];

  if (sortKey === activeSortKey) {
    sortClass.push('button-active');
  }

  return (
    <Button
      onClick={() => onSort(sortKey)}
      className={sortClass.join(' ')}
    >
      {children}
    </Button>
  );
}
# leanpub-end-insert
~~~~~~~~

We can define `sortClass` more efficiently using a library called classnames, which is installed using npm:

{title="Command Line",lang="text"}
~~~~~~~~
npm install classnames
~~~~~~~~

After installation, we import it on top of the *src/App.js* file.

{title="src/App.js",lang="javascript"}
~~~~~~~~
import React, { Component } from 'react';
import axios from 'axios';
import { sortBy } from 'lodash';
# leanpub-start-insert
import classNames from 'classnames';
# leanpub-end-insert
import './App.css';
~~~~~~~~

Now we can define `className` with conditional classes:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Sort = ({
  sortKey,
  activeSortKey,
  onSort,
  children
}) => {
# leanpub-start-insert
  const sortClass = classNames(
    'button-inline',
    { 'button-active': sortKey === activeSortKey }
  );
# leanpub-end-insert

  return (
# leanpub-start-insert
    <Button
      onClick={() => onSort(sortKey)}
      className={sortClass}
    >
# leanpub-end-insert
      {children}
    </Button>
  );
}
~~~~~~~~

There will be failing snapshot and unit tests for the Table component. Since we intentionally changed again our component representations, we accept the snapshot tests, but we still need to fix the unit test. In *src/App.test.js* , provide a `sortKey` and the `isSortReverse` boolean for the Table component.

{title="src/App.test.js",lang="javascript"}
~~~~~~~~
...

describe('Table', () => {

  const props = {
    list: [
      { title: '1', author: '1', num_comments: 1, points: 2, objectID: 'y' },
      { title: '2', author: '2', num_comments: 1, points: 2, objectID: 'z' },
    ],
# leanpub-start-insert
    sortKey: 'TITLE',
    isSortReverse: false,
# leanpub-end-insert
  };

  ...

});
~~~~~~~~

We may need to accept the failing snapshot tests again for the Table component, because we provided extended props for it. The advanced sort interaction is finally complete.

### Exercises:

* Use a library like [Font Awesome](https://fontawesome.com/) to indicate the (reverse) sort. It could be an arrow up or arrow down icon next to each Sort header
* Read about the [classnames library](https://github.com/JedWatson/classnames)

{pagebreak}

You have learned advanced component techniques in React! Let's recap the chapter:

* **React**
  * The `ref` attribute to reference DOM elements
  * Higher-order components are a common way to build advanced components
  * Implementation of advanced interactions in React
  * Conditional classNames with a neat helper library
* **ES6**
  * Rest destructuring to split up objects and arrays

You can always find the source code in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.5).
