# Code Organization and Testing

The chapter will focus on important topics to keep your code maintainable in a scaling application. You will learn about code organization to embrace best practices when structuring your folders and files. Another aspect you will learn is testing, which is important to keep your code robust.

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

Let's add more tests for our independent components. First the Search component:

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

Second the Button component:

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

Last but not least, the Table component:

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

Snapshot tests usually stay pretty basic. You only want to cover that the component doesn't change its output. Once its changes the output, you have to decide if you accept the changes. Otherwise you have to fix the component when the output is not the desired output.

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

{pagebreak}

You can find the source code in the [official repository](https://github.com/rwieruch/hackernews-client/tree/e67239acf624c014453332236a8d5381787cde4e).

You have learned how to organize your code and how to test it! Let's recap the last chapters:

* React
  * Jest allows you to write snapshot tests for your components
  * Enzyme allows you to write unit tests for your components
* ES6
  * import and export statements help you to organize your code
* General
  * code organization allows you to scale your application with best practices