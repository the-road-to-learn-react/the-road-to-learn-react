# Basics in React

The chapter will guide you through the basics of React. It covers state and interactions in components as we move past static components.  We will also cover  the different ways to declare a component, and how to keep components composable and reusable.  

## Local Component State

Local component state, also known as local state, allows you to save, modify, and delete properties stored in your component. The ES6 class component then uses a constructor to initialize local component state. The constructor is called only once, when the component initializes:

{title="src/App.js",lang="javascript"
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

It is mandatory to call `super(props);`.  It sets `this.props` in your constructor in case you want to access them there. They would be `undefined` when accessing `this.props` in your constructor otherwise.  In this case, the initial state of the component should be the sample list of items:

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

The state is bound to the class using the `this` object. so you can access the local state of the whole component. For instance, it can be used in the `render()` method. Previously you have mapped a static list of items in your `render()` method that was defined outside of your component. Now you are about to use the list from your local state in your component.

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

* experiment with the local state
  * define more initial state in the constructor
  * use and access the state in your `render()` method
*  read more about  [the ES6 class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)

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

* experiment with ES6 object initializer
* read more about  [ES6 object initializer](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer)

## Unidirectional Data Flow

Now you have a local state in your App component, but haven't manipulated the state yet. The local state is static, so its component is as well.  A good way to experience state manipulation is to engage in component interaction.

We will practice this concept by adding a button for each item in the displayed list. The button will read "Dismiss", as its purpose will be to remove an item from the list.  In an email client, for example, it would be a useful way to mark some list items as 'read' while keeping the unread items separate.

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

The `onDismiss()` class method is not defined yet. We will do it in a moment, but for now, let's focus on the `onClick` handler of the button element. As you can see, the `onDismiss()` method in the `onClick` handler is enclosed by an arrow function.  You can use it to peek at the `objectID` property of the `item` object and identify the item to be dismissed. An alternative way would be to define the function outside of the `onClick` handler and only pass the defined function to it. We will cover handlers more in-depth as we move along.

Note the use multilines for the button element, and how elements with multiple attributes can get disorganized easily. The button element is used with multilines and indentations to keep it readable.  While this practice isn't specific to React development, it is a programming style I recommend for cleanliness and your own peace of mind.

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

Now we can define what happens inside a class method. Remember, the objective is to remove the item identified by the id from the list and store an updated list to the local state.  The updated list will be used in the re-running `render()` method to display it, where the removed item should no longer appear.

You can remove an item from a list using [JavaScript's built-in filter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) functionality, which takes a function as input. The function has access to each value in the list because it iterates over each item, so you can evaluate them based on certain conditions. The function returns a new list instead of mutating the old one, and it supports the React convention of using immutable data structures.

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

**Remember**: You can filter moreefficiently using a JavaScript ES6 arrow function.

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

Run the application again and try the "Dismiss" button.  What you experienced is the **unidirectional data flow** of React.  An action is triggered in the view layer with `onClick()`, a function or class method modifies the local component state, and then the `render()` method of the component runs again to update the view.

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

The component renders just fine, but when you click the button, you see `undefined` in your developer console log. This is one of the main sources of bugs developers encounter in React. If you want to access `this.state` in your class method, it cannot be retrieved because `this` is `undefined`.  To make `this` accessible in your class methods, you have to bind the class methods to `this`.

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

Avoid this practice, however, because it binds the class method every time the `render()` method runs, meaning every time the component updates, which will hurt your application's performance. Binding the class method in the constructor need only be done once, when the component is instantiated.

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

Avoid this method as well, as it will clutter your constructor over time. The constructor is only there to instantiate your class with all its properties, so the business logic of class methods should be defined outside the constructor.

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

* try the different approaches of bindings and console log the `this` object

## Event Handler

Now we'll cover event handlers in elements. In your application, you are using the following button element to dismiss an item from the list.

{title="Code Playground",lang="javascript"} 
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

{title="Code Playground",lang="javascript"} 
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

However, using `onClick={this.onDismiss}` wouldn't suffice, because the `item.objectID`property needs to be passed to the class method to identify the item that should be dismissed. We wrap it into another function to sneak in the property. This concept is called higher-order functions in JavaScript, which we will cover briefly later.

{title="Code Playground",lang="javascript"} 
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

{title="Code Playground",lang="javascript"} 
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

After all, it has to be a function that is passed to the element's handler. As an example, try this code instead:

{title="Code Playground",lang="javascript"} 
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

{title="Code Playground",lang="javascript"} 
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

>**Remember:** You can transform functions into a JavaScript ES6 arrow function, just as we did with the `onDismiss()` class method:

{title="Code Playground",lang="javascript"} 
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

Newcomers to React often have difficulty using functions in event handlers, so don't get discouraged if you have trouble on the first pass. You should end up with the following code in your button, for an inlined JavaScript ES6 arrow function with access to the `objectID` property of the `item` object:

{title="Code Playground",lang="javascript"} 
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

* try the different approaches of using functions in the `onClick` handler of your button

## Interactions with Forms and Events

Now, we'll add another interaction for the application to experience forms and events using React. The interaction is a search functionality, where the input of the search field is used to temporarily filter a list based on the title property of an item.

In the first step, we define a form with an input field in JSX:

{title="Code Playground",lang="javascript"} 
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

In the following scenario you will place a temporary list in the input field and filter by the search term used in the input field. To filter the list based on the value of the input field, we store the value of the input field in the local state.  We use **synthetic events** in React to access the value in the event payload.

Let's define a `onChange` handler for the input field:

{title="Code Playground",lang="javascript"} 
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

{title="Code Playground",lang="javascript"} 
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

{title="Code Playground",lang="javascript"} 
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

{title="Code Playground",lang="javascript"} 
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

Don't forget to define the initial state for the `searchTerm` property in the constructor. The input field should be empty in the beginning, so its value is empty string.

{title="Code Playground",lang="javascript"}
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

We can assume that when updating the `searchTerm` with `this.setState(),` the list also needs to be passed to preserve it. React's `this.setState()` is a shallow merge, however, so it preserves the sibling properties in the state object when it updates a property.  The list state, though you have already dismissed an item from it, stays the same when updating the `searchTerm` property.

Returning to the application, we see the list isn't filtered yet, based on the input field value stored in the local state. We need to filter the list temporarily based on the `searchTerm`, and we have everything we need to perform this operation.  In the `render()` method, before mapping over the list, we apply a filter to it. The filter will only evaluate if the `searchTerm` matches the title property of the item.  We've already used the built-in JavaScript filter functionality, so let's use it again to sneak in the filter function before the map function. The filter function returns a new array, so the map function can be used on it in lots of useful ways.

{title="Code Playground",lang="javascript"} 
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

Let's approach the filter function in a different way this time. We want to define the filter argument, the function that is passed to the filter function, outside of the ES6 class component. There we don't have access to the state of the component and thus we have no access to the `searchTerm` property to evaluate the filter condition. We have to pass the `searchTerm` to the filter function and have to return a new function to evaluate the condition. That's called a higher-order function.

Normally I wouldn't mention higher-order functions, but in a React book it makes total sense. It makes sense to know about higher-order functions, because React deals with a concept called higher-order components. You will get to know the concept later in the book. Now again, let's focus on the filter functionality.

First, you have to define the higher-order function outside of your App component.

{title="Code Playground",lang="javascript"}
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

The function takes the `searchTerm` and returns another function, because after all the filter function takes a function as its input. The returned function has access to the item object because it is the function that is passed to the filter function. In addition, the returned function will be used to filter the list based on the condition defined in the function. Let's define the condition.

{title="Code Playground",lang="javascript"}
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

The condition says that you match the incoming `searchTerm` pattern with the title property of the item from your list. You can do that with the built-in `includes` JavaScript functionality. Only when the pattern matches, you return true and the item stays in the list. When the pattern doesn't match the item is removed from the list. But be careful with pattern matching: You shouldn't forget to lower case both strings. Otherwise there will be mismatches between a search term 'redux' and an item title 'Redux'. Since we are working on a immutable list and return a new list by using the filter function, the original list in the local state isn't modified at all.

One thing is left to mention: We cheated a bit by using the [built-in includes JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/includes) functionality. It is already a JavaScriptES7 feature. How would that look like in JavaScript ES5? You would use the `indexOf()` function to get the index of the item in the list. When the item is in the list, `indexOf()` will return its index in the array.

{title="Code Playground",lang="javascript"}
~~~~~~~~
// ES5
string.indexOf(pattern) !== -1

// ES7
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

// ES6 + ES7
const isSearched = searchTerm => item =>
  item.title.toLowerCase().includes(searchTerm.toLowerCase());
~~~~~~~~

One could argue which function is more readable. Personally I prefer the second one. The React ecosystem uses a lot of functional programming concepts. It happens often that you will use a function which returns a function (higher-order functions). In JavaScript ES6, you can express these more concisely with arrow functions.

Last but not least, you have to use the defined `isSearched()` function to filter your list. You pass it the `searchTerm` property from your local state, it returns the filter input function, and filters your list based on the filter condition. Afterward it maps over the filtered list to display an element for each list item.

{title="Code Playground",lang="javascript"}
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

* read more about [React events](https://reactjs.org/docs/handling-events.html)
* read more about [higher-order functions](https://en.wikipedia.org/wiki/Higher-order_function)

## ES6 Destructuring

There is a way in JavaScript ES6 for an easier access to properties in objects and arrays. It's called destructuring. Compare the following snippet in JavaScript ES5 and ES6.

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

While you have to add an extra line each time you want to access an object property in JavaScript ES5, you can do it in one line in JavaScript ES6. A best practice for readability is to use multilines when you destructure an object into multiple properties.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const {
  firstname,
  lastname
} = user;
~~~~~~~~

The same goes for arrays. You can destructure them too. Again, multilines will keep your code scannable and readable.

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

Perhaps you have noticed that the local state object in the App component can get destructured the same way. You can shorten the filter and map line of code.

{title="Code Playground",lang="javascript"}
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

But since the book uses JavaScript ES6 and beyond most of the time, you should stick to it.

### Exercises:

* read more about [ES6 destructuring](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

## Controlled Components

You already learned about the unidirectional data flow in React. The same law applies for the input field, which updates the local state with the `searchTerm` in order to filter the list. When the state changes, the `render()` method runs again and uses the recent `searchTerm` from the local state to apply the filter condition.

But didn't we forget something in the input element? A HTML input tag comes with a `value` attribute. The value attribute usually has the value that is shown in the input field. In this case it would be the `searchTerm` property. However, it seems like we don't need that in React.

That's wrong. Form elements such as `<input>`, `<textarea>` and `<select>` hold their own internal state in plain HTML. They modify the value internally once someone changes it from the outside. In React that's called an **uncontrolled component**, because it handles its own state. In React, you should make sure to make those elements **controlled components**.

How should you do that? You only have to set the value attribute of the input field. The value is already saved in the `searchTerm` state property. So why not access it from there?

{title="Code Playground",lang="javascript"} ~~~~~~~~
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

That's it. The unidirectional data flow loop for the input field is self-contained now. The local component state is the single source of truth for the input field.

The whole local state management and unidirectional data flow might be new to you. But once you are used to it, it will be your natural flow to implement things in React. In general, React brought a novel pattern with the unidirectional data flow to the world of single page applications. It is now adopted by several frameworks and libraries.

### Exercises:

* read more about [React forms](https://reactjs.org/docs/forms.html)
* learn more about [different controlled components](https://github.com/the-road-to-learn-react/react-controlled-components-examples)

## Split Up Components

You now have one large App component, that keeps growing and can eventually become confusing. Let's start to split it up into chunks of smaller components, creating separate components for the search input and for the list of items.

{title="Code Playground",lang="javascript"} 
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

You can pass those components properties which they can use themselves. In the case of the App component it needs to pass the properties managed in the local state and its class methods.

{title="Code Playground",lang="javascript"}
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

Now you can define the components next to your App component. Those components will be ES6 class components as well. They render the same elements like before.

The first one is the Search component.

{title="Code Playground",lang="javascript"}
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

{title="Code Playground",lang="javascript"} 
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

Now you have three ES6 class components. Perhaps you have noticed the `props` object that is accessible via the class instance by using `this`. The props, short for properties, have all the values you have passed to the components when you used them in your App component. That way, components can pass properties down the component tree.

By extracting those components from the App component, they have become reusable. Since components get their values by using the `props` object, you can pass different props to your components every time you use them somewhere else.

### Exercises:

* figure out further components that you could split up as you have done with the Search and Table components
  * but don't do it now, otherwise you will run into conflicts in the next chapters

## Composable Components

There is one more little property which is accessible in the props object: the `children` prop. You can use it to pass elements to your components from above, which are unknown to the component itself but which make it possible to compose your components together. Let's see how this looks when you pass a text string, as a child, to the Search component.

{title="Code Playground",lang="javascript"}
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

{title="Code Playground",lang="javascript"} ~~~~~~~~
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

The "Search" text should now be visible next to your input field. And, when you use the Search component somewhere else, you can, if you like, use different text. After all, it's not only text that can be passed as children. You can pass an element, or element trees (which can be encapsulated by components again), as children. The children property makes it possible to weave components into each other.

### Exercises:

* read more about [the composition model of React](https://reactjs.org/docs/composition-vs-inheritance.html)
* read more about [passing props in React](https://www.robinwieruch.de/react-pass-props-to-component/)

## Reusable Components

Reusable and composable components empower you to come up with capable component hierarchies. They are the foundation of React's view layer. The last chapters mentioned reusability. Now, you can reuse the Table and Search components. Even the App component is reusable, as you could instantiate it somewhere else again.

Let's define one more reusable component, a Button component, which will, eventually, be reused more often.

{title="Code Playground",lang="javascript"}
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

It might seem redundant to declare a component such as this. You will use a `Button` component instead of a `button` element. It only spares the `type="button"`. Except for the type attribute you have to define everything else when you want to use the Button component. But you have to think about the long term investment here. Imagine you have several buttons in your application, but want to change an attribute, style or behavior for the button. Without the component you would have to refactor every button. Instead the Button component ensures to have only one single source of truth. One Button to refactor all buttons at once. One Button to rule them all.

Since you already have a button element, you can use the Button component instead. It omits the type attribute, because the Button component specifies it.

{title="Code Playground",lang="javascript"} 
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

The Button component expects a `className` property in the `props`. The `className` attribute is another React derivate for the HTML attribute class. But we didn't pass any `className` when the Button was used. In the code it should be more explicit in the Button component that the `className` is optional. Therefore, you can assign a default value in your object destructuring.

{title="Code Playground",lang="javascript"}
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

Now, whenever there is no `className` property specified when using the Button component, the value will be an empty string instead of `undefined`.

## Component Declarations

By now you have four ES6 class components. But you can do better. Let me introduce functional stateless components as alternative for ES6 class components. Before you refactor your components, let's introduce the different types of components in React.

* **Functional Stateless Components:** These components are functions which get an input and return an output. The input are the props. The output is a component instance thus plain JSX. So far it is quite similar to an ES6 class component. However, functional stateless components are functions (functional) and they have no local state (stateless). You cannot access or update the state with `this.state` or `this.setState()` because there is no `this` object. Additionally, they have no lifecycle methods. You didn't learn about lifecycle methods yet, but you already used two: `constructor()` and `render()`. Whereas the constructor runs only once in the lifetime of a component, the `render()` class method runs once in the beginning and every time the component updates. Keep in mind that functional stateless components have no lifecycle methods, when you arrive at the lifecycle methods chapter later on.

* **ES6 Class Components:** You already used this type of component declaration in your four components. In the class definition, they extend from the React component. The `extend` hooks all the lifecycle methods, available in the React component API, to the component. That way you were able to use the `render()` class method. Additionally, you can store and manipulate state in ES6 class components by using `this.state` and `this.setState()`.

* **React.createClass:** The component declaration was used in older versions of React and still in JavaScript ES5 React applications. But [Facebook declared it as deprecated](https://reactjs.org/blog/2015/03/10/react-v0.13.html) in favor of JavaScript ES6. They even added a [deprecation warning in version 15.5](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html). You will not use it in the book.

So basically there are only two component declarations left. But when to use functional stateless components over ES6 class components? A rule of thumb is to use functional stateless components when you don't need local state or component lifecycle methods. Usually you start to implement your components as functional stateless components. Once you need access to the state or lifecycle methods, you have to refactor it to an ES6 class component. In our application, we started the other way around for the sake of learning React.

Let's get back to your application. The App component uses local state. That's why it has to stay as an ES6 class component. But the other three of your ES6 class components are stateless. They don't need access to `this.state` or `this.setState()`. Even more, they have no lifecycle methods. Let's refactor together the Search component to a stateless functional component. The Table and Button component refactoring will remain as your exercise.

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

That's basically it. The `props` are accessible in the function signature and the return value is JSX. But you can do more code wise in a functional stateless component. You already know the ES6 destructuring. The best practice is to use it in the function signature to destructure the `props`.

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

But it can get better. You know already that ES6 arrow functions allow you to keep your functions concise. You can remove the block body of the function. In a concise body an implicit return is attached thus you can remove the `return` statement. Since your functional stateless component is a function, you can keep it concise as well.

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

The last step was especially useful to enforce only to have props as input and JSX as output. Nothing in between. Still, you could *do something* in between by using a block body in your ES6 arrow function.

{title="Code Playground",lang="javascript"} ~~~~~~~~
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

But you don't need it for now. That's why you can keep the previous version without the block body. When using block bodies, people often tend to do too many things in the function. By leaving the block body out, you can focus on the input and output of your function.

Now you have one lightweight functional stateless component. Once you would need access to its local component state or lifecycle methods, you would refactor it to an ES6 class component. In addition you saw how JavaScript ES6 can be used in React components to make them more concise and elegant.

### Exercises:

* refactor the Table and Button component to stateless functional components
* read more about [ES6 class components and functional stateless components](https://reactjs.org/docs/components-and-props.html)

## Styling Components

Let's add some basic styling to your application and components. You can reuse the *src/App.css* and *src/index.css* files. These files should already be in your project since you have bootstrapped it with *create-react-app*. They should be imported in your *src/App.js* and *src/index.js* files too. I prepared some CSS which you can simply copy and paste to these files, but feel free to use your own style at this point.

First, styling for your overall application.

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

Second, styling for your components in the App file.

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

Now you can use the style in some of your components. Don't forget to use React `className` instead of `class` as HTML attribute.

First, apply it in your App ES6 class component.

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

Second, apply it in your Table functional stateless component.

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

Now you have styled your application and components with basic CSS. It should look quite decent. As you know, JSX mixes up HTML and JavaScript. Now one could argue to add CSS in the mix as well. That's called inline style. You can define JavaScript objects and pass them to the style attribute of an element.

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

The style is inlined now. You could define the style objects outside of your elements to make it cleaner.

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

After that you would use them in your columns: `<span style={smallColumn}>`.

In general, you will find different opinions and solutions for style in React. You used pure CSS and inline style now. That's sufficient to get started.

I don't want to be opinionated here, but I want to leave you some more options. You can read about them and apply them on your own. But if you are new to React, I would recommend to stick to pure CSS and inline style for now.

* [styled-components](https://github.com/styled-components/styled-components)
* [CSS Modules](https://github.com/css-modules/css-modules)

{pagebreak}

You have learned the basics to write your own React application! Let's recap the last chapters:

* React
  * use `this.state` and `setState()` to manage your local component state
  * pass functions or class methods to your element handler
  * use forms and events in React to add interactions
  * unidirectional data flow is an important concept in React
  * embrace controlled components
  * compose components with children and reusable components
  * usage and implementation of ES6 class components and functional stateless components
  * approaches to style your components
* ES6
  * functions that are bound to a class are class methods
  * destructuring of objects and arrays
  * default parameters
* General
  * higher-order functions

Again it makes sense to take a break. Internalize the learnings and apply them on your own. You can experiment with the source code you have written so far. You can find the source code in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.2).
rty of the item. We've already used the built-in JavaScript filter functionality, so let's use it again to sneak in the filter function before the map function.  The filter function returns a new array, so the map function can be used on it in lots of useful ways.

{title="Code Playground",lang="javascript"} 
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

Let's approach the filter function in a different way this time. We want to define the filter argument, the function that is passed to the filter function, outside of the ES6 class component. There we don't have access to the state of the component and thus we have no access to the `searchTerm` property to evaluate the filter condition. We have to pass the `searchTerm` to the filter function and have to return a new function to evaluate the condition. That's called a higher-order function.

Normally I wouldn't mention higher-order functions, but in a React book it makes total sense. It makes sense to know about higher-order functions, because React deals with a concept called higher-order components. You will get to know the concept later in the book. Now again, let's focus on the filter functionality.

First, you have to define the higher-order function outside of your App component.

{title="Code Playground",lang="javascript"} 
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

The function takes the `searchTerm` and returns another function, because after all the filter function takes a function as its input. The returned function has access to the item object because it is the function that is passed to the filter function. In addition, the returned function will be used to filter the list based on the condition defined in the function. Let's define the condition.

{title="Code Playground",lang="javascript"} 
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

The condition says that you match the incoming `searchTerm` pattern with the title property of the item from your list. You can do that with the built-in `includes` JavaScript functionality. Only when the pattern matches, you return true and the item stays in the list. When the pattern doesn't match the item is removed from the list. But be careful with pattern matching: You shouldn't forget to lower case both strings. Otherwise there will be mismatches between a search term 'redux' and an item title 'Redux'. Since we are working on a immutable list and return a new list by using the filter function, the original list in the local state isn't modified at all.

One thing is left to mention: We cheated a bit by using the built-in includes JavaScript functionality. It is already an ES6 feature. How would that look like in JavaScript ES5? You would use the `indexOf()` function to get the index of the item in the list. When the item is in the list, `indexOf()` will return its index in the array.

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

One could argue which function is more readable. Personally I prefer the second one. The React ecosystem uses a lot of functional programming concepts. It happens often that you will use a function which returns a function (higher-order functions). In JavaScript ES6, you can express these more concisely with arrow functions.

Last but not least, you have to use the defined `isSearched()` function to filter your list. You pass it the `searchTerm` property from your local state, it returns the filter input function, and filters your list based on the filter condition. Afterward it maps over the filtered list to display an element for each list item.

{title="Code Playground",lang="javascript"} 
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

* read more about [React events](https://reactjs.org/docs/handling-events.html)
* read more about [higher-order functions](https://en.wikipedia.org/wiki/Higher-order_function)

## ES6 Destructuring

There is a way in JavaScript ES6 for an easier access to properties in objects and arrays, called *destructuring*. Compare the following snippet in JavaScript ES5 and ES6:

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

While you have to add an extra line each time you want to access an object property in JavaScript ES5, you can do it in one line in JavaScript ES6. Use multilines when you destructure an object into multiple properties if you want to increase readability.

{title="Code Playground",lang="javascript"}
~~~~~~~~
const {
  firstname,
  lastname
} = user;
~~~~~~~~

You can also destructure arrays. Again, multilines will keep your code scannable and readable:

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

By now, you've probably noticed that the local state object in the App component can get destructured in the same way. You can shorten the filter and map line of code:

{title="Code Playground",lang="javascript"}
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

Since the book uses JavaScript ES6 most of the time, you should stick to it.

### Exercises:

* read more about [ES6 destructuring](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

## Controlled Components

We learned about the unidirectional data flow in React previously. The same rules apply for the input field, which updates the local state with the `searchTerm` in order to filter the list. When the state changes, the `render()` method runs again, using the recent `searchTerm` from the local state to apply the filter condition.

Remember , though, an HTML input tag comes with a `value` attribute. The value attribute usually has the value that is shown in the input field, which is the `searchTerm` property in this case.  It may seem like we don't need that in React, but form elements such as `<input>`, `<textarea>` and `<select>` hold their own state in plain HTML. They modify the value internally once it is changed externally. In React, that's called an **uncontrolled component**, because it handles its own state. We want to make sure those elements are **controlled components** in our React applications.

To do this, set the value attribute of the input field, which was already saved in the `searchTerm` state property:

{title="Code Playground",lang="javascript"}
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

The unidirectional data flow loop for the input field is now self-contained. The local component state is the single source of truth for the input field.

Once you are used to local state management and unidirectional data flow, they will become your default way of React implementation. React brought a novel pattern with the unidirectional data flow to the world of single page applications, which has since been adopted by several frameworks and libraries.

### Exercises:

* read more about [React forms](https://reactjs.org/docs/forms.html)
* learn more about [different controlled components](https://github.com/the-road-to-learn-react/react-controlled-components-examples)

## Split Up Components

Now we have one large App component that keeps growing.  As this can eventually become confusing, we will begin to split it  into smaller components, creating separate components for search input and the items list.

{title="Code Playground",lang="javascript"}
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

You can pass properties among the components, which they can use themselves.  An App component needs to pass the properties managed in the local state and class methods.

{title="Code Playground",lang="javascript"}
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

You can define the components next to your App component, and as before, they will be ES6 class components.  The first one is the Search component:

{title="Code Playground",lang="javascript"}
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

The second is the table component:

{title="Code Playground",lang="javascript"}
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

Now you have three ES6 class components. Note that the `props` object is accessible via the class instance by using `this`. The props, or properties, have all the values you passed in your App component. That way, components can pass properties down the component tree.

By extracting those components from the App component, they have become reusable. Since components get their values by using the `props` object, you can pass different props to your components every time you use them elsewhere.

### Exercises:

* figure out further components that you could split up as you have done with the Search and Table components
  * but don't do it now, otherwise you will run into conflicts in the next chapters

## Composable Components

The `children` prop can be used pass elements that are unknown to the component itself. In this example, we pass a text string, as a child, to the search component.

{title="Code Playground",lang="javascript"}
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

Now the search component can destructure the `children` property from the `props` object, and then specify where it should be displayed.

{title="Code Playground",lang="javascript"}
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

The "Search" text should be visible next to your input field.  When you use the search component elsewhere, you can use different text, and other properties can be passed as children. You can also pass an element, or element trees (which can be encapsulated by components again), as children, so the children property makes it possible to weave components into each other.

### Exercises:

* read more about [the composition model of React](https://reactjs.org/docs/composition-vs-inheritance.html)

## Reusable Components

Reusable and composable components allow you to come up with capable component hierarchies. They are the foundation of React's view layer. We mentioned reusability last chapter, and now you will reuse the sable and search components. Even the App component is reusable, as you could instantiate it again elsewhere as much as you need to.

Let's define one more reusable component called a Button component, which we'll reuse often.

{title="Code Playground",lang="javascript"}
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

Here, we use a `Button` component instead of a `button` element, which only affects `type="button"`. Except for the type attribute, every other property must be defined to use `Button`.  This is for long-term consideration. Imagine there are several buttons in an application that require changes to attributes, style, or behavior. Without the component, every button would need to be refractored. Instead, the Button component allows only a  single source of truth, which refractors all the buttons at once.  One Button to rule them all.

Since we already have a button element, we can use the Button component instead. It omits the type attribute, because the Button component tells it to.

{title="Code Playground",lang="javascript"} 
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

The Button component expects a `className` property in `props`. The `className` attribute is another React derivate for the HTML attribute class.  We didn't pass any `className` information when the Button was used, so we'll some code to be more explicit that the `className` is optional:

{title="Code Playground",lang="javascript"} ~~~~~~~~
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

Now, whenever there is no `className` property specified when using the Button component, the value will be an empty string instead of `undefined`.

## Component Declarations

By now you should have four ES6 class components, so we'll move on to functional stateless components as alternative for ES6 class components. **Functional Stateless Components **are functions which that take an input and return an output. The inputs are the props, and the output is a component instance, or plain JSX, similar to an ES6 class component.  The difference is that functional stateless components are functions (functional) and they have no local state (stateless).

You cannot access or update the state with `this.state` or `this.setState()` because there is no `this` object or lifecycle methods.  We haven't covered lifecycle methods yet, but you've already used two of them: `constructor()` and `render()`.  The constructor runs only once in the lifetime of a component, but the `render()` class method runs every time the component updates.  Remember that functional stateless components have no lifecycle methods when you arrive at the lifecycle methods chapter later.


We covered **ES6 Class Components** in the previous four components.  E56 components extend from the React component. The `extend` hooks all lifecycle methods available in the React component API to the component. This is how we were able to use the `render()` class method. Remember can store and manipulate state in ES6 class components using `this.state` and `this.setState()`.

>NOTE: **React.createClass** is a component declaration that was used in older versions of React, which is still used in some JavaScript ES5 React applications.  [Facebook declared it as deprecated](https://reactjs.org/blog/2015/03/10/react-v0.13.html) in for JavaScript ES6. They even added a [deprecation warning in version 15.5](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html), so we will not use it in the book.

We're left with two component declarations--functional stateless and ES6 class components--without a clear way of when to use which.  My rule of thumb is to use functional stateless components when you don't need local state or component lifecycle methods. Usually, you start to implement your components as functional stateless components, but refactor it to an ES6 class component when access to the state or lifecycle methods is needed. In our application, we started the other way around for the sake of learning React.

Let's get back to your application. The App component uses local state, so it has to stay as an ES6 class component. The other three of your ES6 class components are stateless. They don't need access to `this.state` or `this.setState()`. Even more, they have no lifecycle methods.   

The Table and Button component refactoring remains our exercise, so we'll refactor the search component with a stateless functional component.

{title="Code Playground",lang="javascript"} 
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

The `props` are accessible in the function signature, and the return value is JSX, but you can do more with a functional stateless component. You already know the ES6 destructuring. The best practice is to use it in the function signature to destructure the `props`.

{title="Code Playground",lang="javascript"} 
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

Rememeber, ES6 arrow functions allow you to keep your functions concise. You can remove the block body of the function. In a concise body, an implicit return is attached, so you can remove the `return` statement. Since your functional stateless component is a function, you can keep it concise as well.

{title="Code Playground",lang="javascript"}
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

The last step was especially useful to enforce props as input and JSX as output. Still, you could *do something* in between by using a block body in your ES6 arrow function:

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

When using block bodies, people often do too many things in the function; by leaving it out, you can focus on just the input and output. Now you have one lightweight functional stateless component.  When you need access to its local component state or lifecycle methods, you would refactor it to an ES6 class component.We also saw how JavaScript ES6 can be useful in making React components more elegant.

### Exercises:

* refactor the Table and Button component to stateless functional components
* read more about [ES6 class components and functional stateless components](https://reactjs.org/docs/components-and-props.html)

## Styling Components

Now, we'll add basic styling to your application and components.  For this excercise, we will reuse the *src/App.css* and *src/index.css* files, which should be in project already since you bootstrapped it with *create-react-app*. They should be imported in your *src/App.js* and *src/index.js* files too. I have some CSS for you to copy and paste to these files, but feel free to use your own style if you feel comfortable with the language.

First, style your overall application:

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

Second, styling for components in the App file:

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

Don't forget to use React `className` instead of `class` as HTML attribute.  First, apply it in your App ES6 class component:

{title="Code Playground",lang="javascript"}
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

Second, apply it in your table functional stateless component:

{title="Code Playground",lang="javascript"} 
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

We have styled the application and components with basic CSS.  As you know, JSX mixes HTML and JavaScript; but one could argue that with these methods, CSS is also in that mix. This is called inline style, which lets you define JavaScript objects and pass them to the style attribute of an element.

Let's keep the table column width flexible using inline style:

{title="Code Playground",lang="javascript"}
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

The style is inlined now. You cab also define the style objects outside your elements to make it cleaner.

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

After that, you use them in the columns: `<span style={smallColumn}>`.

You will find varying opinions about style in React, but the pure CSS and inline style we've used is sufficient for this tutorial.  You can read more about these topics by following one of the links below.

* [styled-components](https://github.com/styled-components/styled-components)
* [CSS Modules](https://github.com/css-modules/css-modules)

{pagebreak}

You have learned the basics of writing a React application. Let's recap:

* React
  * use `this.state` and `setState()` to manage your local component state
  * pass functions or class methods to your element handler
  * use forms and events in React to add interactions
  * unidirectional data flow is an important concept in React
  * embrace controlled components
  * compose components with children and reusable components
  * usage and implementation of ES6 class components and functional stateless components
  * approaches to style your components
* ES6
  * functions that are bound to a class are class methods
  * destructuring of objects and arrays
  * default parameters
* General
  * higher-order functions

After you absorb what we've covered in this chapter, you can experiment with the source code of your application and see how the results change. You can find the source code in the [official repository](https://github.com/the-road-to-learn-react/hackernews-client/tree/5.2).


