# Code Organization and Testing

The chapter will focus on keeping code organized in a scaling application, so we will cover the best practices for structuring your folders and files. We will also cover testing, an important practice to keep source code robust, as well as useful tools for debugging a React application. This chapter will step back from the practical application for a moment, so we can cover these topics in detail before moving on.

## ES6 Modules: Import and Export

In JavaScript ES6, you can import and export functionalities from modules. These can be functions, classes, components, constants, essentially anything you can assign to a variable. Modules can be single files or whole folders with one index file as entry point.

After we bootstrapped our application with *create-react-app* at the beginning, we already used several `import` and `export` statements in the initial files. The `import` and `export` statements help you to share code across multiple files. Historically there were already several solutions for this in the JavaScript environment, but it was a mess because there wasn't standardized method of performing this task. JavaScript ES6 added it as a native behavior eventually.

These statements embrace code splitting, where we distribute code across multiple files to keep it reusable and maintainable. The former is true because we can import a piece of code into multiple files. The latter is true because there is only one source where you maintain the piece of code.

We also want to think about code encapsulation, since not every functionality needs to be exported from a file. Some of these functionalities should only be used in files where they have been defined. File exports are basically a public API to a file, where only the exported functionalities are available to be reused elsewhere. This follows the best practice of encapsulation.

The following examples showcase the statements by sharing one or multiple variables across two files. In the end, the approach can scale to multiple files and could share more than simple variables.

The act of exporting one or multiple variables is called a named export:

{title="Code Playground: file1.js",lang="javascript"}
~~~~~~~~
const firstname = 'Robin';
const lastname = 'Wieruch';

export { firstname, lastname };
~~~~~~~~

And import them in another file with a relative path to the first file.

{title="Code Playground: file2.js",lang="javascript"}
~~~~~~~~
import { firstname, lastname } from './file1.js';

console.log(firstname);
// output: Robin
~~~~~~~~

You can also import all exported variables from another file as one object.

{title="Code Playground: file2.js",lang="javascript"}
~~~~~~~~
import * as person from './file1.js';

console.log(person.firstname);
// output: Robin
~~~~~~~~

Imports can have aliases, which are necessary when we import functionalities from multiple files that have the same named export.

{title="Code Playground: file2.js",lang="javascript"}
~~~~~~~~
import { firstname as username } from './file1.js';

console.log(username);
// output: Robin
~~~~~~~~

There is also the `default` statement, which can be used for a few cases:

* to export and import a single functionality
* to highlight the main functionality of the exported API of a module
* to have a fallback import functionality

{title="Code Playground: file1.js",lang="javascript"}
~~~~~~~~
const robin = {
  firstname: 'Robin',
  lastname: 'Wieruch',
};

export default robin;
~~~~~~~~

You have to leave out the curly braces to import the default export.

{title="Code Playground: file2.js",lang="javascript"}
~~~~~~~~
import developer from './file1.js';

console.log(developer);
// output: { firstname: 'Robin', lastname: 'Wieruch' }
~~~~~~~~

The import name can differ from the exported default name, and it can be used with the named export and import statements:

{title="Code Playground: file1.js",lang="javascript"}
~~~~~~~~
const firstname = 'Robin';
const lastname = 'Wieruch';

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

Import the default or the named exports in another file:

{title="Code Playground: file2.js",lang="javascript"}
~~~~~~~~
import developer, { firstname, lastname } from './file1.js';

console.log(developer);
// output: { firstname: 'Robin', lastname: 'Wieruch' }
console.log(firstname, lastname);
// output: Robin Wieruch
~~~~~~~~

You can spare the extra lines, and export the variables directly for named exports.

{title="Code Playground: file1.js",lang="javascript"}
~~~~~~~~
export const firstname = 'Robin';
export const lastname = 'Wieruch';
~~~~~~~~

These are the main functionalities for ES6 modules. They help you to organize your code, to maintain it, and to design reusable module APIs. You can also export and import functionalities to test them which you will do in a later chapter.

### Exercises:

* Read about [ES6 import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)
* Read about [ES6 export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)

## Code Organization with ES6 Modules

You might wonder why we didn't follow the best practices of putting components into different files for the *src/App.js* file. We already have multiple components in the file that can be defined in their own files/folders (modules). For the sake of learning React, it is practical to keep these items in one place. Once your React application grows, you should consider splitting these components into multiple modules so it can scale properly.

In the following, I propose several module structures you can apply. You can apply them as an exercise later, but we will continue developing our app with the *src/App.js* file to keep things simple.

Here is one possible module structure:

{title="Folder Structure",lang="text"}
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

This one separates the components into their own files, but it doesn't look too promising. You can see a lot of naming duplications, and only the file extension differs. Another module structure could be:

{title="Folder Structure",lang="text"}
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

Now it looks cleaner than before. The index naming of a file describes it as an entry point file to the folder. It is just a common naming convention, but you can use your own naming as well. In this module structure, a component is defined by its component declaration in the JavaScript file, but also by its style and tests. If you have trouble finding your components this way while searching for them in your editor/IDE, search for paths rather than files (e.g. search for "Table index").

Another step is extracting the constant variables from the App component, which were used to compose the Hacker News API URL.

{title="Folder Structure",lang="text"}
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
      index.css
    Button/
      index.js
      test.js
      index.css
    ...
~~~~~~~~

Naturally, the modules would split up into *src/constants/* and *src/components/*. The *src/constants/index.js* file could look like the following:

{title="Code Playground: src/constants/index.js",lang="javascript"}
~~~~~~~~
export const DEFAULT_QUERY = 'redux';
export const DEFAULT_HPP = '100';
export const PATH_BASE = 'https://hn.algolia.com/api/v1';
export const PATH_SEARCH = '/search';
export const PARAM_SEARCH = 'query=';
export const PARAM_PAGE = 'page=';
export const PARAM_HPP = 'hitsPerPage=';
~~~~~~~~

The *App/index.js* file could import these variables in order to use them.

{title="Code Playground: src/components/App/index.js",lang="javascript"}
~~~~~~~~
import {
  DEFAULT_QUERY,
  DEFAULT_HPP,
  PATH_BASE,
  PATH_SEARCH,
  PARAM_SEARCH,
  PARAM_PAGE,
  PARAM_HPP,
} from '../../constants/index.js';

...
~~~~~~~~

When you use the *index.js* naming convention, you can omit the filename from the relative path.

{title="Code Playground: src/components/App/index.js",lang="javascript"}
~~~~~~~~
import {
  DEFAULT_QUERY,
  DEFAULT_HPP,
  PATH_BASE,
  PATH_SEARCH,
  PARAM_SEARCH,
  PARAM_PAGE,
  PARAM_HPP,
# leanpub-start-insert
} from '../../constants';
# leanpub-end-insert

...
~~~~~~~~

The *index.js* file naming is convention was introduced in the Node.js world, where the index file is the entry point to a module. It describes the public API to the module. External modules are only allowed to use the *index.js* file to import shared code from the module. Consider the following module structure to demonstrate it:

{title="Folder Structure",lang="text"}
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

The *Buttons/* folder has multiple button components defined in its distinct files. Each file can `export default` the specific component, making it available to *Buttons/index.js*. The *Buttons/index.js* file imports all different button representations and exports them as public module API.

{title="Code Playground: src/Buttons/index.js",lang="javascript"}
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

{title="Code Playground: src/App/index.js",lang="javascript"}
~~~~~~~~
import {
  SubmitButton,
  SaveButton,
  CancelButton
} from '../Buttons';
~~~~~~~~

However, it can be seen as bad practice to reach into other files than the *index.js* in the module, as it breaks the rules of encapsulation.

{title="Code Playground: src/App/index.js",lang="javascript"}
~~~~~~~~
// bad practice, don't do it
import SubmitButton from '../Buttons/SubmitButton';
~~~~~~~~

We refactored the source code in a module with the constraints of encapsulation. Again, we'll keep things simple in this book, but you should always refactor your source code to keep it clean.

### Exercises:

* Refactor your *src/App.js* file into multiple component modules when you finished the book

## Snapshot Tests with Jest

Testing source code is essential to programming, and should be seen as mandatory. We want to keep the quality of your code high and make sure everything works before using it in production. We will use the testing pyramid as our guide.

The testing pyramid includes end-to-end tests, integration tests, and unit tests. A unit test is for an isolated and small block of code, such a single function. However, sometimes units work well in isolation but not in combination with other units, so they need to be tested as a group. That's where integration tests can help us figure out if units work well together. An end-to-end test is a simulation of a real-life scenario, such as the automated setup of a browser simulating the login flow in a web application. Where unit tests are fast and easy to write and maintain, end-to-end tests are at the opposite of the spectrum.

We want to have many unit tests covering the isolated functions. After that, we can use several integration tests to make sure the most important functions work in combination as expected. Finally, we may need a few end-to-end tests to simulate critical scenarios.

The foundation for testing in React are component tests, generalized partly as unit tests and partly as snapshot tests. Unit tests for our components will be covered in the next chapter with a library called Enzyme. In this section, we focus on snapshot tests, using [Jest](https://jestjs.io/).

Jest is a JavaScript testing framework used by Facebook. It is used for component tests by the React community. Fortunately, *create-react-app* already comes with Jest, so you don't need to set it up.

Before you can test your first components, you have to export them from your *src/App.js* file. Afterward, you can test them in a different file.

{title="src/App.js",lang="javascript"}
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

In your *App.test.js* file, you will find the first test that came with *create-react-app*. It verifies the App component will render without errors.

{title="src/App.test.js",lang="javascript"}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});
~~~~~~~~

The "it"-block describes one test case. It comes with a test description, and when you test it, it can either succeed or fail. Furthermore, you could wrap it into a "describe"-block that defines your test suite. A test suite could include a bunch of the "it"-blocks for one specific component. You will see "describe"-blocks later. Both blocks are used to separate and organize your test cases.

Note that the `it` function is acknowledged in the JavaScript community as the function where you run a single test. However, in Jest it is often found as an alias `test` function.

You can run the test cases using the interactive *create-react-app* test script on the command line. You will get the output for all test cases on your command line interface.

{title="Command Line",lang="text"}
~~~~~~~~
npm test
~~~~~~~~

Jest enables you to write snapshot tests. These tests make a snapshot of your rendered component and runs it against future snapshots. When a future snapshot changes, you will get notified in the test. You can either accept the snapshot change, because you changed the component implementation on purpose, or deny the change and investigate for the error. It complements unit tests very well, because you only test the differences of the rendered output. It doesn't add big maintenance costs since you can accept snapshots for intentional changes.

Jest stores snapshots in a folder so it can validate the diff against a future snapshot. This also lets use share snapshots across teams when having version control such as git in place.

Before writing your first snapshot test with Jest, you have to install its utility library:

{title="Command Line",lang="text"}
~~~~~~~~
npm install --save-dev react-test-renderer
~~~~~~~~

Now you can extend the App component test with your first snapshot test. First, import the new functionality from the node package and wrap your previous "it"-block for the App component into a descriptive "describe"-block. In this case, the test suite is only for the App component.

{title="src/App.test.js",lang="javascript"}
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
  it('renders without crashing', () => {
    const div = document.createElement('div');
    ReactDOM.render(<App />, div);
    ReactDOM.unmountComponentAtNode(div);
  });
# leanpub-start-insert

});
# leanpub-end-insert
~~~~~~~~

Now you can implement your first snapshot test by using a "test"-block:

{title="src/App.test.js",lang="javascript"}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import renderer from 'react-test-renderer';
import App from './App';

describe('App', () => {

  it('renders without crashing', () => {
    const div = document.createElement('div');
    ReactDOM.render(<App />, div);
    ReactDOM.unmountComponentAtNode(div);
  });

# leanpub-start-insert
  test('has a valid snapshot', () => {
    const component = renderer.create(
      <App />
    );
    const tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });

# leanpub-end-insert
});
~~~~~~~~

Run your tests again and observe how they succeed or fail. Once you change the output of the render block in your App component, the snapshot test should fail. Then you can decide to update the snapshot or investigate in your App component.

The `renderer.create()` function creates a snapshot of your App component. It renders it virtually, and then stores the DOM into a snapshot. Afterward, the snapshot is expected to match the previous version from the last test. This is how we make sure the DOM stays the same and doesn't change anything by accident.

Let's add more tests for our independent components. First, the Search component:

{title="src/App.test.js",lang="javascript"}
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

  it('renders without crashing', () => {
    const div = document.createElement('div');
    ReactDOM.render(<Search>Search</Search>, div);
    ReactDOM.unmountComponentAtNode(div);
  });

  test('has a valid snapshot', () => {
    const component = renderer.create(
      <Search>Search</Search>
    );
    const tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });

});
# leanpub-end-insert
~~~~~~~~

The Search component has two tests similar to the App component. The first test simply renders the Search component to the DOM and verifies that there is no error during the rendering process. If there would be an error, the test would break even though there isn't any assertion (e.g. expect, match, equal) in the test block. The second snapshot test is used to store a snapshot of the rendered component and to run it against a previous snapshot. It fails when the snapshot has changed.

Second, you can test the Button component whereas the same test rules as in the Search component apply.

{title="src/App.test.js",lang="javascript"}
~~~~~~~~
...
# leanpub-start-insert
import App, { Search, Button } from './App';
# leanpub-end-insert

...

# leanpub-start-insert
describe('Button', () => {

  it('renders without crashing', () => {
    const div = document.createElement('div');
    ReactDOM.render(<Button>Give Me More</Button>, div);
    ReactDOM.unmountComponentAtNode(div);
  });

  test('has a valid snapshot', () => {
    const component = renderer.create(
      <Button>Give Me More</Button>
    );
    const tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });

});
# leanpub-end-insert
~~~~~~~~

Finally, the Table component that you can pass a bunch of initial props to render it with a sample list.

{title="src/App.test.js",lang="javascript"}
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

  it('renders without crashing', () => {
    const div = document.createElement('div');
    ReactDOM.render(<Table { ...props } />, div);
  });

  test('has a valid snapshot', () => {
    const component = renderer.create(
      <Table { ...props } />
    );
    const tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });

});
# leanpub-end-insert
~~~~~~~~

Snapshot tests usually stay pretty basic. You only want to make sure the component doesn't change its output. Once it does, you have to decide if you accept the changes, otherwise you have to fix the component.

### Exercises:

* See how a snapshot test fails once you change your component's return value in the `render()` method
  * Accept or deny the snapshot change(s)
* Keep your snapshots tests up to date when the implementation of components change in next chapters
* Read about [Jest in React](https://jestjs.io/docs/en/tutorial-react)

## Unit Tests with Enzyme

[Enzyme](https://github.com/airbnb/enzyme) is a testing utility by Airbnb to assert, manipulate, and traverse React components. It is used to conduct unit tests to complement snapshot tests in React. First we have to install it along with its extension, since it doesn't come with *create-react-app*.

{title="Command Line",lang="text"}
~~~~~~~~
npm install --save-dev enzyme react-addons-test-utils enzyme-adapter-react-16
~~~~~~~~

Second, we include it in the test setup and we initialize its adapter.

{title="src/App.test.js",lang="javascript"}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import renderer from 'react-test-renderer';
# leanpub-start-insert
import Enzyme from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';
# leanpub-end-insert
import App, { Search, Button, Table } from './App';

# leanpub-start-insert
Enzyme.configure({ adapter: new Adapter() });
# leanpub-end-insert
~~~~~~~~

Now you can write your first unit test in the Table "describe"-block. You will use `shallow()` to render your component and assert that the Table was passed two items. The assertion simply checks if the element has two elements with the class `table-row`.

{title="src/App.test.js",lang="javascript"}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import renderer from 'react-test-renderer';
# leanpub-start-insert
import Enzyme, { shallow } from 'enzyme';
# leanpub-end-insert
import Adapter from 'enzyme-adapter-react-16';
import App, { Search, Button, Table } from './App';

...

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

Shallow renders the component without its child components, so you can dedicate the test to one component.

Enzyme has three rendering mechanisms in its API. You already know `shallow()`, but there is also `mount()` and `render()`. Both instantiate instances of the parent component and all child components. `mount()` gives you access to the component lifecycle methods. These are the rules of them as to when to use each mechanism:

* Always begin with a shallow test
* If `componentDidMount()` or `componentDidUpdate()` should be tested, use `mount()`
* If you want to test component lifecycle and children behavior, use `mount()`
* If you want to test a component's children rendering with less overhead than `mount()` and you are not interested in lifecycle methods, use `render()`

Continue to unit test your components, but be sure to keep the tests simple and maintainable. Otherwise you will have to refactor them once you change your components. This is one of the main reasons Facebook introduced snapshot tests with Jest.

### Exercises:

* Write a unit test with Enzyme for your Button component
* Keep your unit tests up to date during the following chapters
* Read about [Enzyme and its rendering API](https://github.com/airbnb/enzyme)
* Read about [testing React applications](https://www.robinwieruch.de/react-testing-tutorial)

## Component Interface with PropTypes

[TypeScript](https://www.typescriptlang.org/) and [Flow](https://flowtype.org/) are used to introduce a type system to JavaScript. A typed language is less error prone because the code gets validated based on its program text. Editors and other utilities can catch these errors before the program runs.

In the book, you will not introduce Flow or TypeScript, but another useful way to check your types in components: React comes with a built-in type checker to prevent bugs. You can use PropTypes to describe your component interface. All the props passed from a parent component to a child get validated based on the PropTypes interface assigned to the child.

This section will show you to make components type safe with PropTypes. I will omit the changes for the following chapters, since they add unnecessary code refactorings, but you should update them along the way to keep your components interface type safe.

First, install a separate package for React:

{title="Command Line",lang="text"}
~~~~~~~~
npm install prop-types
~~~~~~~~

Now, you can import the PropTypes.

{title="src/App.js",lang="javascript"}
~~~~~~~~
import React, { Component } from 'react';
import axios from 'axios';
# leanpub-start-insert
import PropTypes from 'prop-types';
# leanpub-end-insert
~~~~~~~~

Let's start to assign a props interface to the components:

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

# leanpub-start-insert
Button.propTypes = {
  onClick: PropTypes.func,
  className: PropTypes.string,
  children: PropTypes.node,
};
# leanpub-end-insert
~~~~~~~~

Essentially, we want to take every argument from the function signature and assign a PropType to it. The basic PropTypes for primitives and complex objects are:

* PropTypes.array
* PropTypes.bool
* PropTypes.func
* PropTypes.number
* PropTypes.object
* PropTypes.string

There are two more PropTypes that can define a renderable fragment (node), e.g. a string, and a React element:

* PropTypes.node
* PropTypes.element

You already used the `node` PropType for the Button component. There are more PropType definitions in the official React documentation.

At the moment, all the defined PropTypes for the Button are optional. The parameters can be `null` or `undefined`. But for several props you want to enforce that they be defined. To do this, we make it a requirement that these props are passed to the component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
Button.propTypes = {
# leanpub-start-insert
  onClick: PropTypes.func.isRequired,
# leanpub-end-insert
  className: PropTypes.string,
# leanpub-start-insert
  children: PropTypes.node.isRequired,
# leanpub-end-insert
};
~~~~~~~~

The `className` is not required, because it can default to an empty string. Next you will define a PropType interface for the Table component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
Table.propTypes = {
  list: PropTypes.array.isRequired,
  onDismiss: PropTypes.func.isRequired,
};
# leanpub-end-insert
~~~~~~~~

You can define the content of an array PropType more explicitly:

{title="src/App.js",lang="javascript"}
~~~~~~~~
Table.propTypes = {
  list: PropTypes.arrayOf(
    PropTypes.shape({
      objectID: PropTypes.string.isRequired,
      author: PropTypes.string,
      url: PropTypes.string,
      num_comments: PropTypes.number,
      points: PropTypes.number,
    })
  ).isRequired,
  onDismiss: PropTypes.func.isRequired,
};
~~~~~~~~

Only the `objectID` is required, because some of the code depends on it. The other properties are only displayed, so they are not required. Moreover you cannot be sure the Hacker News API always has a defined property for each object in the array.

You can also define default props in your component. The `className` property has an ES6 default parameter in the component signature:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Button = ({
  onClick,
  className = '',
  children
}) =>
  ...
~~~~~~~~

Replace it with the internal React default prop:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const Button = ({
  onClick,
  className,
  children
}) =>
# leanpub-end-insert
  <button
    onClick={onClick}
    className={className}
    type="button"
  >
    {children}
  </button>

# leanpub-start-insert
Button.defaultProps = {
  className: '',
};
# leanpub-end-insert
~~~~~~~~

Like the ES6 default parameter, the default prop ensures the property is set to a default value when the parent component doesn't specify it. The PropType type check happens after the default prop is evaluated.

If you run your tests again, you might see PropType errors for your components on your command line. It happens when we don't define all props for components in the tests that are required in the PropType definition. The tests themselves all pass correctly though. Make sure to pass all required props to the components in your tests to avoid these errors.

### Exercises:

* Define the PropType interface for the Search component
* Add and update the PropType interfaces when you add and update components in the next chapters
* Read about [React PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html)

## Debugging with React Developer Tools

[React Developer Tools](https://github.com/facebook/react-devtools) lets you inspect the React components hierarchy, props and state. It comes as a browser extension for Chrome and Firefox and as a standalone app that works with other environments. Once installed, the extension icon will light up on websites using React. On such pages, you will see a tab called "React" in your browser's developer tools.

Let's try it on our Hacker News application. On most browsers, a quick way to bring the *dev tools* up is to right-click on the page and than hit "Inspect". Do it when your application is loaded, then click on the "React" tab. You should see its elements hierarchy, being `<App>` the root element. If you expand it, you will find instances of your `<Search>`, `<Table>` and `<Button>` components, as well.

The extension shows on the side pane the component's state and props for the selected element. For instance, if you click on `<App>`, you will see that it has no props, but it already has a state. A very straightforward debugging technique is monitoring your application's state changes from user interaction.

First, check the "Highlight Updates" option, usually above the elements tree. Second, type a different search term in the application's input field. Only `searchTerm` will be changed in the component's state. We already knew that would happen, but now we can see it working as planned.

Finally, press the "Search" button. The `searchKey` state immediately changes to same value as `searchTerm`, and then the response object is added to `results`. The asynchronous nature of your code is now visible.

If you right-click on any element, a dropdown menu will show several useful options. For instance, you could copy the element's props or name, find the corresponding DOM node, or jump to the application's source code in the browser. The last option is very useful for inserting breakpoints and debugging your JavaScript functions.

### Exercises:

* Install the [React Developer Tools](https://github.com/facebook/react-devtools) extension on your favorite browser
  * Run your Hacker News Clone application and inspect it using the extension
  * Experiment with state and props changes
  * Watch what happens when you trigger an asynchronous request
  * Perform several requests, including repeated ones. Watch the cache mechanism working
* Read about [how to debug your JavaScript functions in the browser](https://developers.google.com/web/tools/chrome-devtools/javascript/)
* Read about (and use) [the React Profiler](https://reactjs.org/blog/2018/09/10/introducing-the-react-profiler.html)

You have learned how to organize and test React code! Let's recap the last chapter:

* **React**
  * PropTypes let you define type checks for components
  * Jest allows you to write snapshot tests for your components
  * Enzyme allows you to write unit tests for your components
  * React Developer Tools is a helpful debugging tool
* **ES6**
  * import and export statements help you to organize your code
* **General**
  * code organization allows you to scale your application with best practices

You can find the source code in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.4).
