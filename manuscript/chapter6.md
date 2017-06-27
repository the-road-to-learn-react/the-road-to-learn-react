# State Management in React and beyond

You have learned the basics of state management in React already in the previous chapters. This chapter digs a bit deeper into the topic. You will learn best practices, how to apply them and why you could consider using a third party state management library.

## Lifting State

Only the App component is a stateful ES6 component in your application. It handles a lot of application state and logic (methods). Maybe you have noticed that you pass a lot of properties to your Table component. Most of the props are only used in the component. It makes no sense that the App component knows about them.

The sort functionality is only handled in the Table component. You could move it closer to the Table component. The App component doesn't need to know about it at all. The process of refactoring substate from one component to another is known as *lifting state*. In your case you want to move state that isn't used in the App component closer to the Table component. The state moves down from parent to child component.

In order to deal with state and methods in the Table component, it has to become an ES6 class component. The refactoring from functional stateless component to ES6 class component is straight forward.

Your Table component as a functional stateless component:

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
    ...
  );
}
~~~~~~~~

Can get refactored to an ES6 class component:

{title="src/App.js",lang=javascript}
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

    return(
      ...
    );
  }
}
# leanpub-end-insert
~~~~~~~~

Since you want to deal with state and methods in your component, you have to add a constructor and initial state.

{title="src/App.js",lang=javascript}
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

Now you can move state and methods from your App component down to your Table component.

{title="src/App.js",lang=javascript}
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
    const isSortReverse = this.state.sortKey === sortKey && !this.state.isSortReverse;
    this.setState({ sortKey, isSortReverse });
  }
# leanpub-end-insert

  render() {
    ...
  }
}
~~~~~~~~

Don't forget to remove the moved state and `onSort()` method from your App component.

{title="src/App.js",lang=javascript}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      results: null,
      searchKey: '',
      searchTerm: DEFAULT_QUERY,
      isLoading: false,
    };

    this.setSearchTopstories = this.setSearchTopstories.bind(this);
    this.fetchSearchTopstories = this.fetchSearchTopstories.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
    this.onSearchSubmit = this.onSearchSubmit.bind(this);
    this.onSearchChange = this.onSearchChange.bind(this);
    this.needsToSearchTopstories = this.needsToSearchTopstories.bind(this);
  }

  ...

}
~~~~~~~~

Additionally you can make the Table component API more lightweight. Remove the props which are handled internally in the Table component.

{title="src/App.js",lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  render() {
# leanpub-start-insert
    const {
      searchTerm,
      results,
      searchKey,
      isLoading
    } = this.state;
# leanpub-end-insert

    ...

    return (
      <div className="page">
        ...
# leanpub-start-insert
        <Table
          list={list}
          onDismiss={this.onDismiss}
        />
# leanpub-end-insert
        ...
      </div>
    );
  }
}
~~~~~~~~

Now in your Table component you can use the internal `onSort()` method and the internal Table state.

{title="src/App.js",lang=javascript}
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

Your application should still work. But you made a crucial refactoring. You moved functionality and state closer to a component. Other components got more lightweight again. Additionally the component API of the Table got more lightweight because it deals internally with the sort functionality.

The process of lifting state can go the other way as well: from child to parent component - lifting state up. Imagine you were dealing with internal state in a child component. Now you want to fulfill a requirement to show the state in your parent component as well. You would have to lift up the state to your parent component. But it goes even further. Imagine you want to show the state in a sibling component of your child component. Again you would have to lift the state up to your parent component. The parent component deals with the internal state, but exposes it to both child components.

### Exercises:

* read more about [lifting state in React](https://facebook.github.io/react/docs/lifting-state-up.html)

## Revisited: setState()

So far, you have used React `setState()` to manage your internal component state. You can pass an object to the function where you can update partially the internal state.

{title="Code Playground",lang="javascript"}
~~~~~~~~
this.setState({ foo: bar });
~~~~~~~~

But `setState()` doesn't take only an object. In its second version, you can pass a function to update the state.

{title="Code Playground",lang="javascript"}
~~~~~~~~
this.setState((prevState, props) => {
  ...
});
~~~~~~~~

Why should you want to do that? There is one crucial use case where it makes sense to use a function over an object. It is when you update the state depending on the previous state or props. If you don't use a function, the internal state management can cause bugs.

But why does it cause bugs to use an object over a function when the update depends on the previous state or props? The React `setState()` method is asynchronous. React batches `setState()` calls and executes them eventually. It can happen that the previous state or props changed in between when you would rely on it.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const { fooCount } = this.state;
const { barCount } = this.props;
this.setState({ count: fooCount + barCount });
~~~~~~~~

Imagine that `fooCount` and `barCount`, thus the state or the props, can change somewhere else in your components. In a growing application you have more often 'setState()' calls across your application. Since `setState()` executes asynchronously, you would rely in the example on likely stale values.

With the function approach, the function in `setState()` is a callback that operates on the state and props at the time of executing the callback function. Even though `setState()` is asynchronous, with a function it takes the state and props at the time when it is executed.

{title="Code Playground",lang="javascript"}
~~~~~~~~
this.setState((prevState, props) => {
  const { fooCount } = prevState;
  const { barCount } = props;
  return { count: fooCount + barCount };
});
~~~~~~~~

Now, lets get back to your code to fix this behavior. Together we will fix it for one place where `setState()` is used and relies on the state or props. Afterward you are able to fix it at places where the state update relies on the previous state or props.

The `setSearchTopstories()` method relies on the previous state and thus is a perfect example to use a function over an object in `setState()`. Right now it looks like the following code snippet. You extract values from the state, but update the state depending on the previous state asynchronously.

{title="src/App.js",lang=javascript}
~~~~~~~~
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
    },
    isLoading: false
  });
}
~~~~~~~~

Now you can use the function to prevent bugs because of a stale state.

{title="src/App.js",lang=javascript}
~~~~~~~~
setSearchTopstories(result) {
  const { hits, page } = result;

# leanpub-start-insert
  this.setState(prevState => {
    ...
  });
# leanpub-end-insert
}
~~~~~~~~

You can move the whole block that you already implemented into the function. You only have to exchange that you operate on the `prevState` rather than `this.state`.

{title="src/App.js",lang=javascript}
~~~~~~~~
setSearchTopstories(result) {
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

That will fix the issue with a stale state. Since it is a function, you can extract the function for an improved readability. That's one more advantage to use a function over an object. The function can live outside of the component. But you have to use a higher order function to pass the result. After all you want to update the state based on the fetched result from the API.

{title="src/App.js",lang=javascript}
~~~~~~~~
setSearchTopstories(result) {
  const { hits, page } = result;
  this.setState(updateSearchTopstoriesState(hits, page));
}
~~~~~~~~

The `updateSearchTopstoriesState()` function has to return a function. It is a higher order function. You can define this higher order function outside of your App component. Note how the function signature changes slightly now.

{title="src/App.js",lang=javascript}
~~~~~~~~
# leanpub-start-insert
const updateSearchTopstoriesState = (hits, page) => (prevState) => {
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

That's it. The function over an object approach in `setState()` fixes potential bugs yet increases readability and maintainability of your code.

### Exercise:

* read more about [React using state correctly](https://facebook.github.io/react/docs/state-and-lifecycle.html#using-state-correctly)
* refactor all setState methods to use a function
  * but only when it makes sense, because it relies on props or state

## Taming the State

The previous chapters have shown you that state management can be a crucial topic in your application. In general, not only React but a lot of SPA frameworks struggle with it. Applications got more complex in the recent years. One big challenge in web applications nowadays is to tame and control the state.

Compared to other solutions React already made a big step forward. The unidirectional data flow and a simple API to manage state in a component are indispensable. These concepts make it easier to reason about your state and your state changes. It makes it easier to reason about it on a component level and to a certain degree on a application level.

In a growing application it gets harder to reason about state changes. You can introduce bugs by operating on stale state when using an object over a function in `setState()`. You have to lift state around to share necessary or hide unnecessary state across components. It can happen that a component needs to lift up state, because its sibling component depends on it. Perhaps the component is far away in the component tree and you have to share the state across the whole component tree. It happens that components get involved to a greater extent in state management. But after all the main responsibility of components should be representing the UI, shouldn't it?

Because of all these reasons, there exist standalone solutions to take care of the state management. These solutions are not only used in React. However, that's what makes the React ecosystem such a powerful place. You can use different solutions to solve your problems. To address the problem of scaling state management, you might have heard of the libraries [Redux](http://redux.js.org/docs/introduction/) or [MobX](https://mobx.js.org/). You can use either of these solutions in a React application. They come with extensions, [react-redux](https://github.com/reactjs/react-redux) and [mobx-react](https://github.com/mobxjs/mobx-react), to integrate with React.

Redux and MobX are outside of the scope of this book. When you have finished the book, you will get guidance on how you can continue to learn React and its ecosystem. One learning path could be to learn Redux. Before you dive into the topic of external state management, I can recommend to read this [article](https://www.robinwieruch.de/redux-mobx-confusion/). It aims to give you a better understanding of how to learn external state management.

### Exercises:

* read more about [external state management and how to learn it](https://www.robinwieruch.de/redux-mobx-confusion/)

{pagebreak}

You have learned advanced state management in React! Let's recap the last chapters:

* React
  * lift state management up and down to suitable components
  * setState can use a function to prevent stale state bugs
  * existing external solutions that help you to tame the state

You can find the source code in the [official repository](https://github.com/rwieruch/hackernews-client/tree/72a0828790f365af9d68a9f529c8ebe5db2e9c7f).
