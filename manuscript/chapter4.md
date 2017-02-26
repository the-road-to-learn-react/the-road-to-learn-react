# Advanced React Components

The chapter will focus on the implementation of advanced React components. Before you jump into this, you will need to know how to export and import and how to test your components. Afterward you are prepared to implement your own higher order components and advanced interactions in React. Along the way you will learn more advanced internal React behavior.

## ES6 Modules: Import and Export

In JavaScript ES6 you can import and export functionalities from modules. These functionalities can be functions, classes, components, constants etc. Basically everything that you can assign to a variable. The modules can be single files or whole folders with one index file as entry point.

In the beginning of the book, after you have bootstrapped your application with *create-react-app*, you already had several `import` and `export` statements across your initial files. Now it is the appropriate time to explain these.

The `import` and `export` statements help you to share code across multiple files. Before there were already several solutions for this in the JavaScript environment. It was a mess, because you would want to follow one stardardized way rather than having several approaches for the same thing. Now it is a native behavior in JavaScript ES6.

Additionally these statements embrace code splitting. You distribute your code across multiple files to keep it reusable and maintainable. The former is true because you can import the piece of code in multiple files. The latter is true because you have one single source where you maintain the piece of code.

Last but not least, it helps you to think about code encapsulation. Not every functionality needs to get exported from a file. Some of these functionalities should only be used in the file where they have been defined. The exports of a file are basically the public API to the file. Only the exported functionalities are available to be reused somewhere else. It follows the best practice of encapsulation.

But let's get practical. How do these `import` and `export` statements work? The following examples showcase the statements by sharing one or multiple variables across two files. In the end, the approach can scale to multiple files and could share more than simple variables.

You can export one or multiple variables. It is called a named export.

*file1.js*

{lang=javascript}
~~~~~~~~
const firstname = 'robin';
const lastname = 'wieruch';

export { firstname, lastname };
~~~~~~~~

And import them in another file with a relative path to the first file.

*file2.js*

{lang=javascript}
~~~~~~~~
import { firstname, lastname } from './file1.js';

console.log(firstname);
// output: robin
~~~~~~~~

You can also import all exported variables from another file at once.

*file2.js*

{lang=javascript}
~~~~~~~~
import * as person from './file1.js';

console.log(person.firstname);
// output: robin
~~~~~~~~

Imports can have an alias. It can happen that you import functionalities from multiple files that have the same named export. That's why you can use an alias.

*file2.js*

{lang=javascript}
~~~~~~~~
import { firstname as foo } from './file1.js';

console.log(foo);
// output: robin
~~~~~~~~

Last but not least there exists the `default` statement. It can be used for a few use cases:

* to export and import a single functionality
* to highlight the main functionality of the exported API
* to have a fallback import functionality

*file1.js*

{lang=javascript}
~~~~~~~~
const robin = {
  firstname: 'robin',
  lastname: 'wieruch',
};

export default robin;
~~~~~~~~

*file2.js*

{lang=javascript}
~~~~~~~~
import developer from './file1.js';

console.log(developer);
// output: { firstname: 'robin', lastname: 'wieruch' }
~~~~~~~~

The import name can differ from the exported default name. You can also use it in conjunction with the named export and import statements.

*file1.js*

{lang=javascript}
~~~~~~~~
const firstname = 'robin';
const lastname = 'wieruch';

const person = {
  firstname,
  lastname,
};

export {
  firstname,
  lastname,
};

export default person;
~~~~~~~~

*file2.js*

{lang=javascript}
~~~~~~~~
import developer, { firstname, lastname } from './file1.js';

console.log(developer);
// output: { firstname: 'robin', lastname: 'wieruch' }
console.log(firstname, lastname);
// output: robin wieruch
~~~~~~~~

In named exports you can spare additional lines and export the variables directly.

*file1.js*

{lang=javascript}
~~~~~~~~
export const firstname = 'robin';
export const lastname = 'wieruch';
~~~~~~~~

These are the main functionalities for ES6 modules. They help you to organize your code, to maintain your code and to design reusable module APIs. You can also export and import functionalities to test them. You will do that in one of the following chapters.

### Exercises:

* read more about [ES6 import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
* read more about [ES6 export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

## Code Organization with ES6 Modules

You might wonder: Why didn't we follow the best practices of code splitting for the *src/App.js* file? In the file we already have multiple components which could be defined in their own files/folders (modules). For the sake of learning React, it is practical to keep these things at one place. But once your React application grows, you should consider to split up these components into multiple modules. Only that way your application scales.

In the following I will propose several module structures you *could* apply. You can either apply them as an exercise or at the end of the book. For the sake of the book and learning React, I will not perform the code splitting and will continue the following chapters with the *src/App.js* file.

One possible module structure could be:

{lang=javascript}
~~~~~~~~
src/
  index.js
  index.css
  App.js
  App.test.js
  App.css
  Button.js
  Button.test.js
  Button.css
  Table.js
  Table.test.js
  Table.css
  Search.js
  Search.test.js
  Search.css
~~~~~~~~

It doesn't look too promising. You can see a lot of naming duplications and only the file extension differs. Another module structure could be:

{lang=javascript}
~~~~~~~~
src/
  index.js
  index.css
  App/
    index.js
    test.js
    index.css
  Button/
    index.js
    test.js
    index.css
  Table/
    index.js
    test.js
    index.css
  Search/
    index.js
    test.js
    index.css
~~~~~~~~

It looks cleaner than before. Another step could be extracting the constant variables from the App component. These constants were used to compose the Hacker News API url.

{lang=javascript}
~~~~~~~~
src/
  index.js
  index.css
# leanpub-start-insert
  constants/
    index.js
  components/
# leanpub-end-insert
    App/
      index.js
      test.js
      index..css
    Button/
      index.js
      test.js
      index..css
    ...
~~~~~~~~

Naturally the modules would split up into submodules *constants/* and *components/*.

Now the *constants/index.js* file could look like the following:

{lang=javascript}
~~~~~~~~
export const DEFAULT_QUERY = 'redux';
export const DEFAULT_PAGE = 0;
export const DEFAULT_HPP = '100';

export const PATH_BASE = 'https://hn.algolia.com/api/v1';
export const PATH_SEARCH = '/search';
export const PARAM_SEARCH = 'query=';
export const PARAM_PAGE = 'page=';
export const PARAM_HPP = 'hitsPerPage=';
~~~~~~~~

The *App/index.js* file could import the variables in order to use them.

{lang=javascript}
~~~~~~~~
import {
  DEFAULT_QUERY,
  DEFAULT_PAGE,
  DEFAULT_HPP,

  PATH_BASE,
  PATH_SEARCH,
  PARAM_SEARCH,
  PARAM_PAGE,
  PARAM_HPP,
} from '../constants/index.js';

...
~~~~~~~~

When you use the *index.js* naming convention, you can omit the filename from the relative path.

{lang=javascript}
~~~~~~~~
import {
  DEFAULT_QUERY,
  DEFAULT_PAGE,
  DEFAULT_HPP,

  PATH_BASE,
  PATH_SEARCH,
  PARAM_SEARCH,
  PARAM_PAGE,
  PARAM_HPP,
# leanpub-start-insert
} from '../constants';
# leanpub-end-insert

...
~~~~~~~~

But what's behind the *index.js* file naming? The convention was introduced in the node.js world. The index file is the entry point to a module. It describes the public API to the module. External modules are only allowed to use the *index.js* file to import shared code from the module. Consider the following made up module structure to demonstrate it:

{lang=javascript}
~~~~~~~~
src/
  index.js
  App/
    index.js
  Buttons/
    index.js
    SubmitButton.js
    SaveButton.js
    CancelButton.js
~~~~~~~~

The *Buttons/* folder has multiple button components defined in its distinct files. Each file can `export default` the specific component making it available to *Buttons/index.js*. The *Buttons/index.js* file imports all different button represantations and exports them as public module API.

*src/Buttons/index.js*

{lang=javascript}
~~~~~~~~
import SubmitButton from './SubmitButton';
import SaveButton from './SaveButton';
import CancelButton from './CancelButton';

export {
  SubmitButton,
  SaveButton,
  CancelButton,
};
~~~~~~~~

Now the *src/App/index.js* can import the buttons from the public module API located in the *index.js* file.

*src/App/index.js*

{lang=javascript}
~~~~~~~~
import {
  SubmitButton,
  SaveButton,
  CancelButton
} from '../Buttons';
~~~~~~~~

By going with this constraint, it would be a bad practice to reach into other files in the module. It would break the rules of encapsulation.

*src/App/index.js*

{lang=javascript}
~~~~~~~~
// bad practice, don't do it
import SubmitButton from '../Buttons/SubmitButton';
~~~~~~~~

Now you know how you could refactor your source code in modules with the constraints of encapsulation. As I said, for the sake of keeping the tutorial simple I will not apply these changes. But you should do the refactoring eventually.

### Exercises:

* refactor your *src/App.js* file into multiple component modules
  * keep in mind that the following chapters will not apply the refactoring
  * keep in mind that you could do the final refactoring as last exercise when you finished the book

## Snapshot Tests with Jest

[Jest](https://facebook.github.io/jest/) is a JavaScript testing framework. At Facebook it is used to validate the JavaScript code. In the React community it is used for React components test coverage. Fortunately *create-react-app* already comes with Jest.

Let's start to test your first components. Before you can do that, you have to export the components from your *App.js* file to test them during the chapter.

{lang=javascript}
~~~~~~~~
...

class App extends Component {
  ...
}

...

export default App;

# leanpub-start-insert
export {
  Button,
  Search,
  Table,
};
# leanpub-end-insert
~~~~~~~~

In your *App.test.js* file you will find a first test. It verifies that the App component renders without any errors.

{lang=javascript}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
});
~~~~~~~~

You can run it by using the interactive *create-react-app* scripts on the command line.

{lang=javascript}
~~~~~~~~
npm run test
~~~~~~~~

Now Jest enables you to write Snapshot tests. These tests make a snapshot of your rendered component and run this snapshot against future snapshots. When a future snapshot changes you will get notified during the test. You can either accept the snapshot change, because you changed the component implementation on purpose, or deny the change and investigate for an error.

Jest stores the snapshots in a folder. Only that way it can show the diff to future snapshots. Additionally the snapshots can be shared across teams.

You have to install an utility library before you can write your first Snapshot test.

{lang=javascript}
~~~~~~~~
npm install --save-dev react-test-renderer
~~~~~~~~

Now you can extend the App component test with your first Snapshot test.

{lang=javascript}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
# leanpub-start-insert
import renderer from 'react-test-renderer';
# leanpub-end-insert
import App from './App';

# leanpub-start-insert
describe('App', () => {
# leanpub-end-insert

  it('renders', () => {
    const div = document.createElement('div');
    ReactDOM.render(<App />, div);
  });

# leanpub-start-insert
  test('snapshots', () => {
    const component = renderer.create(
      <App />
    );
    let tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });
# leanpub-end-insert

# leanpub-start-insert
});
# leanpub-end-insert
~~~~~~~~

Run your tests again and see how the tests either succeed or fail. They should succeed. Once you change the output of the render block in your App component, the Snapshot test should fail. Then you can decide to update the snapshot or investigate in your App component.

Let's add more tests for our independent components. First the Search component.

{lang=javascript}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import renderer from 'react-test-renderer';
# leanpub-start-insert
import App, { Search } from './App';
# leanpub-end-insert

...

# leanpub-start-insert
describe('Search', () => {

  it('renders', () => {
    const div = document.createElement('div');
    ReactDOM.render(<Search>Search</Search>, div);
  });

  test('snapshots', () => {
    const component = renderer.create(
      <Search>Search</Search>
    );
    let tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });

});
# leanpub-end-insert
~~~~~~~~

Second the Button component.

{lang=javascript}
~~~~~~~~
...
# leanpub-start-insert
import App, { Search, Button } from './App';
# leanpub-end-insert

...

# leanpub-start-insert
describe('Button', () => {

  it('renders', () => {
    const div = document.createElement('div');
    ReactDOM.render(<Button>Give Me More</Button>, div);
  });

  test('snapshots', () => {
    const component = renderer.create(
      <Button>Give Me More</Button>
    );
    let tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });

});
# leanpub-end-insert
~~~~~~~~

Last but not least for the Table component.

{lang=javascript}
~~~~~~~~
...
# leanpub-start-insert
import App, { Search, Button, Table } from './App';
# leanpub-end-insert

...

# leanpub-start-insert
describe('Table', () => {

  const props = {
    list: [
      { title: '1', author: '1', num_comments: 1, points: 2, objectID: 'y' },
      { title: '2', author: '2', num_comments: 1, points: 2, objectID: 'z' },
    ],
  };

  it('renders', () => {
    const div = document.createElement('div');
    ReactDOM.render(<Table { ...props } />, div);
  });

  test('snapshots', () => {
    const component = renderer.create(
      <Table { ...props } />
    );
    let tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });

});
# leanpub-end-insert
~~~~~~~~

Snapshot tests usually stay pretty basic. You only want to cover that the component doesn't change its output.

### Exercises:

* see how the Snapshot tests fail once you change your component implementation
  * either accept or deny the snapshot change
* keep your snapshots tests up to date when the implementation changes in future chapters
* read more about [Jest in React](https://facebook.github.io/jest/docs/tutorial-react.html)
* read more about ES6 [export](https://developer.mozilla.org/en/docs/web/javascript/reference/statements/export) and [import](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/import)

## Unit Tests with Enzyme

[Enzyme](https://github.com/airbnb/enzyme) is a testing utility by Airbnb to assert, manipulate and traverse your React components. You can use it to conduct unit tests to complement your snapshot tests.

Let's see how you can use enzyme. First you have to install it since it doesn't come with *create-react-app*.

{lang=javascript}
~~~~~~~~
npm install --save-dev enzyme react-addons-test-utils
~~~~~~~~

Now you can write your first unit test in the Table describe block. You will use `shallow()` to render your component and assert that the Table has two items.

{lang=javascript}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import renderer from 'react-test-renderer';
# leanpub-start-insert
import { shallow } from 'enzyme';
# leanpub-end-insert
import App, { Search, Button, Table } from './App';

describe('Table', () => {

  const props = {
    list: [
      { title: '1', author: '1', num_comments: 1, points: 2, objectID: 'y' },
      { title: '2', author: '2', num_comments: 1, points: 2, objectID: 'z' },
    ],
  };

  ...

# leanpub-start-insert
  it('shows two items in list', () => {
    const element = shallow(
      <Table { ...props } />
    );

    expect(element.find('.table-row').length).toBe(2);
  });
# leanpub-end-insert

});
~~~~~~~~

Shallow renders the component without child components. You can make the test very dedicated to one component.

Enzyme has overall three rendering mechanisms in its API. You already know `shallow()`, but there also exist `mount()` and `render()`. Both instantiate instances of the parent component and all child components. Additionally `mount()` gives you more access to the component lifecycle methods. But when to use which render mechanism? Here some rules of thumb:

* Always begin with a shallow test
* If `componentDidMount()` or `componentDidUpdate()` should be tested, use `mount()`
* If you want to test component lifecycle and children behavior, use `mount()`
* If you want to test children rendering with less overhead than `mount()` and you are not interested in lifecycle methods, use `render()`

You could continue to unit test your components. But make sure to keep the tests simple and maintainable. Otherwise you will have to refactor them once you change your components. That's why Facebook introduced Snapshot tests with Jest in the first place.

### Exercises:

* keep your unit tests up to date during the following chapters
* read more about [enzyme and its rendering API](https://github.com/airbnb/enzyme)

## Reference a DOM Element

The `ref` attribute gives you access to a DOM node in your elements. Usually that is an anti pattern in React, because you should use its declarative way of doing things and its unidirectional data flow. But there are certain cases where you need access to the DOM node. The official documentation mentions three use cases:

* to use the DOM API (focus, media playback etc.)
* to invoke imperative DOM node animations
* to integrate with third-party libraries that need the DOM node (e.g. [D3.js](https://d3js.org/))

Let's do it by example with the Search component. When the application renders the first time, the input field should be focused. This chapter will show you how it works, but since it is not very useful for the application itself, we will omit the changes after the chapter.

In general, you can use the `ref` attribute in functional stateless components and in ES6 class components. In the example of the focus use case, you will need a lifecycle method. That's why I will first show you the approach of using the `ref` attribute with an ES6 class component.

The initial step is to refactor the functional stateless component to an ES6 class component.

{lang=javascript}
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

{lang=javascript}
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

Now you can focus the input field when the component mounted by using the `this` object and the appropriate lifecycle method.

{lang=javascript}
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

The input field should be focused when the application is rendered. But how would you get access to the `ref` in a functional stateless component without the `this` object? The following functional stateless component demonstrates it:

{lang=javascript}
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

In the example of the focus use case it wouldn't help you, because you have no lifecycle method to trigger the DOM API. But there are other use cases where it can make sense to use a functional stateless component with the `ref` attribute.

### Exercises

* read more about [the ref attribute in React](https://facebook.github.io/react/docs/refs-and-the-dom.html)

## Loading ...

Now let's get back to the application. You might want to show a loading indicator when you submit a search request to the Hacker News API. The request is asynchronous and you should show your user some feedback that something is about to happen. Let's define a reusable Loading component in your *App.js* file.

{lang=javascript}
~~~~~~~~
const Loading = () =>
  <div>Loading ...</div>
~~~~~~~~

Now you will need a property to store the loading state. Based on the loading state you can decide to show the Loading component later on.

{lang=javascript}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      results: null,
      searchKey: '',
      searchTerm: DEFAULT_QUERY,
# leanpub-start-insert
      isLoading: false,
# leanpub-end-insert
    };

    ...
  }

  ...

}
~~~~~~~~

The initial value of that property is false. You don't load anything before the App component is mounted.

When you make the request, you set a loading state to true. Eventually the request will succeed and you can set the loading state to false.

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

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
      # leanpub-start-insert
      isLoading: false
      # leanpub-end-insert
    });
  }

  fetchSearchTopstories(searchTerm, page) {
# leanpub-start-insert
    this.setState({ isLoading: true });
# leanpub-end-insert

    fetch(`${PATH_BASE}${PATH_SEARCH}?${PARAM_SEARCH}${searchTerm}&${PARAM_PAGE}${page}&${PARAM_HPP}${DEFAULT_HPP}`)
      .then(response => response.json())
      .then(result => this.setSearchTopstories(result));
  }

  ...

}
~~~~~~~~

In the last step you will use the Loading component in your App. A conditional rendering based on the loading state will decide whether you show a Loading or Button component. The latter one is your button to fetch more data.

{lang=javascript}
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
        ...
        <div className="interactions">
# leanpub-start-insert
          { isLoading
            ? <Loading />
            : <Button
              onClick={() => this.fetchSearchTopstories(searchKey, page + 1)}>
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

Initially the Loading component will show up when you start your application, because you make a request on `componentDidMount()`. There is no Table component, because the list is empty. When the response returns from the Hacker News API, the result is shown, the loading state is set to false and the Loading component disappears. The "More" button to fetch more data appears. Once you fetch more data, the button will disappear. Instead the Loading component will show up.

### Exercises:

* use a library like [Font Awesome](http://fontawesome.io/) to show a loading icon instead of text

## Higher Order Components

Higher order components (HOC) are an advanced concept in React. HOCs are equivalent to higher order functions. They take any input - most of the time a component - and return a component as output. The returned component is an enhanced version.

Let's do a simple HOC which takes a component as input and returns a component. You can place it in your *App.js* file.

{lang=javascript}
~~~~~~~~
const withSomething = (Component) => (props) =>
  <Component { ...props } />
~~~~~~~~

In the example the input component would stay the same as the output component. It renders the same component instance and passes all of the props to the output component.

Now let's enhance the output component. The output component should show the Loading component, when the loading state is true, otherwise it should show the input component.

{lang=javascript}
~~~~~~~~
# leanpub-start-insert
const withLoading = (Component) => ({ isLoading, ...rest }) =>
  isLoading ? <Loading /> : <Component { ...rest } />
# leanpub-end-insert
~~~~~~~~

You use a conditional rendering based on the loading property. It will return the Loading component or input component.


Additionally you may have noticed the `{ isLoading, ...rest }` ES6 rest destructuring. It takes one property out of the object, but keeps the remaining object. It works with multiple properties as well. You might have already read about it in [destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).

In general it can be very efficient to spread an object as input for a component. See the difference in the following code snippet.

{lang=javascript}
~~~~~~~~
// before you would have to destructure the props before passing them
const { foo, bar } = props;
<SomeComponent foo={foo} bar={bar} />

// but you can use the object spread operator to pass all object properties
<SomeComponent { ...props } />
~~~~~~~~

Now you can use the HOC. In your use case you want to show either the "More" button or Loading component. The HOC can take the Button component as input. The enhanced output component is a ButtonWithLoading component.

{lang=javascript}
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
  isLoading ? <Loading /> : <Component { ...rest } />

# leanpub-start-insert
const ButtonWithLoading = withLoading(Button);
# leanpub-end-insert
~~~~~~~~


Everything is declared. As the last step, you have to use the ButtonWithLoading component, which receives the loading state as an additional property. While the HOC consumes the loading property, all other props get passed to the Button component.

{lang=javascript}
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
            onClick={() => this.fetchSearchTopstories(searchKey, page + 1)}>
            More
          </ButtonWithLoading>
# leanpub-end-insert
        </div>
      </div>
    );
  }
}
~~~~~~~~

Higher order components are an advanced technique in React. They have multiple purposes like improved reusability of components, greater abstraction, composability of components and manipulations of props, state and view.

### Exercises:

* experiment with the HOC you have created
* optionally implement another HOC

## Advanced Sorting


You have already implemented a client- and server-side search interaction. Since you have a Table component, it would make sense to enhance the Table with advanced interactions. What about enabling sorting by the Table columns?

It would be possible to write your own sort function, but personally I prefer to use a utility library for such cases. [Lodash](https://lodash.com/) is one of these utility libraries. Let's install and use it for the sort functionality.

{lang=javascript}
~~~~~~~~
npm install --save lodash
~~~~~~~~

Now you can import the sort functionality of lodash in your *App.js* file.

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
# leanpub-start-insert
import { sortBy } from 'lodash';
# leanpub-end-insert
import './App.css';
~~~~~~~~

You have several columns in your Table. Among these you have title, author, comments and points columns. You can define sort functions where each function takes a list and returns a list of items sorted by property. Additionally you will need one default sort function which doesn't sort but only returns the unsorted list.

{lang=javascript}
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

You can see that two of the sort functions return a reversed list. That's because you want to see the items with the highest comments and points rather than to see the items with the lowest.

The `SORTS` object allows you to reference any sort function now.

Again your App component is responsible for storing the state of the sort. The initial state will be the initial default sort function, which doesn't sort at all and returns the input list as output.

{lang=javascript}
~~~~~~~~
this.state = {
  results: null,
  searchKey: '',
  searchTerm: DEFAULT_QUERY,
  isLoading: false,
# leanpub-start-insert
  sortKey: 'NONE',
# leanpub-end-insert
};
~~~~~~~~

Once you choose a different `sortKey`, let's say `AUTHOR`, you will sort the list by the author property.


Now you can define a new sort method in your App component that simply sets a `sortKey` to your internal component state.

{lang=javascript}
~~~~~~~~
class App extends Component {

  constructor(props) {

    ...

    this.needsToSearchTopstories = this.needsToSearchTopstories.bind(this);
    this.setSearchTopstories = this.setSearchTopstories.bind(this);
    this.fetchSearchTopstories = this.fetchSearchTopstories.bind(this);
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

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  render() {
# leanpub-start-insert
    const {
      searchTerm,
      results,
      searchKey,
      isLoading,
      sortKey
    } = this.state;

# leanpub-end-insert
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
# leanpub-start-insert
          sortKey={sortKey}
          onSort={this.onSort}
# leanpub-end-insert
          onDismiss={this.onDismiss}
        />
        <div className="interactions">
          <ButtonWithLoading
            isLoading={isLoading}
            onClick={() => this.fetchSearchTopstories(searchKey, page + 1)}>
            More
          </ButtonWithLoading>
        </div>
      </div>
    );
  }
}
~~~~~~~~

The Table component is responsible for sorting the list. It takes one of the `SORT` functions by `sortKey` and passes the list as input. Afterward it still maps over the sorted list.

{lang=javascript}
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
    { SORTS[sortKey](list).map(item =>
# leanpub-end-insert
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
~~~~~~~~

In theory the list would get sorted by one of the functions. But the default sort is set to `NONE`. So far no one executes the `onSort()` method to change the `sortKey`. Let's extend the Table with a header row that uses Sort components in columns to sort each column.

{lang=javascript}
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
    { SORTS[sortKey](list).map(item =>
      ...
    )}
  </div>
~~~~~~~~

Each Sort component gets a specific `sortKey` and the general `onSort()` function. Internally it calls the method with the `sortKey` to set the specific key.

{lang=javascript}
~~~~~~~~
const Sort = ({ sortKey, onSort, children }) =>
  <Button onClick={() => onSort(sortKey)}>
    {children}
  </Button>
~~~~~~~~


As you can see, the Sort component reuses your common Button component. On click, each individual passed `sortKey` will get set by the `onSort()` method. Now you should be able to sort the list when you click on the column headers.

But a button in a column header looks a bit silly. Let's give the Sort a proper `className`.

{lang=javascript}
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

It should look nice now. The next goal would be to implement reverse sort as well. The list should get reverse sorted once you click a Sort component twice. First you need to define the reverse state.

{lang=javascript}
~~~~~~~~
this.state = {
  results: null,
  searchKey: '',
  searchTerm: DEFAULT_QUERY,
  isLoading: false,
  sortKey: 'NONE',
# leanpub-start-insert
  isSortReverse: false,
# leanpub-end-insert
};
~~~~~~~~

Now in your sort method you can evaluate if the list is reverse sorted. It is when `sortKey` in the state is the same as the incoming `sortKey` and the reverse state is not already set to true.

{lang=javascript}
~~~~~~~~
onSort(sortKey) {
# leanpub-start-insert
  const isSortReverse = this.state.sortKey === sortKey && !this.state.isSortReverse;
  this.setState({ sortKey, isSortReverse });
# leanpub-end-insert
}
~~~~~~~~

Again you can pass the reverse prop to your Table component.

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  render() {
# leanpub-start-insert
    const {
      searchTerm,
      results,
      searchKey,
      isLoading,
      sortKey,
      isSortReverse
    } = this.state;

# leanpub-end-insert
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
        ...
        <Table
          list={list}
          sortKey={sortKey}
# leanpub-start-insert
          isSortReverse={isSortReverse}
# leanpub-end-insert
          onDismiss={this.onDismiss}
          onSort={this.onSort}
        />
        ...
      </div>
    );
  }
}
~~~~~~~~

The Table has to have an arrow function block body to compute the data now.

{lang=javascript}
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
      { reverseSortedList.map(item =>
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

Last but not least you have to deal with one open question for the sake of an improved user experience. Can a user distinguish which column is actively sorted? So far it is not possible. Let's give the user a visual feedback. Each Sort component gets its specific `sortKey` already. It could be used to identify the activated sort. You can pass the `sortKey` from the internal component state as active sort key to your Sort component.

{lang=javascript}
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
          <span style={{ width: '10%' }}>
            Archive
          </span>
        </span>
      </div>
      { reverseSortedList.map(item =>
          ...
      )}
    </div>
  );
}
~~~~~~~~

Now in your Sort component you know based on the `sortKey` and `activeSortKey` if the sort is active. Give your Sort component an extra `class` attribute, when it is sorted, to give the user a visual feedback.

{lang=javascript}
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

The way to define the `class` is a bit clumsy, isn't it? There is a neat little library to get rid of this. First you have to install it.

{lang=javascript}
~~~~~~~~
npm install --save classnames
~~~~~~~~

And second you have to import it on top of your *App.js* file.

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import { sortBy } from 'lodash';
# leanpub-start-insert
import classNames from 'classnames';
# leanpub-end-insert
import './App.css';
~~~~~~~~

Now you can use it to define your component `className` with conditional classes.

{lang=javascript}
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

Your advanced sort interaction is complete now.

### Exercises:

* use a library like [Font Awesome](http://fontawesome.io/) to indicate the (reverse) sort
  * it could be an arrow up or down icon next to each Sort header
* read more about the [classnames library](https://github.com/JedWatson/classnames)

## Lift State

Only the App component is a stateful ES6 component in your application. It handles a lot of application state and logic (methods). Maybe you have noticed that you pass a lot of properties to your Table component. Most of the props are only used in the component. It makes no sense that the App component knows about them.

The sort functionality is only handled in the Table component. You could move it closer to the Table component. The App component doesn't need to know about it at all. The process of refactoring substate from one component to another is known as *lifting state*. In your case you want to move state that isn't used in the App component closer to the Table component. The state moves down from parent to child component.

In order to deal with state and methods in the Table component, it has to become an ES6 class component. The refactoring from functional stateless component to ES6 class component is straight forward.

Your Table component as functional stateless component:

{lang=javascript}
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

{lang=javascript}
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

{lang=javascript}
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

{lang=javascript}
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

{lang=javascript}
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

{lang=javascript}
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

{lang=javascript}
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

* read more about [React Lift State](https://facebook.github.io/react/docs/lifting-state-up.html)

{pagebreak}

Your *src/App.js* should look like the following by now:

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import { sortBy } from 'lodash';
import classNames from 'classnames';
import './App.css';

const DEFAULT_QUERY = 'redux';
const DEFAULT_PAGE = 0;
const DEFAULT_HPP = '100';

const PATH_BASE = 'https://hn.algolia.com/api/v1';
const PATH_SEARCH = '/search';
const PARAM_SEARCH = 'query=';
const PARAM_PAGE = 'page=';
const PARAM_HPP = 'hitsPerPage=';

const SORTS = {
  NONE: list => list,
  TITLE: list => sortBy(list, 'title'),
  AUTHOR: list => sortBy(list, 'author'),
  COMMENTS: list => sortBy(list, 'num_comments').reverse(),
  POINTS: list => sortBy(list, 'points').reverse(),
};

class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      results: null,
      searchKey: '',
      searchTerm: DEFAULT_QUERY,
      isLoading: false,
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
      },
      isLoading: false
    });
  }

  fetchSearchTopstories(searchTerm, page) {
    this.setState({ isLoading: true });

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
      searchKey,
      isLoading
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
          <ButtonWithLoading
            isLoading={isLoading}
            onClick={() => this.fetchSearchTopstories(searchKey, page + 1)}>
            More
          </ButtonWithLoading>
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

class Table extends Component {

  constructor(props) {
    super(props);

    this.state = {
      sortKey: 'NONE',
      isSortReverse: false,
    };

    this.onSort = this.onSort.bind(this);
  }

  onSort(sortKey) {
    const isSortReverse = this.state.sortKey === sortKey && !this.state.isSortReverse;
    this.setState({ sortKey, isSortReverse });
  }

  render() {
    const {
      list,
      onDismiss
    } = this.props;

    const {
      sortKey,
      isSortReverse,
    } = this.state;

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
              onSort={this.onSort}
              activeSortKey={sortKey}
            >
              Title
            </Sort>
          </span>
          <span style={{ width: '30%' }}>
            <Sort
              sortKey={'AUTHOR'}
              onSort={this.onSort}
              activeSortKey={sortKey}
            >
              Author
            </Sort>
          </span>
          <span style={{ width: '10%' }}>
            <Sort
              sortKey={'COMMENTS'}
              onSort={this.onSort}
              activeSortKey={sortKey}
            >
              Comments
            </Sort>
          </span>
          <span style={{ width: '10%' }}>
            <Sort
              sortKey={'POINTS'}
              onSort={this.onSort}
              activeSortKey={sortKey}
            >
              Points
            </Sort>
          </span>
          <span style={{ width: '10%' }}>
            Archive
          </span>
        </div>
        { reverseSortedList.map(item =>
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
    );
  }
}

const Sort = ({
  sortKey,
  activeSortKey,
  onSort,
  children
}) => {
  const sortClass = classNames(
    'button-inline',
    { 'button-active': sortKey === activeSortKey }
  );

  return (
    <Button
      onClick={() => onSort(sortKey)}
      className={sortClass}
    >
      {children}
    </Button>
  );
}

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
  isLoading ? <Loading /> : <Component { ...rest } />

const ButtonWithLoading = withLoading(Button);

export default App;

export {
  Button,
  Search,
  Table,
};
~~~~~~~~

You can find the source code in the [official repository](https://github.com/rwieruch/hackernews-client/tree/e67239acf624c014453332236a8d5381787cde4e).

You have learned advanced component techniques in React! Let's recap the last chapters:

* React
  * Jest allows you to write snapshot tests for your components
  * Enzyme allows you to write unit tests for your components
  * the ref attribute to reference DOM nodes
  * higher order components are a common way to build advanced components
  * implementation of advanced interactions in React
  * conditional classNames with a neat helper library
  * lift state up and down
* ES6
  * rest destructuring
