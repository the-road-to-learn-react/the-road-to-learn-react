# Advanced React Components

The chapter will focus on the implementation of advanced React components. You will learn about higher order components and how to implement them. In addition you will dive into more advanced topics in React and implement complex interactions with it.

## Ref a DOM Element

Sometimes you need to interact with your DOM nodes in React. The `ref` attribute gives you access to a node in your elements. Usually that is an anti pattern in React, because you should use its declarative way of doing things and its unidirectional data flow. You have learned about it when you have introduced your first search input field. But there are certain cases where you need access to the DOM node. The official documentation mentions three use cases:

* to use the DOM API (focus, media playback etc.)
* to invoke imperative DOM node animations
* to integrate with third-party library that needs the DOM node (e.g. [D3.js](https://d3js.org/))

Let's do it by example with the Search component. When the application renders the first time, the input field should be focused. That's one use case where you would need access to the DOM API. This chapter will show you how it works, but since it is not very useful for the application itself, we will omit the changes after the chapter. You can keep it for your own application though.

In general, you can use the `ref` attribute in both functional stateless components and ES6 class components. In the example of the focus use case, you will need a lifecycle method. That's why the approach is first showcased by using the `ref` attribute with an ES6 class component.

The initial step is to refactor the functional stateless component to an ES6 class component.

{title="src/App.js",lang=javascript}
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

The `this` object of an ES6 class component helps us to reference the DOM node with the `ref` attribute.

{title="src/App.js",lang=javascript}
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
          ref={(node) => { this.input = node; }}
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

{title="src/App.js",lang=javascript}
~~~~~~~~
class Search extends Component {
# leanpub-start-insert
  componentDidMount() {
    this.input.focus();
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
          ref={(node) => { this.input = node; }}
        />
        <button type="submit">
          {children}
        </button>
      </form>
    );
  }
}
~~~~~~~~

The input field should be focused when the application renders. That's it basically for using the `ref` attribute.

But how would you get access to the `ref` in a functional stateless component without the `this` object? The following functional stateless component demonstrates it.

{title="src/App.js",lang=javascript}
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
        ref={(node) => input = node}
# leanpub-end-insert
      />
      <button type="submit">
        {children}
      </button>
    </form>
  );
}
~~~~~~~~

Now you would be able to access the input DOM element. In the example of the focus use case it wouldn't help you, because you have no lifecycle method in a functional stateless component to trigger the focus. But in the future you might come across other use cases where it can make sense to use a functional stateless component with the `ref` attribute.

### Exercises

* read more about [the ref attribute in general in React](https://facebook.github.io/react/docs/refs-and-the-dom.html)
* read more about [the usage of the ref attribute in React](https://www.robinwieruch.de/react-ref-attribute-dom-node/)

## Loading ...

Now let's get back to the application. You might want to show a loading indicator when you submit a search request to the Hacker News API. The request is asynchronous and you should show your user some feedback that something is about to happen. Let's define a reusable Loading component in your *src/App.js* file.

{title="src/App.js",lang=javascript}
~~~~~~~~
const Loading = () =>
  <div>Loading ...</div>
~~~~~~~~

Now you will need a property to store the loading state. Based on the loading state you can decide to show the Loading component later on.

{title="src/App.js",lang=javascript}
~~~~~~~~
class App extends Component {

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

The initial value of that `isLoading` property is false. You don't load anything before the App component is mounted.

When you make the request, you set a loading state to true. Eventually the request will succeed and you can set the loading state to false.

{title="src/App.js",lang=javascript}
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

    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
      .then(response => response.json())
      .then(result => this.setSearchTopStories(result))
      .catch(e => this.setState({ error: e }));
  }

  ...

}
~~~~~~~~

In the last step, you will use the Loading component in your App. A conditional rendering based on the loading state will decide whether you show a Loading component or the Button component. The latter one is your button to fetch more data.

{title="src/App.js",lang=javascript}
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
              onClick={() => this.fetchSearchTopStories(searchKey, page + 1)}>
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

Initially the Loading component will show up when you start your application, because you make a request on `componentDidMount()`. There is no Table component, because the list is empty. When the response returns from the Hacker News API, the result is shown, the loading state is set to false and the Loading component disappears. Instead, the "More" button to fetch more data appears. Once you fetch more data, the button will disappear again and the Loading component will show up.

### Exercises:

* use a library such as [Font Awesome](http://fontawesome.io/) to show a loading icon instead of the "Loading ..." text

## Higher Order Components

Higher order components (HOC) are an advanced concept in React. HOCs are an equivalent to higher order functions. They take any input - most of the time a component, but also optional arguments - and return a component as output. The returned component is an enhanced version of the input component and can be used in your JSX.

HOCs are used for different use cases. They can prepare properties, manage state or alter the representation of a component. One use case could be to use a HOC as a helper for a conditional rendering. Imagine you have a List component that renders a list of items or nothing, because the list is empty or null. The HOC could shield away that the list would render nothing when there is no list. On the other hand, the plain List component doesn't need to bother anymore about an non existent list. It only cares about rendering the list.

Let's do a simple HOC which takes a component as input and returns a component. You can place it in your *src/App.js* file.

{title="src/App.js",lang=javascript}
~~~~~~~~
function withFoo(Component) {
  return function(props) {
    return <Component { ...props } />;
  }
}
~~~~~~~~

One neat convention is to prefix the naming of a HOC with `with`. Since you are using JavaScript ES6, you can express the HOC more concisely with an ES6 arrow function.

{title="src/App.js",lang=javascript}
~~~~~~~~
const withFoo = (Component) => (props) =>
  <Component { ...props } />
~~~~~~~~

In the example, the input component would stay the same as the output component. Nothing happens. It renders the same component instance and passes all of the props to the output component. But that's useless. Let's enhance the output component. The output component should show the Loading component, when the loading state is true, otherwise it should show the input component. A conditional rendering is a great use case for a HOC.

{title="src/App.js",lang=javascript}
~~~~~~~~
# leanpub-start-insert
const withLoading = (Component) => (props) =>
  props.isLoading
    ? <Loading />
    : <Component { ...props } />
# leanpub-end-insert
~~~~~~~~

Based on the loading property you can apply a conditional rendering. The function will return the Loading component or the input component.

In general it can be very efficient to spread an object, like the props object in the previous example, as input for a component. See the difference in the following code snippet.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// before you would have to destructure the props before passing them
const { foo, bar } = props;
<SomeComponent foo={foo} bar={bar} />

// but you can use the object spread operator to pass all object properties
<SomeComponent { ...props } />
~~~~~~~~

There is one little thing that you should avoid. You pass all the props including the `isLoading` property, by spreading the object, into the input component. However, the input component may not care about the `isLoading` property. You can use the ES6 rest destructuring to avoid it.

{title="src/App.js",lang=javascript}
~~~~~~~~
# leanpub-start-insert
const withLoading = (Component) => ({ isLoading, ...rest }) =>
  isLoading
    ? <Loading />
    : <Component { ...rest } />
# leanpub-end-insert
~~~~~~~~

It takes one property out of the object, but keeps the remaining object. It works with multiple properties as well. You might have already read about it in the [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

Now you can use the HOC in your JSX. An use case in the application could be to show either the "More" button or the Loading component. The Loading component is already encapsulated in the HOC, but an input component is missing. In the use case of showing a Button component or a Loading component, the Button is the input component of the HOC. The enhanced output component is a ButtonWithLoading component.

{title="src/App.js",lang=javascript}
~~~~~~~~
const Button = ({ onClick, className = '', children }) =>
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

{title="src/App.js",lang=javascript}
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
            onClick={() => this.fetchSearchTopStories(searchKey, page + 1)}>
            More
          </ButtonWithLoading>
# leanpub-end-insert
        </div>
      </div>
    );
  }
}
~~~~~~~~

When you run your tests again, you will notice that your snapshot test for the App component fails. The diff might look like the following on the command line:

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

Higher order components are an advanced technique in React. They have multiple purposes like improved reusability of components, greater abstraction, composability of components and manipulations of props, state and view. Don't worry if you don't understand them immediately. It takes time to get used to them.

I encourage you to read the [gentle introduction to higher order components](https://www.robinwieruch.de/gentle-introduction-higher-order-components/). It gives you another approach to learn them, shows you an elegant way to use them a functional programming way and solves specifically the problem of conditional rendering with higher order components.

### Exercises:

* read [a gentle introduction to higher order components](https://www.robinwieruch.de/gentle-introduction-higher-order-components/)
* experiment with the HOC you have created
* think about a use case where another HOC would make sense
  * implement the HOC, if there is a use case

## Advanced Sorting

You have already implemented a client- and server-side search interaction. Since you have a Table component, it would make sense to enhance the Table with advanced interactions. What about introducing a sort functionality for each column by using the column headers of the Table?

It would be possible to write your own sort function, but personally I prefer to use a utility library for such cases. [Lodash](https://lodash.com/) is one of these utility libraries, but you can use whatever library suits you. Let's install Lodash and use it for the sort functionality.

{title="Command Line",lang="text"}
~~~~~~~~
npm install lodash
~~~~~~~~

Now you can import the sort functionality of Lodash in your *src/App.js* file.

{title="src/App.js",lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import fetch from 'isomorphic-fetch';
# leanpub-start-insert
import { sortBy } from 'lodash';
# leanpub-end-insert
import './App.css';
~~~~~~~~

You have several columns in your Table. There are title, author, comments and points columns. You can define sort functions whereas each function takes a list and returns a list of items sorted by a specific property. Additionally, you will need one default sort function which doesn't sort but only returns the unsorted list. That will be your initial state.

{title="src/App.js",lang=javascript}
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

You can see that two of the sort functions return a reversed list. That's because you want to see the items with the highest comments and points rather than to see the items with the lowest counts when you sort the list for the first time.

The `SORTS` object allows you to reference any sort function now.

Again your App component is responsible for storing the state of the sort. The initial state will be the initial default sort function, which doesn't sort at all and returns the input list as output.

{title="src/App.js",lang=javascript}
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

Once you choose a different `sortKey`, let's say the `AUTHOR` key, you will sort the list with the appropriate sort function from the `SORTS` object.

Now you can define a new class method in your App component that simply sets a `sortKey` to your local component state. Afterward, the `sortKey` can be used to retrieve the sorting function to apply it on your list.

{title="src/App.js",lang=javascript}
~~~~~~~~
class App extends Component {

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

# leanpub-start-insert
  onSort(sortKey) {
    this.setState({ sortKey });
  }
# leanpub-end-insert

  ...

}
~~~~~~~~

The next step is to pass the method and `sortKey` to your Table component.

{title="src/App.js",lang=javascript}
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

The Table component is responsible for sorting the list. It takes one of the `SORT` functions by `sortKey` and passes the list as input. Afterward it keeps mapping over the sorted list.

{title="src/App.js",lang=javascript}
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

In theory the list would get sorted by one of the functions. But the default sort is set to `NONE`, so nothing is sorted yet. So far, no one executes the `onSort()` method to change the `sortKey`. Let's extend the Table with a row of column headers that use Sort components in columns to sort each column.

{title="src/App.js",lang=javascript}
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

Each Sort component gets a specific `sortKey` and the general `onSort()` function. Internally it calls the method with the `sortKey` to set the specific key.

{title="src/App.js",lang=javascript}
~~~~~~~~
const Sort = ({ sortKey, onSort, children }) =>
  <Button onClick={() => onSort(sortKey)}>
    {children}
  </Button>
~~~~~~~~

As you can see, the Sort component reuses your common Button component. On a button click each individual passed `sortKey` will get set by the `onSort()` method. Now you should be able to sort the list when you click on the column headers.

There is one minor improvement for an improved look. So far, the button in a column header looks a bit silly. Let's give the button in the Sort component a proper `className`.

{title="src/App.js",lang=javascript}
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

It should look nice now. The next goal would be to implement a reverse sort as well. The list should get reverse sorted once you click a Sort component twice. First, you need to define the reverse state with a boolean. The sort can be either reversed or non reversed.

{title="src/App.js",lang=javascript}
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

{title="src/App.js",lang=javascript}
~~~~~~~~
onSort(sortKey) {
# leanpub-start-insert
  const isSortReverse = this.state.sortKey === sortKey && !this.state.isSortReverse;
  this.setState({ sortKey, isSortReverse });
# leanpub-end-insert
}
~~~~~~~~

Again you can pass the reverse prop to your Table component.

{title="src/App.js",lang=javascript}
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

The Table has to have an arrow function block body to compute the data now.

{title="src/App.js",lang=javascript}
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

The reverse sort should work now.

Last but not least, you have to deal with one open question for the sake of an improved user experience. Can a user distinguish which column is actively sorted? So far, it is not possible. Let's give the user a visual feedback.

Each Sort component gets its specific `sortKey` already. It could be used to identify the activated sort. You can pass the `sortKey` from the internal component state as active sort key to your Sort component.

{title="src/App.js",lang=javascript}
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

Now in your Sort component, you know based on the `sortKey` and `activeSortKey` whether the sort is active. Give your Sort component an extra `className` attribute, in case it is sorted, to give the user a visual feedback.

{title="src/App.js",lang=javascript}
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

The way to define the `sortClass` is a bit clumsy, isn't it? There is a neat little library to get rid of this. First you have to install it.

{title="Command Line",lang="text"}
~~~~~~~~
npm install classnames
~~~~~~~~

And second you have to import it on top of your *src/App.js* file.

{title="src/App.js",lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import fetch from 'isomorphic-fetch';
import { sortBy } from 'lodash';
# leanpub-start-insert
import classNames from 'classnames';
# leanpub-end-insert
import './App.css';
~~~~~~~~

Now you can use it to define your component `className` with conditional classes.

{title="src/App.js",lang=javascript}
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

Again, when you run your tests, you should see failing snapshot tests but also failing unit tests for the Table component. Since you changed again your component representations, you can accept the snapshot tests. But you have to fix the unit test. In your *src/App.test.js* file, you need to provide a `sortKey` and the `isSortReverse` boolean for the Table component.

{title="src/App.test.js",lang=javascript}
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

Once again you might need to accept the failing snapshot tests for your Table component, because you provided extended props for the Table component.

Finally your advanced sort interaction is complete now.

### Exercises:

* use a library like [Font Awesome](http://fontawesome.io/) to indicate the (reverse) sort
  * it could be an arrow up or arrow down icon next to each Sort header
* read more about the [classnames library](https://github.com/JedWatson/classnames)

{pagebreak}

You have learned advanced component techniques in React! Let's recap the last chapters:

* React
  * the ref attribute to reference DOM nodes
  * higher order components are a common way to build advanced components
  * implementation of advanced interactions in React
  * conditional classNames with a neat helper library
* ES6
  * rest destructuring to split up objects and arrays

You can find the source code in the [official repository](https://github.com/rwieruch/hackernews-client/tree/4.5).