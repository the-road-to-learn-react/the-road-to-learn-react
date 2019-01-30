# Basics in React

This chapter will guide you through the basics of React. It covers state and interactions in components as we move past static components. We will also cover the different ways to declare a component, and how to keep components composable and reusable.

## Local Component State

Local component state, also known as internal component state, allows you to save, modify, and delete properties stored in your component. The ES6 class component then uses a constructor to initialize local component state. The constructor is called only once, when the component initializes:

Let's introduce a class constructor.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

# leanpub-start-insert
  constructor(props) {
    super(props);
  }
# leanpub-end-insert

  ...

}
~~~~~~~~

The App component is a subclass of `Component`, so the `extends Component` is in the App component declaration.

It is mandatory to call `super(props);`. It sets `this.props` in your constructor in case you want to access them there. They would be `undefined` when accessing `this.props` in your constructor otherwise. In this case, the initial state of the component should be the sample list of items:


{title="src/App.js",lang="javascript"}
~~~~~~~~
const list = [
  {
    title: 'React',
    url: 'https://reactjs.org/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  },
  ...
];

class App extends Component {

  constructor(props) {
    super(props);

# leanpub-start-insert
    this.state = {
      list: list,
    };
# leanpub-end-insert
  }

  ...

}
~~~~~~~~

The state is bound to the class using the `this` object, so you can access the local state of the whole component. For instance, it can be used in the `render()` method. Previously you have mapped a static list of items in your `render()` method that was defined outside of your component. Now you are about to use the list from your local state in your component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
# leanpub-start-insert
        {this.state.list.map(item =>
# leanpub-end-insert
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
~~~~~~~~

The list is part of the component now, in the local component state. You could add, change, or remove items from your list. Every time you change your component state, the `render()` method of your component will run again. That's how you can change your local component state and see the component re-render the correct data from the local state.

Be careful not to mutate the state directly. Instead, you should use a method called `setState()` to modify your states. We will cover these concepts in more depth next chapter.

### Exercises:

* Experiment with the local state
  * Define more initial state in the constructor
  * Use and access the state in your `render()` method
* Read about [the ES6 class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)

## ES6 Object Initializer

In JavaScript ES6, you can use a shorthand property syntax to initialize your objects more concisely, like following object initialization:

{title="Code Playground",lang="javascript"}
~~~~~~~~
const name = 'Robin';

const user = {
  name: name,
};
~~~~~~~~

When the property name in your object is the same as your variable name, you can do the following:

{title="Code Playground",lang="javascript"}
~~~~~~~~
const name = 'Robin';

const user = {
  name,
};
~~~~~~~~

In your application, you can do the same. The list variable name and the state property name share the same name.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES5
this.state = {
  list: list,
};

// ES6
this.state = {
  list,
};
~~~~~~~~

Shorthand method names are also useful. In JavaScript ES6, you can initialize methods in an object more concisely:

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES5
var userService = {
  getUserName: function (user) {
    return user.firstname + ' ' + user.lastname;
  },
};

// ES6
const userService = {
  getUserName(user) {
    return user.firstname + ' ' + user.lastname;
  },
};
~~~~~~~~

Finally, you are allowed to use computed property names in JavaScript ES6:

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES5
var user = {
  name: 'Robin',
};

// ES6
const key = 'name';
const user = {
  [key]: 'Robin',
};
~~~~~~~~

Later, you will be able to use computed property names to allocate values by key in an object dynamically, a handy way to generate lookup tables in JavaScript.

### Exercises:

* Experiment with ES6 object initializer
* Read about [ES6 object initializer](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer)

## Unidirectional Data Flow

Now you have a local state in your App component, but haven't manipulated the state yet. The local state is static, so its component is as well. A good way to experience state manipulation is to engage in component interaction.

We will practice this concept by adding a button for each item in the displayed list. The button will read "Dismiss", as its purpose will be to remove an item from the list. In an email client, for example, it would be a useful way to mark some list items as 'read' while keeping the unread items separate.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
        {this.state.list.map(item =>
          <div key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
# leanpub-start-insert
            <span>
              <button
                onClick={() => this.onDismiss(item.objectID)}
                type="button"
              >
                Dismiss
              </button>
            </span>
# leanpub-end-insert
          </div>
        )}
      </div>
    );
  }
}
~~~~~~~~

The `onDismiss()` class method is not defined yet. We will do it in a moment, but for now, let's focus on the `onClick` handler of the button element. As you can see, the `onDismiss()` method in the `onClick` handler is enclosed by an arrow function. You can use it to peek at the `objectID` property of the `item` object and identify the item to be dismissed. An alternative way would be to define the function outside of the `onClick` handler and only pass the defined function to it. We will cover handlers more in-depth as we move along.

Note the use of multilines for the button element, and how elements with multiple attributes can get disorganized easily. The button element is used with multilines and indentations to keep it readable. While this practice isn't specific to React development, it is a programming style I recommend for cleanliness and your own peace of mind.

Now we will implement the `onDismiss()` functionality. It takes an id to identify the item to dismiss. The function is bound to the class and thus becomes a class method. That's why you access it with `this.onDismiss()` and not `onDismiss()`. The `this` object is your class instance. In order to define the `onDismiss()` as class method, you have to bind it in the constructor:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      list,
    };

# leanpub-start-insert
    this.onDismiss = this.onDismiss.bind(this);
# leanpub-end-insert
  }

  render() {
    ...
  }
}
~~~~~~~~

In the next step, we define its functionality, the business logic, in the class:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      list,
    };

    this.onDismiss = this.onDismiss.bind(this);
  }

# leanpub-start-insert
  onDismiss(id) {
    ...
  }
# leanpub-end-insert

  render() {
    ...
  }
}
~~~~~~~~

Now we can define what happens inside a class method. Remember, the objective is to remove the item identified by the id from the list and store an updated list to the local state. The updated list will be used in the re-running `render()` method to display it, where the removed item should no longer appear.

You can remove an item from a list using [JavaScript's built-in filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) functionality, which takes a function as input. The function has access to each value in the list because it iterates over each item, so you can evaluate them based on certain conditions.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const words = ['spray', 'limit', 'elite', 'exuberant', 'destruction', 'present'];

const filteredWords = words.filter(function (word) { return word.length > 6; });

console.log(filteredWords);
// expected output: Array ["exuberant", "destruction", "present"]
~~~~~~~~

The function returns a new list instead of mutating the old one, and it supports the React convention of using immutable data structures.

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
# leanpub-start-insert
  const updatedList = this.state.list.filter(function isNotId(item) {
    return item.objectID !== id;
  });
# leanpub-end-insert
}
~~~~~~~~

In the next step, we extract the function and pass it to the filter function:

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
# leanpub-start-insert
  function isNotId(item) {
    return item.objectID !== id;
  }

  const updatedList = this.state.list.filter(isNotId);
# leanpub-end-insert
}
~~~~~~~~

**Remember**: You can filter more efficiently using a JavaScript ES6 arrow function.

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
# leanpub-start-insert
  const isNotId = item => item.objectID !== id;
  const updatedList = this.state.list.filter(isNotId);
# leanpub-end-insert
}
~~~~~~~~

You could even inline it again like you did in the `onClick` handler of the button, though it might get less readable:

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
# leanpub-start-insert
  const updatedList = this.state.list.filter(item => item.objectID !== id);
# leanpub-end-insert
}
~~~~~~~~

The list removes the clicked item now, but the state hasn't updated yet. Use the `setState()` class method to update the list in the local component state:

{title="src/App.js",lang="javascript"}
~~~~~~~~
onDismiss(id) {
  const isNotId = item => item.objectID !== id;
  const updatedList = this.state.list.filter(isNotId);
# leanpub-start-insert
  this.setState({ list: updatedList });
# leanpub-end-insert
}
~~~~~~~~

Run the application again and try the "Dismiss" button. What you experienced is the **unidirectional data flow** of React. An action is triggered in the view layer with `onClick()`, a function or class method modifies the local component state, and then the `render()` method of the component runs again to update the view.

### Exercises:

* Read about [the state and lifecycle in React](https://reactjs.org/docs/state-and-lifecycle.html)

## Bindings

It is important to learn about bindings in JavaScript classes when using React ES6 class components. In the previous chapter, you have bound your class method `onDismiss()` in the constructor.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      list,
    };

    this.onDismiss = this.onDismiss.bind(this);
  }

  ...
}
~~~~~~~~

The binding step is necessary because class methods don't automatically bind `this` to the class instance. Let's demonstrate it with the help of the following ES6 class component:

{title="Code Playground",lang="javascript"}
~~~~~~~~
class ExplainBindingsComponent extends Component {
  onClickMe() {
    console.log(this);
  }

  render() {
    return (
      <button
        onClick={this.onClickMe}
        type="button"
      >
        Click Me
      </button>
    );
  }
}
~~~~~~~~

The component renders just fine, but when you click the button, you see `undefined` in your developer console log. This is one of the main sources of bugs developers encounter in React. If you want to access `this.state` in your class method, it cannot be retrieved because `this` is `undefined`. To make `this` accessible in your class methods, you have to bind the class methods to `this`.

In the following class component the class method is properly bound in the class constructor:

{title="Code Playground",lang="javascript"}
~~~~~~~~
class ExplainBindingsComponent extends Component {
# leanpub-start-insert
  constructor() {
    super();

    this.onClickMe = this.onClickMe.bind(this);
  }
# leanpub-end-insert

  onClickMe() {
    console.log(this);
  }

  render() {
    return (
      <button
        onClick={this.onClickMe}
        type="button"
      >
        Click Me
      </button>
    );
  }
}
~~~~~~~~

Class method binding can happen somewhere else too. For instance, it can happen in the `render()` class method:

{title="Code Playground",lang="javascript"}
~~~~~~~~
class ExplainBindingsComponent extends Component {
  onClickMe() {
    console.log(this);
  }

  render() {
    return (
      <button
# leanpub-start-insert
        onClick={this.onClickMe.bind(this)}
# leanpub-end-insert
        type="button"
      >
        Click Me
      </button>
    );
  }
}
~~~~~~~~

Avoid this practice, however, because it binds the class method every time the `render()` method runs, meaning every time the component updates, which will hurt your application's performance eventually. Binding the class method in the constructor need only be done once, when the component is instantiated.

Some developers will define the business logic of their class methods in the constructor:

{title="Code Playground",lang="javascript"}
~~~~~~~~
class ExplainBindingsComponent extends Component {
  constructor() {
    super();

# leanpub-start-insert
    this.onClickMe = () => {
      console.log(this);
    }
# leanpub-end-insert
  }

  render() {
    return (
      <button
        onClick={this.onClickMe}
        type="button"
      >
        Click Me
      </button>
    );
  }
}
~~~~~~~~

Avoid this approach as well, as it will clutter your constructor over time. The constructor is only there to instantiate your class with all its properties, so the business logic of class methods should be defined outside the constructor.

{title="Code Playground",lang="javascript"}
~~~~~~~~
class ExplainBindingsComponent extends Component {
  constructor() {
    super();

    this.doSomething = this.doSomething.bind(this);
    this.doSomethingElse = this.doSomethingElse.bind(this);
  }

  doSomething() {
    // do something
  }

  doSomethingElse() {
    // do something else
  }

  ...
}
~~~~~~~~

Class methods can be auto-bound using JavaScript ES6 arrow functions:

{title="Code Playground",lang="javascript"}
~~~~~~~~
class ExplainBindingsComponent extends Component {
  onClickMe = () => {
    console.log(this);
  }

  render() {
    return (
      <button
        onClick={this.onClickMe}
        type="button"
      >
        Click Me
      </button>
    );
  }
}
~~~~~~~~

Use this method if the repetitive binding in the constructor annoys you. The official React documentation sticks to the class method bindings in the constructor, so this book will stick with those as well.

### Exercises:

* Try different approaches of bindings and console log the `this` object
* Learn more about [an alternative React component syntax](https://github.com/the-road-to-learn-react/react-alternative-class-component-syntax)

## Event Handler

Now we'll cover event handlers in elements. In your application, you are using the following button element to dismiss an item from the list.

{title="src/App.js",lang="javascript"}
~~~~~~~~
...

<button
  onClick={() => this.onDismiss(item.objectID)}
  type="button"
>
  Dismiss
</button>

...
~~~~~~~~

This function is already complex, because it passes a value to the class method and has to wrap it in another (arrow) function. Essentially, it has to be a function that is passed to the event handler. The following code wouldn't work, because the class method would be executed immediately when you open the application in the browser:

{title="src/App.js",lang="javascript"}
~~~~~~~~
...

<button
  onClick={this.onDismiss(item.objectID)}
  type="button"
>
  Dismiss
</button>

...
~~~~~~~~

When using `onClick={doSomething()}`, the `doSomething()` function executes immediately when the application is opened in a browser. The expression in the handler is evaluated. Since the returned value of the function isn't a function anymore, nothing would happen when you click the button. But using `onClick={doSomething}` where `doSomething` is a function, it would only be executed if the button is clicked. The same rules apply for the `onDismiss()` class method.

However, using `onClick={this.onDismiss}` wouldn't suffice, because the `item.objectID` property needs to be passed to the class method to identify the item that should be dismissed. We wrap it into another function to sneak in the property. This concept is called higher-order functions in JavaScript, which we will cover briefly later.


{title="src/App.js",lang="javascript"}
~~~~~~~~
...

<button
  onClick={() => this.onDismiss(item.objectID)}
  type="button"
>
  Dismiss
</button>

...
~~~~~~~~

We can also define wrapping function outside the method, to pass only the defined function to the handler. Since it needs access to the individual item, it has to live inside the map function block.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
        {this.state.list.map(item => {
# leanpub-start-insert
          const onHandleDismiss = () =>
            this.onDismiss(item.objectID);
# leanpub-end-insert

          return (
            <div key={item.objectID}>
              <span>
                <a href={item.url}>{item.title}</a>
              </span>
              <span>{item.author}</span>
              <span>{item.num_comments}</span>
              <span>{item.points}</span>
              <span>
                <button
# leanpub-start-insert
                  onClick={onHandleDismiss}
# leanpub-end-insert
                  type="button"
                >
                  Dismiss
                </button>
              </span>
            </div>
          );
        }
        )}
      </div>
    );
  }
}
~~~~~~~~

A function has to be passed to the element's handler. As an example, try this code instead:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
        {this.state.list.map(item =>
            ...
            <span>
              <button
# leanpub-start-insert
                onClick={console.log(item.objectID)}
# leanpub-end-insert
                type="button"
              >
                Dismiss
              </button>
            </span>
          </div>
        )}
      </div>
    );
  }
}
~~~~~~~~

This method will run when you open the application in the browser, but not when you click the button. The following code would only run when you click the button, a function that is executed when you trigger the handler:

{title="src/App.js",lang="javascript"}
~~~~~~~~
...

<button
# leanpub-start-insert
  onClick={function () {
    console.log(item.objectID)
  }}
# leanpub-end-insert
  type="button"
>
  Dismiss
</button>

...
~~~~~~~~

**Remember:** You can transform functions into a JavaScript ES6 arrow function, just as we did with the `onDismiss()` class method:

{title="src/App.js",lang="javascript"}
~~~~~~~~
...

<button
# leanpub-start-insert
  onClick={() => console.log(item.objectID)}
# leanpub-end-insert
  type="button"
>
  Dismiss
</button>

...
~~~~~~~~

Newcomers to React often have difficulty using functions in event handlers, so don't get discouraged if you have trouble on the first pass. You should end up with an inlined JavaScript ES6 arrow function with access to the `objectID` property of the `item` object:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {
  ...

  render() {
    return (
      <div className="App">
        {this.state.list.map(item =>
          <div key={item.objectID}>
            ...
            <span>
# leanpub-start-insert
              <button
                onClick={() => this.onDismiss(item.objectID)}
                type="button"
              >
                Dismiss
              </button>
# leanpub-end-insert
            </span>
          </div>
        )}
      </div>
    );
  }
}
~~~~~~~~

Using arrow functions in event handlers directly impacts your application's performance. For instance, the `onClick` handler for the `onDismiss()` method wraps the method in another arrow function to pass the item identifier. Every time the `render()` method runs, the handler instantiates the higher-order arrow function. It can have an impact on your application performance, but in most cases you won't notice. If you have a huge table of data with 1000 items and each row or column has an arrow function in an event handler, it is worth thinking about the performance implications, so you could implement a dedicated Button component to bind the method in the constructor. Before that, though, it is premature optimization, and it is more prudent learn the basics of React before thinking about optimization.

### Exercises:

* Try the different approaches of using functions in the `onClick` handler of your button

## Interactions with Forms and Events

We'll add another interaction to see forms and events in React, a search functionality where the input the search field temporarily filters a list based on the title property of an item.

In the first step, we define a form with an input field in JSX:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
# leanpub-start-insert
        <form>
          <input type="text" />
        </form>
# leanpub-end-insert
        {this.state.list.map(item =>
          ...
        )}
      </div>
    );
  }
}
~~~~~~~~

In the following scenario you will type into the input field and filter the list temporarily by the search term that is used in the input field. To filter the list based on the value of the input field, we store the value of the input field in the local state. We use **synthetic events** in React to access a value in an event payload.

Let's define a `onChange` handler for the input field:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
        <form>
# leanpub-start-insert
          <input
            type="text"
            onChange={this.onSearchChange}
          />
# leanpub-end-insert
        </form>
        ...
      </div>
    );
  }
}
~~~~~~~~

The function is bound to the component, so it is a class method again. You just need to bind and define the method:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      list,
    };

# leanpub-start-insert
    this.onSearchChange = this.onSearchChange.bind(this);
# leanpub-end-insert
    this.onDismiss = this.onDismiss.bind(this);
  }

# leanpub-start-insert
  onSearchChange() {
    ...
  }
# leanpub-end-insert

  ...
}
~~~~~~~~

When using a handler in your element, you get access to the synthetic React event in your callback function's signature.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

# leanpub-start-insert
  onSearchChange(event) {
# leanpub-end-insert
    ...
  }

  ...
}
~~~~~~~~

The event has the value of the input field in its target object, so you can update the local state with a search term using `this.setState()`.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  onSearchChange(event) {
# leanpub-start-insert
    this.setState({ searchTerm: event.target.value });
# leanpub-end-insert
  }

  ...
}
~~~~~~~~

Don't forget to define the initial state for the `searchTerm` property in the constructor. The input field should be empty in the beginning, so its value is an empty string.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      list,
# leanpub-start-insert
      searchTerm: '',
# leanpub-end-insert
    };

    this.onSearchChange = this.onSearchChange.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

  ...
}
~~~~~~~~

We store the input value to the local state every time the value in the input field changes.

We can assume that when we update `searchTerm` with `this.setState()`, the list also needs to be passed to preserve it. React's `this.setState()` is a shallow merge, however, so it preserves the sibling properties in the state object when it updates a property. The list state, though you have already dismissed an item from it, stays the same when updating the `searchTerm` property.

Returning to the application, we see the list isn't filtered yet, based on the input field value stored in the local state. We need to filter the list temporarily based on the `searchTerm`, and we have everything we need to perform this operation. In the `render()` method, before mapping over the list, we apply a filter to it. The filter will only evaluate if the `searchTerm` matches the title property of the item. We've already used the built-in JavaScript filter functionality, so let's use it again to sneak in the filter function before the map function. The filter function returns a new array, so the map function can be used on it.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
        <form>
          <input
            type="text"
            onChange={this.onSearchChange}
          />
        </form>
# leanpub-start-insert
        {this.state.list.filter(...).map(item =>
# leanpub-end-insert
          ...
        )}
      </div>
    );
  }
}
~~~~~~~~

Let's approach the filter function in a different way this time. We want to define the filter argument, which is the function passed to the filter function outside the ES6 class component. We don't have access to the state of the component, so we have no access to the `searchTerm` property to evaluate the filter condition. This means we'll need to pass the `searchTerm` to the filter function, returning a new function to evaluate the condition. This is called a higher-order function.

It makes sense to know about higher-order functions, because React deals with a concept called higher-order components. You will get to know the concept later in the book. Now again, let's focus on the filter functionality.

First, you have to define the higher-order function outside of your App component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
function isSearched(searchTerm) {
  return function (item) {
    // some condition which returns true or false
  }
}
# leanpub-end-insert

class App extends Component {

  ...

}
~~~~~~~~

The function takes the `searchTerm` and returns another function, because the filter function only takes that type as its input. The returned function has access to the item object, because it is the one passed to the filter function.

It will also be used to filter the list based on the condition defined in the function, so let's define the condition:

{title="src/App.js",lang="javascript"}
~~~~~~~~
function isSearched(searchTerm) {
  return function (item) {
# leanpub-start-insert
    return item.title.toLowerCase().includes(searchTerm.toLowerCase());
# leanpub-end-insert
  }
}

class App extends Component {

  ...

}
~~~~~~~~

The condition matches the incoming `searchTerm` pattern with the title property of the item from your list. You can do that with the built-in `includes` JavaScript functionality. When the pattern matches, it returns true and the item stays in the list; when the pattern doesn't match, the item is removed from the list. Don't forget to match the capitalizion on both strings to the letter, as there will be mismatches between the search term 'redux' and an item title 'Redux'. Since we are working on a immutable list and return a new list by using the filter function, the original list in the local state isn't modified at all.

We cheated a bit using JavaScript ES7 features, but these aren't present in ES5. For ES5, use the `indexOf()` function to get the index of the item in the list instead. When the item is in the list, `indexOf()` will return its index in the array.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES5
string.indexOf(pattern) !== -1

// ES6
string.includes(pattern)
~~~~~~~~

Another neat refactoring can be done with an ES6 arrow function again. It makes the function more concise:

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES5
function isSearched(searchTerm) {
  return function (item) {
    return item.title.toLowerCase().indexOf(searchTerm.toLowerCase()) !== -1;
  }
}

// ES6
const isSearched = searchTerm => item =>
  item.title.toLowerCase().includes(searchTerm.toLowerCase());
~~~~~~~~

The React ecosystem uses a lot of functional programming concepts, often using functions that return functions (the concept is called high-order functions) to pass information. JavaScript ES6 lets us express these even more concisely with arrow functions.

Last but not least, use the defined `isSearched()` function to filter lists. We pass it the `searchTerm` property from the local state, so that it returns the filter's input function and filters your list based on the filter condition. After that it maps over the filtered list to display an element for each list item.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
        <form>
          <input
            type="text"
            onChange={this.onSearchChange}
          />
        </form>
# leanpub-start-insert
        {this.state.list.filter(isSearched(this.state.searchTerm)).map(item =>
# leanpub-end-insert
          ...
        )}
      </div>
    );
  }
}
~~~~~~~~

The search functionality should work now. Try it yourself in the browser.

### Exercises:

* Read about [React events](https://reactjs.org/docs/handling-events.html)
* Read about [higher-order functions](https://en.wikipedia.org/wiki/Higher-order_function)

## ES6 Destructuring

Destructuring in JavaScript ES6 provides easier access to properties in objects and arrays. Compare the following snippet in JavaScript ES5 and ES6:

{title="Code Playground",lang="javascript"}
~~~~~~~~
const user = {
  firstname: 'Robin',
  lastname: 'Wieruch',
};

// ES5
var firstname = user.firstname;
var lastname = user.lastname;

console.log(firstname + ' ' + lastname);
// output: Robin Wieruch

// ES6
const { firstname, lastname } = user;

console.log(firstname + ' ' + lastname);
// output: Robin Wieruch
~~~~~~~~

While we add an extra line each time we access an object property in JavaScript ES5, it takes just one line in JavaScript ES6. For readability, use multilines when you destructure an object into multiple properties.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const {
  firstname,
  lastname
} = user;
~~~~~~~~

The same concept applies to arrays. You can destructure them, too, again using multilines to keep your code scannable and readable.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const users = ['Robin', 'Andrew', 'Dan'];
const [
  userOne,
  userTwo,
  userThree
] = users;

console.log(userOne, userTwo, userThree);
// output: Robin Andrew Dan
~~~~~~~~

Note that the local state object in the App component can get destructured the same way. You can shorten the filter and map line of code.

{title="src/App.js",lang="javascript"}
~~~~~~~~
  render() {
# leanpub-start-insert
    const { searchTerm, list } = this.state;
# leanpub-end-insert
    return (
      <div className="App">
        ...
# leanpub-start-insert
        {list.filter(isSearched(searchTerm)).map(item =>
# leanpub-end-insert
          ...
        )}
      </div>
    );
~~~~~~~~

You can do it the ES5 or ES6 way:

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES5
var searchTerm = this.state.searchTerm;
var list = this.state.list;

// ES6
const { searchTerm, list } = this.state;
~~~~~~~~

But since the book uses JavaScript ES6 most of the time, you should stick to it.

### Exercises:

* Read about [ES6 destructuring](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

## Controlled Components

We covered unidirectional data flows before, and the same law applies for the input field, which updates the local state with the `searchTerm` to filter the list. When the state changes, the `render()` method runs again and uses the recent `searchTerm` from the local state to apply the filter condition.

But didn't we forget something in the input element? An HTML input tag comes with a `value` attribute. The value attribute usually contains the value shown in the input field. In this case, that is the `searchTerm` property. Form elements such as `<input>`, `<textarea>`, and `<select>` hold their own state in plain HTML. They modify the value internally once someone changes it from the outside. In React, that's called an **uncontrolled component**, because it handles its own state. We want to make sure those elements are **controlled components** instead.

To do this, we set the value attribute of the input field, which is already saved in the `searchTerm` state property, so we can access it from there:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, list } = this.state;
    return (
      <div className="App">
        <form>
          <input
            type="text"
# leanpub-start-insert
            value={searchTerm}
# leanpub-end-insert
            onChange={this.onSearchChange}
          />
        </form>
        ...
      </div>
    );
  }
}
~~~~~~~~

The unidirectional data flow loop for the input field is self-contained, and the local component state is the single source of truth for the input field.

Local state management and unidirectional data flow might be new to you, but once you adjust to it, it will likely become your natural flow of React implementation. React brings novel patterns with unidirectional data flow, which have been adopted by several frameworks and libraries which create single page applications.

### Exercises:

* Read about [React forms](https://reactjs.org/docs/forms.html)
* Learn more about [different controlled components](https://github.com/the-road-to-learn-react/react-controlled-components-examples)

## Split Up Components

Now we have one large App component that keeps growing and may eventually become too complex to manage efficiently. We need to split it into smaller, more manageable parts by creating separate components for search input and the items list.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, list } = this.state;
    return (
      <div className="App">
# leanpub-start-insert
        <Search />
        <Table />
# leanpub-end-insert
      </div>
    );
  }
}
~~~~~~~~

We pass the components properties that they can use themselves. The App component needs to pass the properties managed in the local state and its class methods.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, list } = this.state;
    return (
      <div className="App">
# leanpub-start-insert
        <Search
          value={searchTerm}
          onChange={this.onSearchChange}
        />
        <Table
          list={list}
          pattern={searchTerm}
          onDismiss={this.onDismiss}
        />
# leanpub-end-insert
      </div>
    );
  }
}
~~~~~~~~

Now we define the components next to the App component, which will be done using JavaScript ES6 by using classes. They render the same elements as before.

The first one is the Search component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {
  ...
}

# leanpub-start-insert
class Search extends Component {
  render() {
    const { value, onChange } = this.props;
    return (
      <form>
        <input
          type="text"
          value={value}
          onChange={onChange}
        />
      </form>
    );
  }
}
# leanpub-end-insert
~~~~~~~~

The second one is the Table component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
...

# leanpub-start-insert
class Table extends Component {
  render() {
    const { list, pattern, onDismiss } = this.props;
    return (
      <div>
        {list.filter(isSearched(pattern)).map(item =>
          <div key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
            <span>
              <button
                onClick={() => onDismiss(item.objectID)}
                type="button"
              >
                Dismiss
              </button>
            </span>
          </div>
        )}
      </div>
    );
  }
}
# leanpub-end-insert
~~~~~~~~

Now you have three ES6 class components. Notice the `props` object is accessible via the class instance by using `this`. Props, short for properties, have all the values passed to the components when we used App component. That way, components can pass properties down the component tree.

By extracting these components from the App component, they become reusable. Since components get their values using the `props` object, you can pass different props to your components every time you use them somewhere else.

### Exercises:

* Discover more components that can be split up like the Search and Table components, but wait until we've covered more of its concepts before you implement any of them.

## Composable Components

The `children` prop is used to pass elements to components from above, which are unknown to the component itself but make it possible to compose components together. We'll see how this looks when you pass a text string as a child to the Search component.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, list } = this.state;
    return (
      <div className="App">
# leanpub-start-insert
        <Search
          value={searchTerm}
          onChange={this.onSearchChange}
        >
          Search
        </Search>
# leanpub-end-insert
        <Table
          list={list}
          pattern={searchTerm}
          onDismiss={this.onDismiss}
        />
      </div>
    );
  }
}
~~~~~~~~

Now the Search component can destructure the `children` property from the `props` object, and specify where it should be displayed.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Search extends Component {
  render() {
# leanpub-start-insert
    const { value, onChange, children } = this.props;
# leanpub-end-insert
    return (
      <form>
# leanpub-start-insert
        {children} <input
# leanpub-end-insert
          type="text"
          value={value}
          onChange={onChange}
        />
      </form>
    );
  }
}
~~~~~~~~

The "Search" text should now be visible next to your input field. When you use the Search component elsewhere, you can use different entities, since it's not just text that can be passed as children. You can also pass an element, or element trees that can be encapsulated by components, as children. The children property makes it possible to weave components into each other.

### Exercises:

* Read about [the composition model of React](https://reactjs.org/docs/composition-vs-inheritance.html)

## Reusable Components

Reusable and composable components empower you to come up with capable component hierarchies, the foundation of React's view layer. The last sections mentioned reusability, and now we can see how reusing the Table and Search components works in our case. Even the App component is reusable, as it can be instantiated elsewhere as well.

Let's define one more reusable component, a Button component which we'll eventually reuse often:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Button extends Component {
  render() {
    const {
      onClick,
      className,
      children,
    } = this.props;

    return (
      <button
        onClick={onClick}
        className={className}
        type="button"
      >
        {children}
      </button>
    );
  }
}
~~~~~~~~

It might seem redundant to declare components like this, but it's not. We use a `Button` component instead of a `button` element, which spares only the `type="button"`. It might not seem like a huge win, but these measures are about long term, however. Imagine you have several buttons in your application, and you want to change an attribute, style, or behavior for just one. Without the component, you'd have to change (refactor) each one. The Button component ensures that the operation has a single source of truth, or one Button to refactor all the others at once.

Since you already have a button element, you can use the Button component instead. It omits the type attribute, because the Button component specifies it.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Table extends Component {
  render() {
    const { list, pattern, onDismiss } = this.props;
    return (
      <div>
        {list.filter(isSearched(pattern)).map(item =>
          <div key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
            <span>
# leanpub-start-insert
              <Button onClick={() => onDismiss(item.objectID)}>
                Dismiss
              </Button>
# leanpub-end-insert
            </span>
          </div>
        )}
      </div>
    );
  }
}
~~~~~~~~

The Button component expects a `className` property in the `props`. The `className` attribute is another React derivate for the HTML attribute class. We didn't pass any `className` when the Button was used, though. It should be more explicit in our Button component that the `className` is optional, so we'll assign a default value in the object destructuring.

{title="src/App.js",lang="javascript"}
~~~~~~~~
class Button extends Component {
  render() {
    const {
      onClick,
# leanpub-start-insert
      className = '',
# leanpub-end-insert
      children,
    } = this.props;

    ...
  }
}
~~~~~~~~

Now, whenever there is no `className` property specified in the Button component, the value will be an empty string instead of `undefined`.

### Exercises:

* Read about [how to pass props in React](https://www.robinwieruch.de/react-pass-props-to-component/)

## Component Declarations

Now we have four ES6 class components, but the application can still be improved using functional stateless components as alternative for ES6 class components. Before you refactor your components, let's introduce the different types.

* **Functional Stateless Components** are functions that take input and return an output. The inputs are the props, and the output is a component instance in plain JSX. So far, it is quite similar to an ES6 class component. However, functional stateless components are functions (functional) and they have no local state (stateless). You cannot access or update the state with `this.state` or `this.setState()` because there is no `this` object. Additionally, they have no lifecycle methods except for the `render()` method which will be applied implicitly in functional stateless components. You didn't learn about lifecycle methods yet, but you already used two: `constructor()` and `render()`. The constructor runs only once in the lifetime of a component, whereas the `render()` class method runs once in the beginning and every time the component updates. Keep in mind that functional stateless components have no lifecycle methods, when we arrive at lifecycle methods chapter later.

* **ES6 Class Components** extend from the React component. The `extend` hooks all the lifecycle methods, available in the React component API, to the component. This is how we were able to use the `render()` class method. You can also store and manipulate state in ES6 class components using `this.state` and `this.setState()`.

* **React.createClass** was used in older versions of React, and is still used in JavaScript ES5 React applications. But [Facebook declared it as deprecated](https://reactjs.org/blog/2015/03/10/react-v0.13.html) in favor of JavaScript ES6. They even added a [deprecation warning in version 15.5](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html), so we will not use it in the book.

When deciding when to use functional stateless components over ES6 class components, a good rule of thumb is to use functional stateless components when you don't need local state or component lifecycle methods. Usually, we implement components as functional stateless components, but once access to the state or lifecycle methods is required, we have to refactor it to an ES6 class component. We started the other way around in our application for the sake of learning.

Returning to the application, we see the App component uses local state, so it has to stay as an ES6 class component. The other three ES6 class components are stateless, so they don't need access to `this.state` or `this.setState()`, and they have no lifecycle methods. We're going to refactor the Search component to a stateless functional component. The Table and Button component refactoring will become your exercise.

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
function Search(props) {
  const { value, onChange, children } = props;
  return (
    <form>
      {children} <input
        type="text"
        value={value}
        onChange={onChange}
      />
    </form>
  );
}
# leanpub-end-insert
~~~~~~~~

The `props` are accessible in the function signature, and the return value is JSX; but we can do more with the code in a functional stateless component using ES6 destructuring. The best practice is to use it in the function signature to destructure the `props`:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
function Search({ value, onChange, children }) {
# leanpub-end-insert
  return (
    <form>
      {children} <input
        type="text"
        value={value}
        onChange={onChange}
      />
    </form>
  );
}
~~~~~~~~

Remember that ES6 arrow functions allow you to keep your functions concise and let you remove the block body of the function. In a concise body, an implicit return is attached, letting us remove the `return` statement. Since the functional stateless component is a function, it can be made more concise as well:

{title="src/App.js",lang="javascript"}
~~~~~~~~
# leanpub-start-insert
const Search = ({ value, onChange, children }) =>
  <form>
    {children} <input
      type="text"
      value={value}
      onChange={onChange}
    />
  </form>
# leanpub-end-insert
~~~~~~~~

The last step is especially useful to enforce props as input and JSX as output. Still, you could *do something* in between by using a block body in your ES6 arrow function:

{title="Code Playground",lang="javascript"}
~~~~~~~~
const Search = ({ value, onChange, children }) => {

  // do something

  return (
    <form>
      {children} <input
        type="text"
        value={value}
        onChange={onChange}
      />
    </form>
  );
}
~~~~~~~~

Now you have one lightweight functional stateless component. When we need access to the local component state or lifecycle methods, we can refactor it to a ES6 class component. When using block bodies, programmers tend to make their functions more complex, but leaving the block body out lets you focus on the input and output. JavaScript ES6 in React components makes components more readable and elegant.

### Exercises:

* Refactor the Table and Button component to stateless functional components
* Read about [ES6 class components and functional stateless components](https://reactjs.org/docs/components-and-props.html)

## Styling Components

In this section, we'll add some basic styling to our application and components using the *src/App.css* and *src/index.css* files. These files should already be in your project, since you have bootstrapped it with *create-react-app*. They should be imported in your *src/App.js* and *src/index.js* files too. The following is CSS that can be copied and pasted to these files, but feel free to use your own if you're comfortable with CSS.

First, styling for your overall application:

{title="src/index.css",lang="css"}
~~~~~~~~
body {
  color: #222;
  background: #f4f4f4;
  font: 400 14px CoreSans, Arial, sans-serif;
}

a {
  color: #222;
}

a:hover {
  text-decoration: underline;
}

ul, li {
  list-style: none;
  padding: 0;
  margin: 0;
}

input {
  padding: 10px;
  border-radius: 5px;
  outline: none;
  margin-right: 10px;
  border: 1px solid #dddddd;
}

button {
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #dddddd;
  background: transparent;
  color: #808080;
  cursor: pointer;
}

button:hover {
  color: #222;
}

*:focus {
  outline: none;
}
~~~~~~~~

Second, styling for your components in the App file:

{title="src/App.css",lang="css"}
~~~~~~~~
.page {
  margin: 20px;
}

.interactions {
  text-align: center;
}

.table {
  margin: 20px 0;
}

.table-header {
  display: flex;
  line-height: 24px;
  font-size: 16px;
  padding: 0 10px;
  justify-content: space-between;
}

.table-empty {
  margin: 200px;
  text-align: center;
  font-size: 16px;
}

.table-row {
  display: flex;
  line-height: 24px;
  white-space: nowrap;
  margin: 10px 0;
  padding: 10px;
  background: #ffffff;
  border: 1px solid #e3e3e3;
}

.table-header > span {
  overflow: hidden;
  text-overflow: ellipsis;
  padding: 0 5px;
}

.table-row > span {
  overflow: hidden;
  text-overflow: ellipsis;
  padding: 0 5px;
}

.button-inline {
  border-width: 0;
  background: transparent;
  color: inherit;
  text-align: inherit;
  -webkit-font-smoothing: inherit;
  padding: 0;
  font-size: inherit;
  cursor: pointer;
}

.button-active {
  border-radius: 0;
  border-bottom: 1px solid #38BB6C;
}
~~~~~~~~

Now we can use this style with some of our components. Remember to use React `className` instead of `class` as an HTML attribute.

First, apply it in your App ES6 class component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
class App extends Component {

  ...

  render() {
    const { searchTerm, list } = this.state;
    return (
# leanpub-start-insert
      <div className="page">
        <div className="interactions">
# leanpub-end-insert
          <Search
            value={searchTerm}
            onChange={this.onSearchChange}
          >
            Search
          </Search>
# leanpub-start-insert
        </div>
# leanpub-end-insert
        <Table
          list={list}
          pattern={searchTerm}
          onDismiss={this.onDismiss}
        />
# leanpub-start-insert
      </div>
# leanpub-end-insert
    );
  }
}
~~~~~~~~

Second, apply it in your Table functional stateless component:

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Table = ({ list, pattern, onDismiss }) =>
# leanpub-start-insert
  <div className="table">
# leanpub-end-insert
    {list.filter(isSearched(pattern)).map(item =>
# leanpub-start-insert
      <div key={item.objectID} className="table-row">
# leanpub-end-insert
        <span>
          <a href={item.url}>{item.title}</a>
        </span>
        <span>{item.author}</span>
        <span>{item.num_comments}</span>
        <span>{item.points}</span>
        <span>
          <Button
            onClick={() => onDismiss(item.objectID)}
# leanpub-start-insert
            className="button-inline"
# leanpub-end-insert
          >
            Dismiss
          </Button>
        </span>
# leanpub-start-insert
      </div>
# leanpub-end-insert
    )}
# leanpub-start-insert
  </div>
# leanpub-end-insert
~~~~~~~~

Now the application and components have been styled with basic CSS. Further, we know JSX mixes up HTML and JavaScript, and now we could arguably add CSS to that mix. That's called inline style, where you can define JavaScript objects and pass them to the style attribute of an element.

Let's keep the Table column width flexible by using inline style.

{title="src/App.js",lang="javascript"}
~~~~~~~~
const Table = ({ list, pattern, onDismiss }) =>
  <div className="table">
    {list.filter(isSearched(pattern)).map(item =>
      <div key={item.objectID} className="table-row">
# leanpub-start-insert
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
# leanpub-end-insert
      </div>
    )}
  </div>
~~~~~~~~

The style is inlined now. Define the style objects outside of your elements to make it cleaner.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const largeColumn = {
  width: '40%',
};

const midColumn = {
  width: '30%',
};

const smallColumn = {
  width: '10%',
};
~~~~~~~~

After that, we use them in the columns: `<span style={smallColumn}>`. There different opinions and solutions about style in React, but the pure inline CSS we used is sufficient for this tutorial. I don't want to be opinionated here, but I want to leave you some more options. You can read about them and apply them on your own:

* [styled-components](https://github.com/styled-components/styled-components)
* [CSS Modules](https://github.com/css-modules/css-modules) (read my short article on [how to use CSS modules in create-react-app](https://www.robinwieruch.de/create-react-app-css-modules/))
* [Sass](https://sass-lang.com/) (read my short article on [how to use Sass in create-react-app](https://www.robinwieruch.de/create-react-app-with-sass-support/))

But if you are new to React, I would recommend to stick to pure CSS and inline style for now.

{pagebreak}

You have learned the basics on how to write your own React application! Let's recap the last chapter:

* **React**
  * Use `this.state` and `setState()` to manage your local component state
  * Pass functions or class methods to your element handler
  * Use forms and events in React to add interactions
  * Unidirectional data flow is an important concept in React
  * Embrace controlled components
  * Compose components with children and reusable components
  * Usage and implementation of ES6 class components and functional stateless components
  * Approaches to style your components
* **ES6**
  * Functions that are bound to a class are class methods
  * Destructuring of objects and arrays
  * Default parameters
* **General**
  * Higher-order functions

Again, it makes sense to take a break, internalize the lessons, and apply them on your own. Experiment with the source code you have written so far. The source code for this project is found in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.2).
