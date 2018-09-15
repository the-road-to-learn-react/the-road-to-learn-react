# State Management in React

We have already covered the basics of state management in React in the previous chapters by using React's local state, so this chapter will dig a bit deeper. It will expand on the best practices, how to apply them, and why you could consider using a third-party state management library.

## Lifting State

Only the App component is a stateful ES6 component in your application. It handles a lot of application state and logic in its class methods. Moreover, we pass a lot of properties to the Table component, most of which are only used in there. It's not important that the App component knows about them, so the sort functionality could be moved into the Table component.

Moving substate from one component to another is known as *lifting state*. We want to move state that isn't used in the App component into the Table component, down from parent to child component. To deal with state and class methods in the Table component, it has to become an ES6 class component. The refactoring from functional stateless component to ES6 class component is straightforward.

Your Table component as a functional stateless component:

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
    ...
  );
}
~~~~~~~~

Your Table component as an ES6 class component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
class Table extends Component {
  render() {
    const {
      list,
      sortKey,
      isSortReverse,
      onSort,
      onDismiss
    } = this.props;

    const sortedList = SORTS[sortKey](list);
    const reverseSortedList = isSortReverse
      ? sortedList.reverse()
      : sortedList;

    return (
      ...
    );
  }
}
# leanpub-end-insert
~~~~~~~~

Since you want to deal with state and methods in your component, you have to add a constructor and initial state.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Table extends Component {
# leanpub-start-insert
  constructor(props) {
    super(props);

    this.state = {};
  }
# leanpub-end-insert

  render() {
    ...
  }
}
~~~~~~~~

Now you can move state and class methods with the sort functionality from your App component down to your Table component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Table extends Component {
  constructor(props) {
    super(props);

# leanpub-start-insert
    this.state = {
      sortKey: 'NONE',
      isSortReverse: false,
    };

    this.onSort = this.onSort.bind(this);
# leanpub-end-insert
  }

# leanpub-start-insert
  onSort(sortKey) {
    const isSortReverse = this.state.sortKey === sortKey &&
      !this.state.isSortReverse;

    this.setState({ sortKey, isSortReverse });
  }
# leanpub-end-insert

  render() {
    ...
  }
}
~~~~~~~~

Remember to remove the moved state and `onSort()` class method from your App component.

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
      isLoading: false,
    };

    this.setSearchTopStories = this.setSearchTopStories.bind(this);
    this.fetchSearchTopStories = this.fetchSearchTopStories.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
    this.needsToSearchTopStories = this.needsToSearchTopStories.bind(this);
  }

  ...

}
~~~~~~~~

You can also make the Table component more lightweight. To do this, we move props that are passed to it from the App component, because they are handled internally in the Table component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
# leanpub-start-insert
    const {
      searchTerm,
      results,
      searchKey,
      error,
      isLoading
    } = this.state;
# leanpub-end-insert

    ...

    return (
      <div className="page">
        ...
        { error
          ? <div className="interactions">
            <p>Something went wrong.</p>
          </div>
          : <Table
# leanpub-start-insert
            list={list}
            onDismiss={this.onDismiss}
# leanpub-end-insert
          />
        }
        ...
      </div>
    );
  }
}
~~~~~~~~

In the Table component, use the internal `onSort()` method and the internal Table state:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Table extends Component {

  ...

  render() {
# leanpub-start-insert
    const {
      list,
      onDismiss
    } = this.props;

    const {
      sortKey,
      isSortReverse,
    } = this.state;
# leanpub-end-insert

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
# leanpub-start-insert
              onSort={this.onSort}
# leanpub-end-insert
              activeSortKey={sortKey}
            >
              Title
            </Sort>
          </span>
          <span style={{ width: '30%' }}>
            <Sort
              sortKey={'AUTHOR'}
# leanpub-start-insert
              onSort={this.onSort}
# leanpub-end-insert
              activeSortKey={sortKey}
            >
              Author
            </Sort>
          </span>
          <span style={{ width: '10%' }}>
            <Sort
              sortKey={'COMMENTS'}
# leanpub-start-insert
              onSort={this.onSort}
# leanpub-end-insert
              activeSortKey={sortKey}
            >
              Comments
            </Sort>
          </span>
          <span style={{ width: '10%' }}>
            <Sort
              sortKey={'POINTS'}
# leanpub-start-insert
              onSort={this.onSort}
# leanpub-end-insert
              activeSortKey={sortKey}
            >
              Points
            </Sort>
          </span>
          <span style={{ width: '10%' }}>
            Archive
          </span>
        </div>
        { reverseSortedList.map((item) =>
          ...
        )}
      </div>
    );
  }
}
~~~~~~~~

We made a crucial refactoring by moving functionality and state closer into another component, and other components got more lightweight. Again, the component API of the Table got lighter because it deals internally with the sort functionality.

Lifting state can go the other way as well: from child to parent component. It is called as lifting state up. Imagine you were dealing with local state in a child component, and you want to fulfil a requirement to show the state in your parent component as well. You would have to lift up the state to your parent component. Moreover, imagine you want to show the state in a sibling component of your child component. Again, you would lift the state up to your parent component. The parent component deals with the local state, but exposes it to both child components.

### Exercises:

* Read about [lifting state in React](https://reactjs.org/docs/lifting-state-up.html)
* Read about lifting state in [learn React before using Redux](https://www.robinwieruch.de/learn-react-before-using-redux/)

## Revisited: setState()

So far, we have used React `setState()` to manage your internal component state. We can pass an object to the function where it updates partially the local state.

{title="Code Playground",lang="javascript"}
~~~~~~~~
this.setState({ value: 'hello'});
~~~~~~~~

But `setState()` doesn't take only an object. In its second version, you can pass a function to update the state.

{title="Code Playground",lang="javascript"}
~~~~~~~~
this.setState((prevState, props) => {
  ...
});
~~~~~~~~

There is one crucial case where it makes sense to use a function over an object: when you update the state depending on the previous state or props. If you don't use a function, the local state management can cause bugs. The React `setState()` method is asynchronous. React batches `setState()` calls and executes them eventually. Sometimes, the previous state or props changes between before we can rely on it in our `setState()` call.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const { oneCount } = this.state;
const { anotherCount } = this.props;
this.setState({ count: oneCount + anotherCount });
~~~~~~~~

Imagine that `oneCount` and `anotherCount`, thus the state or the props, change somewhere else asynchronously when you call `setState()`. In a growing application, you have more than one `setState()` call across your application. Since `setState()` executes asynchronously, you could rely in the example on stale values.

With the function approach, the function in `setState()` is a callback that operates on the state and props at the time of executing the callback function. Even though `setState()` is asynchronous, with a function it takes the state and props at the time when it is executed.

{title="Code Playground",lang="javascript"}
~~~~~~~~
this.setState((prevState, props) => {
  const { oneCount } = prevState;
  const { anotherCount } = props;
  return { count: oneCount + anotherCount };
});
~~~~~~~~

In our code, the `setSearchTopStories()` method relies on the previous state, and this is a good example to use a function over an object in `setState()`. Right now, it looks like the following code:

{title="src/App.js",lang="javascript"}
~~~~~~~~
setSearchTopStories(result) {
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
    },
    isLoading: false
  });
}
~~~~~~~~

Here, we extracted values from the state, but updated the state depending on the previous state asynchronously. Now we'll use the functional approach to prevent bugs from a stale state:

{title="src/App.js",lang="javascript"}
~~~~~~~~
setSearchTopStories(result) {
  const { hits, page } = result;

# leanpub-start-insert
  this.setState(prevState => {
    ...
  });
# leanpub-end-insert
}
~~~~~~~~

We can move the whole block we implemented into the function by directing it to operate on the `prevState` instead of the `this.state`.

{title="src/App.js",lang="javascript"}
~~~~~~~~
setSearchTopStories(result) {
  const { hits, page } = result;

  this.setState(prevState => {
# leanpub-start-insert
    const { searchKey, results } = prevState;

    const oldHits = results && results[searchKey]
      ? results[searchKey].hits
      : [];

    const updatedHits = [
      ...oldHits,
      ...hits
    ];

    return {
      results: {
        ...results,
        [searchKey]: { hits: updatedHits, page }
      },
      isLoading: false
    };
# leanpub-end-insert
  });
}
~~~~~~~~

That will fix the issue with a stale state, but there is still one more improvement. Since it is a function, you can extract the function for improved readability. One more advantage to use a function over an object is that function can live outside of the component. We still have to use a higher-order function to pass the result to it since we want to update the state based on the fetched result from the API.

{title="src/App.js",lang="javascript"}
~~~~~~~~
setSearchTopStories(result) {
  const { hits, page } = result;
  this.setState(updateSearchTopStoriesState(hits, page));
}
~~~~~~~~

The `updateSearchTopStoriesState()` function has to return a function. It is a higher-order function that can be defined outside the App component. Note how the function signature changes slightly now.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const updateSearchTopStoriesState = (hits, page) => (prevState) => {
  const { searchKey, results } = prevState;

  const oldHits = results && results[searchKey]
    ? results[searchKey].hits
    : [];

  const updatedHits = [
    ...oldHits,
    ...hits
  ];

  return {
    results: {
      ...results,
      [searchKey]: { hits: updatedHits, page }
    },
    isLoading: false
  };
};
# leanpub-end-insert

class App extends Component {
  ...
}
~~~~~~~~

The function instead of object approach in `setState()` fixes potential bugs, while increasing the readability and maintainability of your code. Further, it becomes testable outside of the App component. I advise exporting and testing it as practice.

### Exercise:

* Read about [React using state correctly](https://reactjs.org/docs/state-and-lifecycle.html#using-state-correctly)
* Export updateSearchTopStoriesState from the file
  * Write a test for it which passes the a payload (hits, page) and a made up previous state and finally expect a new state
* Refactor your `setState()` methods to use a function, but only when it makes sense, because it relies on props or state
* Run your tests again and verify that everything is up to date

## Taming the State

Previous chapters have shown you that state management can be a crucial topic in larger applications, as React and a lot of other SPA frameworks struggle with it. As applications get more complex, the big challenge in web applications is to tame and control the state.

Compared to other solutions, React has already taken a big step forward. A unidirectional data flow and a simple API to manage state in components is indispensable. These concepts make it easier to reason about your state and your state changes. It also makes it easier to reason about it on a component level and on an application level to a certain degree.

It is possible to introduce bugs by operating on stale state when using an object over a function in `setState()`. We lift state around to share or hide necessary state across components. Sometimes a component needs to lift up state, because its sibling component depends on it. Perhaps the component is far away in the component tree, so the stated needs to be shared across the whole component tree. Components are more involved in state management, as the main responsibility of components is representing the UI.

Because of this, there are standalone solutions to take care of state management. Libraries like [Redux](https://redux.js.org/introduction) or [MobX](https://mobx.js.org/) are both feasible solutions in a React application. They come with extensions, [react-redux](https://github.com/reactjs/react-redux) and [mobx-react](https://github.com/mobxjs/mobx-react), to integrate them into the React view layer. Redux and MobX are outside of the scope of this book, but I encourage you to study the different ways to handle scaling state management as your React applications become more complex.

### Exercises:

* Read about [external state management and how to learn it](https://www.robinwieruch.de/redux-mobx-confusion/)
* Check out my second book about state management in React called [Taming the State in React](https://roadtoreact.com/)

{pagebreak}

You have learned advanced state management in React! Let's recap the last chapter:

* **React**
  * Lift state management up and down to suitable components
  * `setState()` can use a function to prevent stale state bugs
  * Existing external solutions that help you to tame the state

You can find the source code in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.6).