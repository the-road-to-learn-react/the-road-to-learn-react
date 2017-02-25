# Bootstrap your React App

The chapter gives you an introduction to React. You may ask yourself: Why should I learn React in the first place? You will get your answer to that question. Afterwards you will dive into the ecosystem by bootstrapping your first React application. Along the way you will get an introduction to JSX and ReactDOM. Be prepared for your first React components.

## Hi, my name is React.

**Why should you bother to learn React?** In recent years single page applications (SPA) have become popular. Frameworks like Angular, Ember and Backbone helped JavaScript developers to build modern web applications beyond the usage of jQuery. The list is not exhaustive. There exists a wide range of SPA frameworks. When you consider the release dates, most of them are among the first generation of SPAs: Angular 2010, Backbone 2010, Ember 2011.

The initial React release was 2013 by Facebook. React is not an SPA framework but a view library. It is the V in the MVC (model view controller). It only enables you to render components as viewable elements in a browser. Yet the whole ecosystem around React makes it possible to build single page applications.

But why should you consider using React over the first generation of SPA frameworks? While the first generation of frameworks tried to solve a lot of things at once, React only helps you to build your view layer. It's a library and not a framework. The idea behind it: Your view is a hierarchy of composeable components.

In React you can focus on your view before you introduce more aspects to your application. Every other aspect is another building block for your SPA. These building blocks are essential to build a mature application. They come with two advantages.

First you can learn the building blocks step by step. You don't have to worry about understanding them altogether. It is different from a framework that gives you every building block from the start. This book focuses on React as the first building block. More building blocks follow eventually.

Second all building blocks are interchangeable. It makes the ecosystem around React such an innovative place. Multiple solutions are competing with each other. You can pick the most appealing solution for you and your use case.

The first generation of SPA frameworks arrived at an enterprise level. React stays innovative and gets adopted by multiple tech thought leader companies like [Airbnb, Netflix and of course Facebook](https://github.com/facebook/react/wiki/Sites-Using-React). All of them invest in the future of React and are content with React and the ecosystem itself.

React is probably one of the best choices for building single page applications nowadays. It only delivers the view layer, but the ecosystem is a whole flexible and interchangeable framework. React has a slim API, an amazing ecosystem and a great community. You can read about my experiences [why I moved from Angular to React](https://www.robinwieruch.de/reasons-why-i-moved-from-angular-to-react/). I highly recommend to have an understanding why you would choose React over another framework or library. After all everyone is keen to experience where React will lead us in 2017 and beyond.

## Requirements

Before you start to read the book, you should be familiar with HTML, CSS and JavaScript (ES5). The book will teach JavaScript ES6 and beyond. If you are coming from a different SPA framework or library, you should already be familiar with the basics. If you have just started in web development, you should feel comfortable with HTML, CSS and JavaScript ES5 to learn React.

Every developer needs tools to build applications. You will need an editor and terminal (command line) tool. You can read my developer setup to organize your tools: [Developer Setup](https://www.robinwieruch.de/developer-setup/). It is adjusted for Mac developers, but you can substitue most of the tools for other operating system.

The editor is used to organize and write your code. The terminal is used to execute commands. A command can be to start your application, to run tests or to install other libraries for your project.

Last but not least you will need an installation of [node and npm](https://nodejs.org/en/). Both are used to manage libraries you will need along the way. You will install external node packages via npm (node package manager). These node packages can be libraries or whole frameworks.

You can verify your versions of node and npm on the command line. If you don't get any output in the terminal, you need to install node and npm first. These are my versions:

{lang=javascript}
~~~~~~~~
node --version
*v7.4.0
npm --version
*v4.0.5
~~~~~~~~

## node and npm

This chapter gives you a little crash course in node and npm. It is not exhaustive, but you will get all the necessary tools. If you are familiar with both of them, you can skip the chapter.

The **node package manager** (npm) allows you to install external **node packages** from the command line. These packages can be a set of utility functions, libraries or whole frameworks. They are the dependencies of your application. You can either install these packages to your global node package folder or to your local project folder.

Global node packages are accessible from everywhere in the terminal and you have to install them only once. You can install a global package easily by typing:

{lang=javascript}
~~~~~~~~
npm install -g <package>
~~~~~~~~

The `-g` flag tells npm to install the package globally. Local packages are used in your application. For instance, React as a library will be a local package which can be required in your application for usage. You can install it via the terminal by typing:

{lang=javascript}
~~~~~~~~
npm install <package>
~~~~~~~~

In the case of React it would be:

{lang=javascript}
~~~~~~~~
npm install react
~~~~~~~~

The installed package will automatically appear in a folder called *node_modules/*. But be careful. Whenever you install a local package you shouldn't forget the neat `--save` configuration:

{lang=javascript}
~~~~~~~~
npm install --save <package>
~~~~~~~~

The `--save` flag tells npm to store the package requirement in a file called *package.json*. The file can be found in your project folder.

Not every project folder comes with a *package.json* file though. There is a npm command to initialize a npm project and thus a *package.json* file. Only when you have that file, you can install new local packages via npm.

{lang=javascript}
~~~~~~~~
npm init -y
~~~~~~~~

The `-y` flag is a shortcut to initialize all the defaults in your *package.json*. If you don't use it, you have to decide how to configure the file.

One more word about the *package.json*. The file enables you to share your project with other developers without sharing all the packages. The file has all the references of node packages used in your project. These packages are called dependencies. Everyone can copy your project without the dependencies. The dependencies are references in the *package.json*. Someone who copies your project can install all packages by using `npm install` on the command line.

I want to cover one more npm command to prevent confusion:

{lang=javascript}
~~~~~~~~
npm install --save-dev <package>
~~~~~~~~

The `--save-dev` flag indicates that the node package is only used in the development environment. It will not be used in production. What kind of node package could that be? Imagine you want to test your application with the help of a node package. You need to install that package via npm, but want to exclude it from your production environment. There you don't want to test your application anymore. It should be tested already and work out of the box for users. That's only one use case where you would want to use the `--save-dev` flag.

You will encounter more npm commands on your way. But these will be sufficient for now.

### Exercises:

* read more about [npm](https://docs.npmjs.com/)

## Installation

There are multiple approaches to get started with a React application.

The first one is to use a CDN. That may sound more complicated than it is. A CDN is a content delivery network. Several companies have CDNs that host files publicly for users. These files can be libraries like React. After all a library can be only one JavaScript file. It can be hosted somewhere and you can require it in your application.

How to use a CDN to get started in React? You can inline the `<script>` tag in your HTML that points to a CDN address. To get started in React you need two files (libraries): react and react-dom.

{lang=javascript}
~~~~~~~~
<script src="https://unpkg.com/react@15/dist/react.js"></script>
<script src="https://unpkg.com/react-dom@15/dist/react-dom.js"></script>
~~~~~~~~

But why should you use a CDN when you have npm to install node packages (libraries)? When your application has a *package.json* file, you can install *react* and *react-dom* from the command line. You can install multiple node packages in one line with npm.

{lang=javascript}
~~~~~~~~
npm install --save react react-dom
~~~~~~~~

That approach is used to add React to an existing application.

Unfortunately that's not everything. You would have to deal with [Babel](http://babeljs.io/) to make your application aware of JSX, the React syntax, and JavaScript ES6. Babel transpiles your code that browsers can interpret ES6 and JSX. Not all browsers are capable of interpreting the syntax. The setup includes a lot of configuration and tools. It can be overwhelming for React beginners to bother with all the configuration. That's why Facebook introduced create-react-app. I will show you how to setup your application with no configuration in the next chapter.

### Exercises:

* read more about [React installation](https://facebook.github.io/react/docs/installation.html)

## create-react-app

In this book you will use [create-react-app](https://github.com/facebookincubator/create-react-app) to bootstrap your application. It's an opinionated yet zero-configuration starter kit for React introduced by Facebook in 2016. People like and would [recommend it to beginners by 96%](https://twitter.com/dan_abramov/status/806985854099062785). In *create-react-app* the tools and configuration evolve in the background while the focus is on the application implementation.

To get started, you will have to install the package to your global node packages. After that you always have it available on the command line to bootstrap new React applications.

{lang=javascript}
~~~~~~~~
npm install -g create-react-app
~~~~~~~~

You can check the version of *create-react-app* to verify a successful installation in your command line:

{lang=javascript}
~~~~~~~~
create-react-app --version
~~~~~~~~

Now you can bootstrap your first React application. We call it *hackernews*, but you can choose a different name. Afterward simply navigate into the folder:

{lang=javascript}
~~~~~~~~
create-react-app hackernews
cd hackernews
~~~~~~~~

Now you can open the application in your editor. The following folder structure will be presented to you:

{lang=javascript}
~~~~~~~~
hackernews/
  README.md
  node_modules/
  package.json
  .gitignore
  public/
    favicon.ico
    index.html
  src/
    App.css
    App.js
    App.test.js
    index.css
    index.js
    logo.svg
~~~~~~~~

In the beginning everything you need is located in the *src/* folder.

The main focus lies on the *App.js* file to implement React components. It will be used alone, but later you might want to split up your components into multiple files.

Additionally you will find a *App.test.js* file for tests and a *index.js* as entry point to the React world. You will get to know both files in a later chapter. Moreover there is a *App.css* and *index.css* file to style your application. They all come with default implementations when you open them.

Next to to the *src/* folder you will find the *package.json* file and *node_modules/* folder to manage your node packages.

Additionally *create-react-app* comes with the following npm scripts for your command line:

{lang=javascript}
~~~~~~~~
// Runs the application in http://localhost:3000
npm start

// Runs the tests
npm test

// Builds the application for production
npm run build
~~~~~~~~

The scripts are defined in your *package.json* too. Your boilerplate React application is up and running.

### Exercises:

* `npm start` your application and visit the page in your browser
* run the interactive `npm test` script
* make yourself familiar with the folder structure
* make yourself familiar with the content of the files
* read more about [the scripts and create-react-app](https://github.com/facebookincubator/create-react-app)

## Introduction to JSX

Now you will get to know JSX. It is the syntax in React. As I mentioned before, *create-react-app* has already scaffolded a boilerplate application. All files come with default implementations. Let's dive into the source code. The only file you will touch in the beginning will be the *src/App.js* file.

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>Welcome to React</h2>
        </div>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
      </div>
    );
  }
}

export default App;
~~~~~~~~

Don't let yourself get confused by the import/export statements and class declaration. These features are already JavaScript ES6. We will revisit those later.

In the file you have an **ES6 class component** with the name App. Basically you can use the <App /> element everywhere in your application to use the component. Once you use it, it will produce an **instance** of your **component**. The component gets instantiated. The **element** it returns is specified in the `render()` class method. Elements are what components are made of. It is useful to understand the differences between component, instance and element.

Pretty soon you will see where the App component is used. Otherwise you wouldn't see the rendered output in the browser, would you? The App component is only the declaration, but not the usage. You would instantiate the component somewhere in your JSX with `<App />`.

The content in the render block looks pretty similar to HTML, but it's JSX. JSX allows you to mix HTML and JavaScript. It's powerful yet confusing when you are used to plain HTML. That's why a good starting point is to use basic HTML in your JSX. Next you can start to embed JavaScript expressions in between by using curly braces.

First let's remove all the clutter.

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <h2>Welcome to React</h2>
      </div>
    );
  }
}

export default App;
~~~~~~~~

Now you only return HTML without JavaScript. Let's make the "Welcome to React" a variable. As a variable it can be used in your JSX.

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
# leanpub-start-insert
    var helloWorld = 'Welcome to React';
# leanpub-end-insert
    return (
      <div className="App">
# leanpub-start-insert
        <h2>{helloWorld}</h2>
# leanpub-end-insert
      </div>
    );
  }
}

export default App;
~~~~~~~~

It should work when you start your application on the command line.

Additionally you might have noticed the `className` attribute. It reflects the standard `class` attribute in HTML. Because of technical reasons JSX had to replace a handful of internal HTML attributes. You can find all of the [supported HTML attributes in the React documentation](https://facebook.github.io/react/docs/dom-elements.html). On your way to learn React you will come across some more JSX attributes.

### Exercises:

* define more variables and render them in your JSX
* read more about [JSX](https://facebook.github.io/react/docs/introducing-jsx.html)
* read more about [React components, elements and instances](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html)

## ES6 const and let

I guess you noticed that we declared the variable `helloWorld` with `var`. JavaScript ES6 comes with two more options to declare your variables: `const` and `let`. In JavaScript ES6 you will rarely find `var` anymore. Let's get some explanation for `const` and `let`.

A variable declared with `const` cannot be re-assigned or re-declared. It can't get mutated (changed, modified). You embrace immutable data structures by using it.

{lang=javascript}
~~~~~~~~
// not allowed
const helloWorld = 'Welcome to React';
helloWorld = 'Bye Bye React';
~~~~~~~~

A variable declared with `let` can get mutated.

{lang=javascript}
~~~~~~~~
// allowed
let helloWorld = 'Welcome to React';
helloWorld = 'Bye Bye React';
~~~~~~~~

However, you have to be careful at one point with `const`. A variable declared with `const` cannot get modified. But when the variable is an array or object, the value it holds can get altered. The value it holds is not immutable.

{lang=javascript}
~~~~~~~~
// allowed
const helloWorld = {
  text: 'Welcome to React'
};
helloWorld.text = 'Bye Bye React';
~~~~~~~~

But when to use each declaration? There are different opinions about the usage. I suggest to use `const` whenever you can. It indicates that you want to keep your data structure immutable even though values in objects and arrays can get altered. Still, immutability is embraced in React and its ecosystem. When you want to modify your variable, you should use `let` over `const`.

Let's use `const` over `var` in the App component.

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
# leanpub-start-insert
    const helloWorld = 'Welcome to React';
# leanpub-end-insert
    return (
      <div className="App">
        <h2>{helloWorld}</h2>
      </div>
    );
  }
}

export default App;
~~~~~~~~

### Exercises:

* read more about ES6 [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
* read more about ES6 [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
* reasearch more about immutable data structures

## ReactDOM.render

Before you continue with the App component, you might want to see where it's used. It is located in your entry point to the React world: the *src/index.js* file.

{lang=javascript}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.css';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
~~~~~~~~

Basically `ReactDOM.render()` uses a DOM node in your HTML to replace it with your JSX. That's how you can easily integrate React in every foreign application. It is not forbidden to use `ReactDOM.render()` multiple times across your application. You can use it at multiple places in your application to bootstrap simple JSX syntax, a React component, multiple React components or a whole application.

ReactDOM.render expects two arguments.

The first argument is JSX that gets rendered. The second argument specifies the place where the React application hooks into your HTML. It expects an element with an `id='root'`. You can open your *public/index.html* file to find the id.

In the implementation `ReactDOM.render()` already takes your App component. However, it would be fine to pass simpler JSX as long as it is JSX. It doesn't have to be an instantiation of a component.

{lang=javascript}
~~~~~~~~
ReactDOM.render(
  <h1>Hello React World</h1>,
  document.getElementById('root')
);
~~~~~~~~

### Exercises:

* read more about [rendering elements in React](https://facebook.github.io/react/docs/rendering-elements.html)

## Complex JavaScript in JSX

Let's get back to your App component. So far you rendered some primitive variables in your JSX. Now you will start to render a list of items. The list will be artificial data in the beginning, but later you will fetch the data from an external API. That will be far more exciting.

First you have to define the list of items.

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

# leanpub-start-insert
const list = [
  {
    title: 'React',
    url: 'https://facebook.github.io/react/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  },
  {
    title: 'Redux',
    url: 'https://github.com/reactjs/redux',
    author: 'Dan Abramov, Andrew Clark',
    num_comments: 2,
    points: 5,
    objectID: 1,
  },
];
# leanpub-end-insert

class App extends Component {
  ...
}
~~~~~~~~

The artifical data will reflect the data we will fetch later on from the API. An item in the list has a title, an url and a author. Additionally it comes with an identifier, points (which indicate how popular an article is) and a count of comments.

Now you can use the built-in JavaScript map functionality in your JSX. It enables you to iterate over your list of items to display them. As mentioned, you will use curly braces to encapsulate the JavaScript expression in your JSX.

{lang=javascript}
~~~~~~~~
class App extends Component {

  render() {
    return (
      <div className="App">
# leanpub-start-insert
        { list.map(function(item) {
          return <div>{item.title}</div>;
        })}
# leanpub-end-insert
      </div>
    );
  }
}

export default App;
~~~~~~~~

Only the `title` will be displayed for each item. But let's display most of the list item properties.

{lang=javascript}
~~~~~~~~
class App extends Component {

  render() {
    return (
      <div className="App">
# leanpub-start-insert
        { list.map(function(item) {
          return (
            <div>
              <span>
                <a href={item.url}>{item.title}</a>
              </span>
              <span>{item.author}</span>
              <span>{item.num_comments}</span>
              <span>{item.points}</span>
            </div>
          );
        })}
# leanpub-end-insert
      </div>
    );
  }
}

export default App;
~~~~~~~~

You can see how the map function is simply inlined in your JSX. Each item property is displayed in a `<span>` tag. Moreover the url property of the item is used in the `href` attribute of the anchor tag.

React will do all the work for you and display each item. But you should add one helper for React to embrace its full potential. You have to assign a key attribute to each list element. Only that way React is able to identify added, changed and removed items when the list changes.

{lang=javascript}
~~~~~~~~
{ list.map(function(item) {
  return (
# leanpub-start-insert
    <div key={item.objectID}>
# leanpub-end-insert
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
      <span>{item.num_comments}</span>
      <span>{item.points}</span>
    </div>
  );
})}
~~~~~~~~

You should make sure that the key attribute is a stable identifier. Don't make the mistake of using the array index, which isn't stable at all. For instance, when the list changes its order. React will have a hard time identifying the items properly.

{lang=javascript}
~~~~~~~~
// don't do this
{ list.map(function(item, key) {
  return (
    <div key={key}>
      ...
    </div>
  );
})}
~~~~~~~~

You are displaying both list items now. You can start your app, open your browser and see both items of the list displayed.

### Exercises:

* read more about [React lists and keys](https://facebook.github.io/react/docs/lists-and-keys.html)
* recap the [standard built-in Array functionalities in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
* use more JavaScript expressions on your own in JSX

## ES6 Arrow Functions

JavaScript ES6 introduced arrow functions. An arrow function expression is shorter than a function expression.

{lang=javascript}
~~~~~~~~
// function expression
function () { ... }

// arrow function expression
() => { ... }
~~~~~~~~

But you have to be aware of its functionalities. One of them is a different behavior with the `this` object. A function expression always defines its own `this` object. Arrow function expressions still have the `this` object of the enclosing context. Don't get confused when using `this` in an arrow function.

There is another valuable fact about arrow functions regarding the parenthesis. You can remove the parenthesis when the function gets only one argument, but have to keep them when it gets multiple arguments.

{lang=javascript}
~~~~~~~~
// allowed
item => { ... }

// allowed
(item) => { ... }

// not allowed
item, key => { ... }

// allowed
(item, key) => { ... }
~~~~~~~~

However let's have a look at the map function. You can write it more concisely with an ES6 arrow function.

{lang=javascript}
~~~~~~~~
# leanpub-start-insert
{ list.map(item => {
# leanpub-end-insert
  return (
    <div key={item.objectID}>
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
      <span>{item.num_comments}</span>
      <span>{item.points}</span>
    </div>
  );
})}
~~~~~~~~

Additionally you can remove the block body. In a concise body an implicit return is attached thus you can remove the return statement. That will happen more often in the book, so be sure to understand the difference between a block body and a concise body.

{lang=javascript}
~~~~~~~~
# leanpub-start-insert
{ list.map(item =>
# leanpub-end-insert
  <div key={item.objectID}>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
  </div>
# leanpub-start-insert
)}
# leanpub-end-insert
~~~~~~~~

Your JSX looks more concise and readable now. It omits the function and return statements.

### Exercises:

* read more about [ES6 arrow functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

{pagebreak}

Your *src/App.js* should look like the following by now:

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

const list = [
  {
    title: 'React',
    url: 'https://facebook.github.io/react/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  },
  {
    title: 'Redux',
    url: 'https://github.com/reactjs/redux',
    author: 'Dan Abramov, Andrew Clark',
    num_comments: 2,
    points: 5,
    objectID: 1,
  },
];

class App extends Component {
  render() {
    return (
      <div className="App">
        { list.map(item =>
          <div key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
          </div>
        )}
      </div>
    );
  }
}

export default App;
~~~~~~~~

You have learned to bootstrap your own React application! Let's recap the last chapters:

* React
  * create-react-app bootstraps a React application
  * JSX mixes up HTML and JavaScript to define React components
  * components, instances and elements are different things
  * ReactDOM.render() is an entry point for a React application
  * built-in JavaScript functionalities can be used in JSX
    * for instance, map can be used to render a list of items
* ES6
  * variable declarations with const and let
  * arrow functions can be used to shorten your function declarations

It makes sense to take a break at this point. Internalize the learnings and apply them on your own. You can experiment with the source code you have written so far.
