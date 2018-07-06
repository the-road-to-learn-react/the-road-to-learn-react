# Introduction to React

The chapter gives you an introduction to React. You may ask yourself: Why should I learn React in the first place? This chapter seeks to answer that question. Afterward, you will dive into the ecosystem by bootstrapping your first React application from scratch with zero-configuration. Along the way, you will get an introduction to JSX and ReactDOM. So be prepared for your first React components.

## Hi, my name is React.

**Why should you bother to learn React?** In recent years, single page applications ([SPA](https://en.wikipedia.org/wiki/Single-page_application)) have become popular. Frameworks like Angular, Ember, and Backbone helped JavaScript developers build modern web applications beyond the usage of vanilla JavaScript and jQuery. This list of popular solutions is not exhaustive, and there is a wide range of SPA frameworks. When you consider their release dates, most of them are among the first generation of SPAs: Angular 2010, Backbone 2010 and Ember 2011.

React was initially released in 2013 by Facebook. React is not an SPA framework, but a view library. It is the V in the [MVC](https://en.wikipedia.org/wiki/Model–view–controller) (model view controller). It only enables you to render components as viewable elements in a browser. Yet, the whole ecosystem around React makes it possible to build single page applications.

But why should you consider using React over the first generation of SPA frameworks? While the first generation of frameworks tried to solve many things at once, React is only used to build your view layer. It is a library and not a framework. The idea behind React is that your view is a hierarchy of composable components.

In React you can keep the focus on your view layer before you introduce more aspects to your application. Additional aspects are building blocks for your SPA. These building blocks are essential to build a mature application, and they come with two advantages.

Firstly, you can learn the building blocks one at a time without having to understand them altogether. In contrast, an SPA framework gives you every building block from the start. This book focuses on React as the first building block. More building blocks will eventually follow.

Secondly, all building blocks are interchangeable, which makes the ecosystem around React highly innovative. Multiple solutions can compete with each other, and you can choose the most appealing solution for your use case.

The first generation of SPA frameworks arrived at an enterprise level; these frameworks are more rigid. React stays innovative and has been adopted by many tech thought leader companies like [Airbnb, Netflix and, of course, Facebook](https://github.com/facebook/react/wiki/Sites-Using-React). These companies invest in the future of React and are content with React and its ecosystem.

React is one of the best choices for building modern web applications nowadays. It only delivers the view layer, [but the React ecosystem is an entirely flexible and interchangeable framework](https://www.robinwieruch.de/essential-react-libraries-framework/). React has a slim API, an amazing ecosystem, and a great community. You can read about my experiences on [why I moved from Angular to React](https://www.robinwieruch.de/reasons-why-i-moved-from-angular-to-react/). I highly recommend understanding why you would choose React over another framework or library. After all, everyone is keen to experience where React will lead us in the coming years.

### Exercises

* read about [why I moved from Angular to React](https://www.robinwieruch.de/reasons-why-i-moved-from-angular-to-react/)
* read about [React's flexible ecosystem](https://www.robinwieruch.de/essential-react-libraries-framework/)
* read about [how to learn a framework](https://www.robinwieruch.de/how-to-learn-framework/)

## Requirements

What are the requirements to read this book? First of all, you should be familiar with the basics of web development. You should know how to use HTML, CSS, and JavaScript. Perhaps it makes sense to know the term [API](https://www.robinwieruch.de/what-is-an-api-javascript/) too, because you will use APIs in this book. In addition, I encourage you to join the official [Slack Group](https://slack-the-road-to-learn-react.wieruch.com/) for the book to get help or to help others.

### Editor and Terminal

What about the development environment? You will need a running editor or IDE and terminal (command line tool). You can [follow my setup guide](https://www.robinwieruch.de/developer-setup/). It is adjusted for MacOS users, but you can find a Windows setup guide for React too. In general, there are many articles out there that will show you how to set up a web development environment in a more elaborate way for your OS.

Optionally, you can use git to keep your projects and progress in repositories on GitHub while conducting the exercises in this book. There is a [short guide](https://www.robinwieruch.de/git-essential-commands/) on how to use these tools. But once again, it is not mandatory for this book and can be overwhelming when learning everything from scratch. You can skip it if you are a newcomer in web development and want to focus on the essential parts taught in this book.

### Node and NPM

Last but not least, you will need an installation of [node and npm](https://nodejs.org/en/). Both are used to manage libraries you will need along the way. In this book, you will install external node packages via npm (node package manager). These node packages can be libraries or whole frameworks.

You can verify your versions of node and npm on the command line. If you don't get any output in the terminal, you need to install node and npm first. These are my versions at the time of writing this book:

{title="Command Line",lang="text"}
~~~~~~~~
node --version
*v8.9.4
npm --version
*v5.6.0
~~~~~~~~

## node and npm

This section of the chapter gives you a little crash course in node and npm. It is not exhaustive, but it will cover all of the necessary tools. If you are familiar with both of them, you can skip this section.

The **node package manager** (npm) allows you to install external **node packages** from the command line. These packages can be a set of utility functions, libraries, or whole frameworks, and they are the dependencies of your application. You can either install these packages to your global node package folder or to your local project folder.

Global node packages are accessible from everywhere in the terminal, and you only have to install them to your global directory once. You can install a global package by typing in your terminal:

{title="Command Line",lang="text"}
~~~~~~~~
npm install -g <package>
~~~~~~~~

The `-g` flag tells npm to install the package globally. Local packages are used in your application. For instance, React as a library will be a local package which can be required in your application for usage. You can install it via the terminal by typing:

{title="Command Line",lang="text"}
~~~~~~~~
npm install <package>
~~~~~~~~

In the case of React it would be:

{title="Command Line",lang="text"}
~~~~~~~~
npm install react
~~~~~~~~

The installed package will automatically appear in a folder called *node_modules/* and will be listed in the *package.json* file next to your other dependencies.

But how do you initialize the *node_modules/* folder and the *package.json* file for your project in the first place? There is a npm command to initialize a npm project and thus a *package.json* file. Once you have that file, you can install new local packages via npm.

{title="Command Line",lang="text"}
~~~~~~~~
npm init -y
~~~~~~~~

The `-y` flag is a shortcut to initialize all the defaults in your *package.json*. If you don't use the flag, you have to decide how to configure the file. After initializing your npm project, you are ready to install new packages via `npm install <package>`.

One more word about the *package.json*. The file enables you to share your project with other developers without sharing all the node packages. The file has all the references of node packages used in your project. These packages are called dependencies. Everyone can copy your project without the dependencies. The dependencies are references in the *package.json*. Someone who copies your project can simply install all packages by using `npm install` on the command line. The `npm install` script takes all the dependencies listed in the *package.json* file and installs them in the *node_modules/* folder.

I want to cover one more npm command:

{title="Command Line",lang="text"}
~~~~~~~~
npm install --save-dev <package>
~~~~~~~~

The `--save-dev` flag indicates that the node package is only used in the development environment. It will not be used in production when you deploy your application on a server. What kind of node package would use this flag? Imagine you want to test your application with the help of a node package. You need to install that package via npm, but you want to exclude it from your production environment. Testing should only happen during the development process, but not when your application is already running in production. At that point, you don't want to test your application anymore. It should be tested already and working out-of-the-box for your users. This is only one use case where you would want to use the `--save-dev` flag.

You will encounter more npm commands along the way, but these will be sufficient for now.

There is one more important thing to be mentioned. Many people opt to use another package manager to work with node packages in their applications. **Yarn** is a dependency manager that works in a very similar way to **npm**. Is has its own list of commands to perform the same tasks, but you still have access to the npm registry. Yarn was born to solve some issues that npm couldn't. But today, both tools are evolving really fast and you can choose to use whichever you want.

### Exercises:

* setup a throw away npm project
  * create a new folder with `mkdir <folder_name>`
  * navigate into the folder with `cd <folder_name>`
  * execute `npm init -y` or `npm init`
  * install a local package like React with `npm install react`
  * have a look into the *package.json* file and the *node_modules/* folder
  * find out on your own how to uninstall the *react* node package again
* read more about [npm](https://docs.npmjs.com/)
* read more about [yarn](https://yarnpkg.com/en/docs/) package manager

## Installation

There are multiple approaches to getting started with a React application.

The first one is using a CDN, which may sound more complicated than it is. A CDN is a [content delivery network](https://en.wikipedia.org/wiki/Content_delivery_network). Several companies have CDNs that host files publicly for people to consume. These files can be libraries like React, since the bundled React library is just a *react.js* JavaScript file. It can be hosted somewhere, and you can require it in your application.

How do you use a CDN to get started in React? You can inline the `<script>` tag in your HTML that points to a CDN url. To get started in React, you need two files (libraries): *react* and *react-dom*.

{title="Code Playground",lang="javascript"}
~~~~~~~~
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
~~~~~~~~

But why should you use a CDN when you have npm to install node packages such as React?

When your application has a *package.json* file, you can install *react* and *react-dom* from the command line. However, the folder must be initialized as a npm project by using `npm init -y` with a *package.json* file. You can install multiple node packages in one line with npm.

{title="Command Line",lang="text"}
~~~~~~~~
npm install react react-dom
~~~~~~~~

This approach is often used to add React to an existing application that is managed with npm.

Unfortunately, that is not everything. You would have to deal with [Babel](http://babeljs.io/) to make your application aware of JSX (the React syntax) and JavaScript ES6. Babel transpiles your code so that browsers can interpret JavaScript ES6 and JSX, because not all browsers are capable of interpreting the syntax. The setup includes a lot of configuration and tooling, and it can be overwhelming for React newcomers to bother with all the configuration.

Because of this, Facebook introduced *create-react-app* as a zero-configuration React solution. The next chapter will show you how to setup your application by using this bootstrapping tool.

### Exercises:

* read more about [React installations](https://reactjs.org/docs/try-react.html)

## Zero-Configuration Setup

In the Road to learn React, you will use [create-react-app](https://github.com/facebookincubator/create-react-app) to bootstrap your application. It's an opinionated yet zero-configuration starter kit for React introduced by Facebook in 2016, and is [recommended for beginners by 96% of React users](https://twitter.com/dan_abramov/status/806985854099062785). In *create-react-app* the tooling and configuration evolve in the background while the focus is on the application implementation.

To get started, you must install the package to your global node packages. Afterwards, you always have it available on the command line to bootstrap new React applications.

{title="Command Line",lang="text"}
~~~~~~~~
npm install -g create-react-app
~~~~~~~~

You can check the version of *create-react-app* to verify a successful installation on your command line:

{title="Command Line",lang="text"}
~~~~~~~~
create-react-app --version
*v1.5.1
~~~~~~~~

Now you can bootstrap your first React application. We will call it *hackernews*, but you can choose a different name. The bootstrapping takes a couple of seconds. Afterward, simply navigate into the folder:

{title="Command Line",lang="text"}
~~~~~~~~
create-react-app hackernews
cd hackernews
~~~~~~~~

Now you can open the application in your editor. The following folder structure, or a variation of it depending on the *create-react-app* version, should be presented to you:

{title="Folder Structure",lang="text"}
~~~~~~~~
hackernews/
  README.md
  node_modules/
  package.json
  .gitignore
  public/
    favicon.ico
    index.html
    manifest.json
  src/
    App.css
    App.js
    App.test.js
    index.css
    index.js
    logo.svg
    registerServiceWorker.js
~~~~~~~~

Here is a short breakdown of the folders and files. It is okay if you don't understand all of them in the beginning.

* **README.md:** The .md extension indicates that the file is a markdown file. Markdown is used as a lightweight markup language with plain text formatting syntax. Many source code projects come with a *README.md* file to give you initial instructions about the project. When pushing your project to a platform such as GitHub, the *README.md* file will show its content prominently when you access the repository. Because you used *create-react-app*, your *README.md* should be the same as shown in the official [create-react-app GitHub repository](https://github.com/facebookincubator/create-react-app).

* **node_modules/:** This folder has all the node packages that have been installed via npm. Since you used *create-react-app*, there should already be a couple of node modules installed for you. You will usually never touch this folder, because node packages are installed and uninstalled with npm from the command line instead.

* **package.json:** This file shows you a list of node package dependencies and other project configurations.

* **.gitignore:** This file indicates all files and folders that shouldn't be added to your git repository when using git; such files and folders should only be located in your local project. The *node_modules/* folder is one such use case. It is sufficient to share the *package.json* file with your peers to enable them to install all dependencies on their own without sharing the whole dependency folder.

* **public/:** This folder holds development root files, such as *public/index.html*. This index is the one displayed on localhost:3000 when developing your app. The boilerplate takes care of relating this index with all the scripts in *src/*.

* **build/** This folder will be created when you build the project for production, and it holds all of the production files. Upon building your project for production, all of your written code in the *src/* and *public/* folders are bundled into a couple of files and placed in the build folder.

* **manifest.json** and **registerServiceWorker.js:** Don't worry about what these files do at this stage, we won't be needing them in this project.

You don't need to touch the mentioned files and folders. In the beginning, everything you need is located in the *src/* folder. The main focus lies on the *src/App.js* file which is used to implement React components. It will be used to implement your application, but later you might want to split up your components into multiple files, where each file maintains one or a few components on its own.

Additionally, you will find a *src/App.test.js* file for your tests and a *src/index.js* as the entry point to the React world. You will get to know both files in a later chapter. In addition, there is a *src/index.css* and a *src/App.css* file to style your general application and components. They come with the default style when you open them.

The *create-react-app* application is a npm project. You can use npm to install and uninstall node packages to your project. Additionally, it comes with the following npm scripts for your command line:

{title="Command Line",lang="text"}
~~~~~~~~
// Runs the application in http://localhost:3000
npm start

// Runs the tests
npm test

// Builds the application for production
npm run build
~~~~~~~~

The scripts are defined in your *package.json*. Your boilerplate React application is bootstrapped now. The following exercises will allow you to finally run your bootstrapped application in a browser.

### Exercises:

* `npm start` your application and visit the application in your browser (you can exit the command by pressing Control + C )
* run the interactive `npm test` script
* run the `npm run build` script and verify that a *build/* folder was added to your project (you can remove it again afterward; note that the build folder can be used later on to [deploy your application](https://www.robinwieruch.de/deploy-applications-digital-ocean/))
* make yourself familiar with the folder structure
* make yourself familiar with the content of the files
* read more about [the npm scripts and create-react-app](https://github.com/facebookincubator/create-react-app)

## Introduction to JSX

Now you will get to know JSX, which is the syntax in React. As mentioned before, *create-react-app* has already bootstrapped a boilerplate application for you. All files come with default implementations. In the beginning, the only file you will touch is the *src/App.js* file.

{title="src/App.js",lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
      </div>
    );
  }
}

export default App;
~~~~~~~~

Don't let yourself get confused by the import/export statements and class declaration. These are features of JavaScript ES6, and we will revisit them in a later chapter.

In the file you have a **React ES6 class component** with the name App. This is a component declaration. Basically, after you have declared a component, you can use it as an element anywhere in your application. It will produce an **instance** of your **component** or, in other words, the component gets instantiated.

The **element** returned is specified in the `render()` method. Elements are what components are made of. It is important to understand the differences between a component, an instance of a component, and an element.

Pretty soon, you will see where the App component is instantiated. Otherwise, you wouldn't see the rendered output in the browser. The App component is only the declaration, but not the usage. You can instantiate the component anywhere in your JSX with `<App />`.

The content in the render block looks pretty similar to HTML, but it is actually JSX. JSX allows you to mix HTML and JavaScript. It is powerful, but can be confusing when you are used to separating your HTML and JavaScript. It is a good idea to start out by using basic HTML in your JSX. Therefore, open the `App.js` file and remove all the unnecessary HTML code as illustrated below.

{title="src/App.js",lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <h2>Welcome to the Road to learn React</h2>
      </div>
    );
  }
}

export default App;
~~~~~~~~

Now, you only return HTML in your `render()` method without any JavaScript. Let's define the "Welcome to the Road to learn React" as a variable. A variable can be used in your JSX by using curly braces.

{title="src/App.js",lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
# leanpub-start-insert
    var helloWorld = 'Welcome to the Road to learn React';
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

It should work when you start your application on the command line with `npm start` again.

Additionally, you might have noticed the `className` attribute. It reflects the standard `class` attribute in HTML. Due to technical reasons, JSX had to replace a handful of internal HTML attributes. You can find all of the [supported HTML attributes in the React documentation](https://reactjs.org/docs/dom-elements.html#all-supported-html-attributes), which all follow the camelCase convention. On your way to learn React, you will come across some more JSX specific attributes.

### Exercises:

* define more variables and render them in your JSX
  * use a complex object to represent a user with a first name and last name
  * render the user properties in your JSX
* read more about [JSX](https://reactjs.org/docs/introducing-jsx.html)
* read more about [React components, elements and instances](https://facebook.github.io/react/blog/2015/12/18/react-components-elements-and-instances.html)

## ES6 const and let

You probably noticed that we declared the variable `helloWorld` with a `var` statement. JavaScript ES6 comes with two more options to declare your variables: `const` and `let`. In JavaScript ES6, you will rarely find `var` anymore.

A variable declared with `const` cannot be re-assigned or re-declared, and cannot be changed or modified (it is immutable). Once the data structure is defined, you cannot change it.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// not allowed
const helloWorld = 'Welcome to the Road to learn React';
helloWorld = 'Bye Bye React';
~~~~~~~~

A variable declared with `let` can be modified.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// allowed
let helloWorld = 'Welcome to the Road to learn React';
helloWorld = 'Bye Bye React';
~~~~~~~~

You would declare variables with `let` if you need to re-assign the variable later on.

However, you have to be careful with `const`. A variable declared with `const` cannot be modified. However, when the variable is an array or object, the value it holds can get updated. The value it holds is not immutable.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// allowed
const helloWorld = {
  text: 'Welcome to the Road to learn React'
};
helloWorld.text = 'Bye Bye React';
~~~~~~~~

When do you use each declaration? There are different opinions about the usage. I suggest using `const` whenever you can. It indicates that you want to keep your data structure immutable even though values in objects and arrays can be modified. If you want to modify your variable, you can use `let`.

Immutability is embraced in React and its ecosystem. That's why `const` should be your default choice when you define a variable. Still, in complex objects the values within can be modified. Be careful about this behavior.

In your application, you should use `const` over `var`.

{title="src/App.js",lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import './App.css';

class App extends Component {
  render() {
# leanpub-start-insert
    const helloWorld = 'Welcome to the Road to learn React';
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

* read more about [ES6 const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
* read more about [ES6 let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
* research more about immutable data structures
  * why do they make sense in programming in general
  * why are they used in React and its ecosystem

## ReactDOM

Before we continue with the App component, you might want to see where it is used. It is located in your entry point to the React world: the *src/index.js* file.

{title="src/index.js",lang=javascript}
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

Basically, `ReactDOM.render()` uses a DOM node in your HTML to replace it with your JSX. That's how you can easily integrate React in any foreign application. It is not forbidden to use `ReactDOM.render()` multiple times across your application. You can use it at multiple places to bootstrap simple JSX syntax, a React component, multiple React components, or a whole application. In a plain React application, you would only use it once to bootstrap your whole component tree.

`ReactDOM.render()` expects two arguments. The first argument is JSX that gets rendered. The second argument specifies the place where the React application hooks into your HTML. It expects an element with an `id='root'`. You can open your *public/index.html* file to find the id attribute.

In the implementation, `ReactDOM.render()` already takes your App component. However, it would be fine to pass simpler JSX as long as it is JSX. It doesn't have to be an instantiation of a component.

{title="Code Playground",lang=javascript}
~~~~~~~~
ReactDOM.render(
  <h1>Hello React World</h1>,
  document.getElementById('root')
);
~~~~~~~~

### Exercises:

* open the *public/index.html* to see where the React applications hook into your HTML
* read more about [rendering elements in React](https://reactjs.org/docs/rendering-elements.html)

## Hot Module Replacement

Hot Module Replacement is one thing that you can do in the *src/index.js* file to improve your experience as a developer, but it is optional and may overwhelm you as a beginner learning React.

By default, *create-react-app* will cause the browser to automatically refresh the page when your source code is modified. Try it by changing the `helloWorld` variable in your *src/App.js* file, which should cause the browser to refresh the page. However, there is a better way of handling source code changes during development.

Hot Module Replacement (HMR) is a tool to reload your application in the browser, so that the browser doesn't perform a page refresh. You can easily activate it in *create-react-app*. In your *src/index.js*, your entry point to React, add the following configuration.

{title="src/index.js",lang=javascript}
~~~~~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './index.css';

ReactDOM.render(
  <App />,
  document.getElementById('root')
);

# leanpub-start-insert
if (module.hot) {
  module.hot.accept();
}
# leanpub-end-insert
~~~~~~~~

That's it. Again, change the `helloWorld` variable in your *src/App.js* file. The browser shouldn't perform a page refresh, but the application will reload and show the correct output. HMR comes with multiple advantages:

Imagine you are debugging your code with `console.log()` statements. These statements will stay in your developer console, even though you changed your code, because the browser doesn't refresh the page anymore. This can be convenient for many debugging purposes.

In a growing application, a page refresh delays your productivity, because you have to wait for the page to load. A page reload can take several seconds in a large application. HMR removes this disadvantage.

Finally, The largest benefit of HMR is that you can keep the application state after the application reloads. Imagine you have a dialog or wizard in your application with multiple steps, and you are at step 3. Without HMR you would change the source code and your browser would refresh the page. You would have to open the dialog again and navigate from step 1 to step 3. With HMR your dialog stays open at step 3. It keeps the application state even though the source code changed. The application itself reloads, but not the page.

### Exercises:

* change your *src/App.js* source code a few times to see HMR in action
* watch the first 10 minutes of [Live React: Hot Reloading with Time Travel](https://www.youtube.com/watch?v=xsSnOQynTHs) by Dan Abramov

## Complex JavaScript in JSX

Let's get back to your App component. So far you have rendered some primitive variables in your JSX. Now you will start to render a list of items. The list will be sample data in the beginning, but later you will fetch the data from an external [API](https://www.robinwieruch.de/what-is-an-api-javascript/), which will be far more exciting.

First you have to define the list of items.

{title="src/App.js",lang=javascript}
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

The sample data will represent data we will fetch from the API later on. An item in the list has a title, a url, and an author. Additionally, it comes with an identifier, points (which indicate how popular an article is), and a count of comments.

Now you can use the built-in JavaScript `map` functionality in your JSX. It enables you to iterate over your list of items to display them. Again, you will use curly braces to encapsulate the JavaScript expression in your JSX.

{title="src/App.js",lang=javascript}
~~~~~~~~
class App extends Component {
  render() {
    return (
      <div className="App">
# leanpub-start-insert
        {list.map(function(item) {
          return <div>{item.title}</div>;
        })}
# leanpub-end-insert
      </div>
    );
  }
}

export default App;
~~~~~~~~

Using JavaScript alongside HTML in JSX is very powerful. You might have used `map` to convert one list of items to another list of items. This time you can use `map` to convert a list of items to HTML elements.

So far, only the `title` will be displayed for each item. Let's display some more of the item properties.

{title="src/App.js",lang=javascript}
~~~~~~~~
class App extends Component {
  render() {
    return (
      <div className="App">
# leanpub-start-insert
        {list.map(function(item) {
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

You can see how the map function is simply inlined in your JSX. Each item property is displayed in a `<span>` tag. Moreover, the url property of the item is used in the `href` attribute of the anchor tag.

React will do all the work for you and display each item, but you should add one helper for React to embrace its full potential and improve its performance. You have to assign a key attribute to each list element. That way React is able to identify added, changed and removed items when the list changes. The sample list items come with an identifier already.

{title="src/App.js",lang=javascript}
~~~~~~~~
{list.map(function(item) {
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

You should make sure that the key attribute is a stable identifier. Don't make the mistake of using the index of the item in the array, because the array index is not stable. For instance, if the list changes its order, React will not be able to identify the items properly.

{title="src/App.js",lang=javascript}
~~~~~~~~
// don't do this
{list.map(function(item, key) {
  return (
    <div key={key}>
      ...
    </div>
  );
})}
~~~~~~~~

You are displaying both list items now. You can start your app, open your browser, and see both items of the list displayed.

### Exercises:

* read more about [React lists and keys](https://reactjs.org/docs/lists-and-keys.html)
* recap the [standard built-in array functionalities in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
* use more JavaScript expressions on your own in JSX

## ES6 Arrow Functions

JavaScript ES6 introduced arrow functions. An arrow function expression is shorter than a function expression.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// function expression
function () { ... }

// arrow function expression
() => { ... }
~~~~~~~~

You have to be aware of the functionalities of arrow functions. One of the functionalities is different behavior with the `this` object. A function expression always defines its own `this` object. Arrow function expressions still have the `this` object of the enclosing context.

There is another valuable fact about arrow functions regarding the parentheses. You can remove the parentheses if the function has only one argument, but you have to keep the parentheses if it gets multiple arguments.

{title="Code Playground",lang="javascript"}
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

Let's take a look at the `map` function. You can write it more concisely with an ES6 arrow function.

{title="src/App.js",lang=javascript}
~~~~~~~~
# leanpub-start-insert
{list.map(item => {
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

Additionally, you can remove the *block body*, meaning the curly braces, of the ES6 arrow function. In a *concise body*, an implicit return is attached. Thus you can remove the return statement. This will happen more often in this book, so be sure to understand the difference between a block body and a concise body when using arrow functions.

{title="src/App.js",lang=javascript}
~~~~~~~~
# leanpub-start-insert
{list.map(item =>
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

Your JSX looks more concise and readable now. It omits the function statement, the curly braces, and the return statement. Instead a developer can focus on the implementation details.

### Exercises:

* read more about [ES6 arrow functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

## ES6 Classes

JavaScript ES6 introduced classes. Classes are commonly used in object-oriented programming languages. JavaScript was and is very flexible in its programming paradigms. You can do functional programming and object-oriented programming side-by-side for their particular use cases.

Even though React embraces functional programming, e.g. immutable data structures, classes are used to declare components. They are called ES6 class components. React mixes the good parts of both programming paradigms.

Let's consider the following Developer class to examine a JavaScript ES6 class without thinking about a component.

{title="Code Playground",lang="javascript"}
~~~~~~~~
class Developer {
  constructor(firstname, lastname) {
    this.firstname = firstname;
    this.lastname = lastname;
  }

  getName() {
    return this.firstname + ' ' + this.lastname;
  }
}
~~~~~~~~

A class has a constructor to make it instantiable. The constructor can take arguments to assign it to the class instance. Additionally, a class can define functions. Because the function is associated with a class, it is called a method. It is often referenced as a class method.

The Developer class is only the class declaration. You can create multiple instances of a class by invoking it. It is similar to the ES6 class component, which has a declaration, but you have to use it somewhere else to instantiate it.

Let's see how you can instantiate the class and how you can use its methods.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const robin = new Developer('Robin', 'Wieruch');
console.log(robin.getName());
// output: Robin Wieruch
~~~~~~~~

React uses JavaScript ES6 classes for ES6 class components. You already used one ES6 class component.

{title="src/App.js",lang=javascript}
~~~~~~~~
import React, { Component } from 'react';

...

class App extends Component {
  render() {
    ...
  }
}
~~~~~~~~

The App class extends from `Component`. Basically, when you declare the App component it extends from another component. What does extend mean? In object-oriented programming, there is the principle of inheritance, which means that functionalities can be passed from one class to another class.

The App class extends functionality from the Component class. To be more specific, it inherits functionalities from the Component class. The Component class is used to extend a basic ES6 class to a ES6 component class. It has all the functionalities that a component in React needs to have. The render method is one of these functionalities that you have already used. You will learn about other component class methods later on.

The `Component` class encapsulates all the implementation details of a React component. It enables developers to use classes as components in React.

The methods exposed by a React `Component` are its public interface. One of these methods must be overridden, while the others don't need to be overridden. You will learn about the latter ones when the book arrives at lifecycle methods in a later chapter. The `render()` method has to be overridden, because it defines the output of a React `Component`. Therefore, it must be defined.

Now you know the basics around JavaScript ES6 classes and how they are used in React to extend them to components. You will learn more about the Component methods when the book describes React lifecycle methods.

### Exercises:

* read more about [ES6 classes](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes)

{pagebreak}

You have learned to bootstrap your own React application! Let's recap the last chapters:

* React
  * create-react-app bootstraps a React application
  * JSX mixes up HTML and JavaScript to define the output of React components in their render methods
  * components, instances, and elements are different things in React
  * `ReactDOM.render()` is an entry point for a React application to hook React into the DOM
  * built-in JavaScript functionalities can be used in JSX
    * map can be used to render a list of items as HTML elements
* ES6
  * variable declarations with `const` and `let` can be used for specific use cases
    * use const over let in React applications
  * arrow functions can be used to keep your functions concise
  * classes are used to define components in React by extending them

It makes sense to take a break at this point. Internalize the learnings and apply them on your own. You can experiment with the source code you have written so far. You can find the source code in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.1).