# Basics in React

The chapter will guide you through the basics of React. It covers state and interactions in components - because static components are a bit dull, aren't they? Additionally you will learn about the options to declare a component and how to keep components composeable and reusable. Be prepared to breath life into your components.

## Internal Component State

Internal component state allows you to store, modify and delete properties of your component. The ES6 class component can use a constructor to initialize internal component state. The constructor is called only once when the component initializes. Don't worry about the constructor and ES6 class itself - we will cover these in later chapters.

Let's introduce a class constructor where you can set the initial internal component state.

{lang=javascript}
~~~~~~~~
class App extends Component {

# leanpub-start-insert
  constructor(props) {
    super(props);

    this.state = {
      list: list,
    };

  }
# leanpub-end-insert

  ...

}
~~~~~~~~

In your case the initial state is the list of items. Note that you have to call `super(props);` to call the constructor of the extended class. It's mandatory.

The state is bound to the class with the `this` object. You can access the state in your component - for instance in the `render()` method. Before you have mapped a static list of items. Now you are about to use the list of your internal component state.

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
# leanpub-start-insert
        { this.state.list.map(item =>
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

The list is part of the component now. It resides in the internal component state. You could add items, change items or remove items in and from your list. Every time you change your component state, the `render()` method of your component will run again. But be careful! Don't mutate the state directly, you have to use a method called `setState()` to modify your state. You will get to know it in a following chapter.

### Exercises:

* read more about [the ES6 class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor)

## ES6 Object Initializer

In JavaScript ES6 you can use a shorthand property syntax to initialize your objects more concise. Imagine the following object initialization:

{lang=javascript}
~~~~~~~~
const name = 'Robin';

const user = {
  name: name,
};
~~~~~~~~

When the property name in your object can be the same as your variable name, you can do the following:

{lang=javascript}
~~~~~~~~
const name = 'Robin';

const user = {
  name,
};
~~~~~~~~

In your application you can do the same. The list variable name and the state property name share the same name.

{lang=javascript}
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

Another neat helper are shorthand method names. In ES6 you can initialize methods in an object more concise.

{lang=javascript}
~~~~~~~~
// ES5
var userService = {
  getUserName: function (user) {
    return user.firstname + ' ' + user.lastname;
  },
};

// ES6
var userService = {
  getUserName(user) {
    return user.firstname + ' ' + user.lastname;
  },
};
~~~~~~~~

Last but not least you are allowed to use computed property names in ES6.

{lang=javascript}
~~~~~~~~
// ES5
var user = {
  name: 'Robin',
};

// ES6
var key = 'name';
var user = {
  [key]: 'Robin',
};
~~~~~~~~

Computed property names might make no sense yet. Why should you need a computed property name? But you will come to a point where you can use it.

### Exercises:

* read more about [ES6 object initializer](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer)

## Unidirectional Data Flow

Now you have some internal state in your App component. However you have not manipulated the internal state yet. The state is static and thus is the component. A good way to experience state manipulation is to have some component interaction.

Let's add a button in our displayed list of items. The button says "Dismiss" and will remove the item from the list. It could be useful onetime when you only want to keep a list of unread items.

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
        { this.state.list.map(item =>
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

As you can see the `onDismiss()` method gets enclosed by another function. Only that way you can sneak in the `objectID`. Otherwise you would have to define the function outside of the elements. However by using an arrow function you can inline it.

Note that elements with multiple attributes get messy as one line at some point. That's why the button is already used with multilines and indentation to keep it readable. But it is not mandatory. It is only a code style recommendation.

Now you have to implement the `onDismiss()` functionality. It takes an item id to identify the item to dismiss. The function is bound to the class and thus becomes a class method. You have to bind class methods in the constructor. Additionally you have to define its functionality in your class.

{lang=javascript}
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

Now you can define what happens inside of the class method. Since you want to remove the clicked item from your list, you can do that with the built-in array filter functionality. The filter function takes a function to evaluate each item in the list. If the evaluation for an item is true, the item stays in the list. Otherwise it will be removed. Additionally the function returns a new list and doesn't mutate the old list.

{lang=javascript}
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

You can do it more concise by using an arrow function again.

{lang=javascript}
~~~~~~~~
onDismiss(id) {
# leanpub-start-insert
  const isNotId = item => item.objectID !== id;
  const updatedList = this.state.list.filter(isNotId);
# leanpub-end-insert
}
~~~~~~~~

You could even inline it - like we did in the `onClick()` handler of the button - but it might get less readable.

{lang=javascript}
~~~~~~~~
onDismiss(id) {
# leanpub-start-insert
  const updatedList = this.state.list.filter(item => item.objectID !== id);
# leanpub-end-insert
}
~~~~~~~~

The list removes the clicked item now. However the state isn't updated yet. You can use the `setState()` class method to update the list in the internal component state.

{lang=javascript}
~~~~~~~~
onDismiss(id) {
  const isNotId = item => item.objectID !== id;
  const updatedList = this.state.list.filter(isNotId);
# leanpub-start-insert
  this.setState({ list: updatedList });
# leanpub-end-insert
}
~~~~~~~~

Now run again your app and try the "Dismiss" button. It should work. What you experience now is the **unidirectional data flow** in React. You trigger an action in your view - with `onClick()` - a function or class method modifies the internal component state and the `render()` method of the component runs again to update the view.

![Internal state update with unidirectional data flow](images/set-state-to-render-unidirectional.png)

### Exercises:

* read more about [the state and lifecycle in React](https://facebook.github.io/react/docs/state-and-lifecycle.html)

## Interactions with Forms and Events

Let's add another interaction to experience forms and events in React. The interaction is a search functionality. The input of the search field should be used to filter your list based on the title property of an item.

First you define your input field in your JSX.

{lang=javascript}
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
        { this.state.list.map(item =>
          ...
        )}
      </div>
    );
  }
}
~~~~~~~~

In the following scenario you will type into the field and filter the list temporary by the search term. To be able to filter the list, you need the value of the input field to update the state. But how do you access the value? You can use synthetic events in React to access the event payload.

Let's define an `onChange()` function for the input field.

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

  render() {
    return (
      <div className="App">
# leanpub-start-insert
        <form>
          <input
            type="text"
            onChange={this.onSearchChange}
          />
        </form>
# leanpub-end-insert
        ...
      </div>
    );
  }
}
~~~~~~~~

The function is bound to the component and thus a class method again. You have to bind and define the method.

{lang=javascript}
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

The method gives you access to the synthetic React event. The event has the value of the input field in its target object. Now you can manipulate the state for the search term:

{lang=javascript}
~~~~~~~~
class App extends Component {

  ...

# leanpub-start-insert
  onSearchChange(event) {
    this.setState({ searchTerm: event.target.value });
  }
# leanpub-end-insert

  ...
}
~~~~~~~~

Additionally you have to define the initial state for the `searchTerm` in the constructor.

{lang=javascript}
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

Now you store the input value to your internal component state every time the value in the input field changes. However the list doesn't update yet. You have to filter the list temporary based on the `searchTerm`. That's fairly simple. Before you map the list you can apply a filter on it. You have already used the built-in filter functionality.

{lang=javascript}
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
        { this.state.list.filter(...).map(item =>
# leanpub-end-insert
          ...
        )}
      </div>
    );
  }
}
~~~~~~~~

Let's approach the filter function in a different way now. We want to define the filter argument - the function - outside of our ES6 class component. There we don't have access to the state of the component - thus we have no access to the `searchTerm` property to evaluate the filter condition. We have to pass the `searchTerm` to the filter function and have to return a new function to evaluate the condition. That's called a higher order function.

Normally I wouldn't mention higher order functions, but in a React book it makes totally sense. It makes sense to know about higher order functions, because React deals with a pattern called higher order components. You will get to know the pattern later. Now again let's focus on the filter functionality.

First you have to define the higher order function outside of your class.

{lang=javascript}
~~~~~~~~
# leanpub-start-insert
function isSearched(searchTerm) {
  return function(item) {
    // some condition which returns true or false
  }
}
# leanpub-end-insert

class App extends Component {

  ...

}
~~~~~~~~

The function takes the `searchTerm` and returns another function which takes an item. The returned function will be used to filter the list based on the condition defined in the function.

Let's define the condition.

{lang=javascript}
~~~~~~~~
function isSearched(searchTerm) {
  return function(item) {
# leanpub-start-insert
    return !searchTerm || item.title.toLowerCase().includes(searchTerm.toLowerCase());
# leanpub-end-insert
  }
}

class App extends Component {

  ...

}
~~~~~~~~

The condition says multiple things. You filter the list only when a `searchTerm` is set. When a `searchTerm` is set, you match the incoming `searchTerm` pattern with the title of the item. You can do that with the built-in functionality includes. Only when the pattern matches you return true and the item stays in the list. But be careful with pattern matching: You shouldn't forget to lower case both strings. Otherwise there will be mismatches between a search term 'redux' and an item title 'Redux'.

One thing is left to mention: We cheated a bit by using the built-in includes functionality. It is already an ES6 feature. How would that look like in ES5? You would use the `indexOf()` function to get the index of the item in the list. When the item is in the list, `indexOf()` will return a positive index.

{lang=javascript}
~~~~~~~~
// ES5
string.indexOf(pattern) !== -1

// ES6
string.includes(pattern)
~~~~~~~~

Another neat refactoring can be done with an arrow function again. It makes the function more concise:

{lang=javascript}
~~~~~~~~
// ES5
function isSearched(searchTerm) {
  return function(item) {
    return !searchTerm || item.title.toLowerCase().includes(searchTerm.toLowerCase());
  }
}

// ES6
const isSearched = (searchTerm) => (item) =>
  !searchTerm || item.title.toLowerCase().includes(searchTerm.toLowerCase());
~~~~~~~~

One could argue which one is more readable. Personally I prefer the second one. The React ecosystem uses a lot of functional programming concepts. It happens often that you will use a function which returns a function (higher order functions). In ES6 you can express these more concise with arrow functions.

Last but not least you have to use the defined `isSearched()` function to filter your list.

{lang=javascript}
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
        { this.state.list.filter(isSearched(this.state.searchTerm)).map(item =>
# leanpub-end-insert
          ...
        )}
      </div>
    );
  }
}
~~~~~~~~

The search functionality should work now. Try it.

### Exercises:

* read more about [React events](https://facebook.github.io/react/docs/handling-events.html)
* read more about [higher order functions](https://en.wikipedia.org/wiki/Higher-order_function)

## ES6 Destructuring

There is a way in ES6 to access properties in objects and arrays easier. It's called destructuring. Compare the following snippet in ES5 and ES6.

{lang=javascript}
~~~~~~~~
const user = {
  firstname: 'Robin',
  lastname: 'Wieruch',
};

// ES5
var firstname = user.firstname;
var lastname = user.lastname;

// ES6
var { firstname, lastname } = user;

console.log(firstname + ' ' + lastname);
// output: Robin Wieruch
~~~~~~~~

While you have to add an extra line each time you want to access an object property in ES5, you can do it in one line in ES6. Additionally you don't have to have duplicated property names. A best practice for readability is to use multilines when you destructure an object into multiple properties.

{lang=javascript}
~~~~~~~~
var {
  firstname,
  lastname
} = user;
~~~~~~~~

The same goes for arrays. You can destructure them too, but keep it more readable with multilines.

{lang=javascript}
~~~~~~~~
var users = ['Robin', 'Andrew', 'Dan'];
var [
  userOne,
  userTwo,
  userThree
] = users;

console.log(userOne, userTwo, userThree);
// output: Robin Andrew Dan
~~~~~~~~

Perhaps you have noticed that the state in the App component can be destructure the same way. You can keep the list filter and map line of code more concise.

{lang=javascript}
~~~~~~~~
  render() {
# leanpub-start-insert
    const { searchTerm, list } = this.state;
# leanpub-end-insert
    return (
      <div className="App">
        ...
# leanpub-start-insert
        { list.filter(isSearched(searchTerm)).map(item =>
# leanpub-end-insert
          ...
        )}
      </div>
    );
~~~~~~~~

Again you could do it the ES5 or ES6 way:

{lang=javascript}
~~~~~~~~
// ES5
var searchTerm = this.state.searchTerm;
var list = this.state.list;

// ES6
var { searchTerm, list } = this.state;
~~~~~~~~

But since the book uses ES6 most of the time, you should stick to ES6.

### Exercises:

* read more about [ES6 destructuring](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

## Controlled Components

You already learned about the unidirectional data flow. The same law applies for the input field, which updates the state that in turn filters the list. The state was changed, the `render()` method runs again and uses the recent `searchTerm` state to apply the filter condition.

But didn't we forget something in the input element? A HTML input tag comes with a `value` attribute. The value attribute usually has the value that is shown in the input field - in our case the `searchTerm` property. However it seems like we don't need that in React.

That's wrong. Form elements such as <input>, <textarea> and <select> hold their own state. They modify the value internally once someone changes it from the outside. In React that's called an **uncontrolled component**, because it handles its own state. In React you should make sure to make those elements **controlled components**.

How to do that? You only have to set the value attribute of the input field. The value is already saved in the `searchTerm` state property.

{lang=javascript}
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

The unidirectional data flow cycle for the input field is self-contained now. The internal component state is the single source of truth for the input field.

The whole internal state management and unidirectional data flow might be new to you. But once you are used to it, it will be your natural flow to implement things in React. In general React brought a novel pattern with the unidirectional data flow. It gets adopted by several frameworks and libraries.

### Exercises:

* read more about [React forms](https://facebook.github.io/react/docs/forms.html)

## Breakup Your Components

You have one large App component. It keeps growing and gets confusing eventually. You can start to split it up into chunks - smaller components.

Let's start to use a component for the search input and a component for the list of items.

{lang=javascript}
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

You can pass those components properties which they can use themselves.

{lang=javascript}
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

{lang=javascript}
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

{lang=javascript}
~~~~~~~~
...

# leanpub-start-insert
class Table extends Component {
  render() {
    const { list, pattern, onDismiss } = this.props;
    return (
      <div>
        { list.filter(isSearched(pattern)).map(item =>
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

Now you have three ES6 class components. Perhaps you have seen the `this.props` object. The props - short form for properties - have all the values you have passed to the components when you used them in your App component. You could reuse these components somewhere else but pass them different values. They are reusable.

### Exercises:

* figure out which components you could split up
  * but don't do it, otherwise you will run into conflicts in the next chapters

## Composeable Components

There is one more little property which is accessible in the props object - the `children` prop. You can use it to pass elements to your components from above - which are unknown to the component itself - but make it possible to compose components into each other. Let's see how this looks like when you only pass text as child to the Search component.

{lang=javascript}
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

Now the Search component can destructure the children property from props. Then it can specify where the children should be displayed.

{lang=javascript}
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

The "Search" text should be visible next to your input field now. When you use the Search component somewhere else, you can choose a different text if you like. After all it is not only text that you can pass as children. You can pass an element and element trees (which can be encapsulated by components again) as children. The children property makes it possible to weave components into each other.

### Exercises:

* read more about [the composition model of React](https://facebook.github.io/react/docs/composition-vs-inheritance.html)

## Reusable Components

Reusable and composeable components empower you to come up with capable component hierarchies. They are the foundation of your view layer. The last chapters mentioned often the term reusability. You can reuse the Table and Search components already. Not to forget the App component.

Let's define one more reusable component - a Button component - which gets reused more often eventually.

{lang=javascript}
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

It might seem redundant to declare such a component. You will use a `Button` instead of a `button`. It only spares the `type="button"`. Except for the type attribute you have to define everything else when you want to use the Button component. But you have to think about the long term investment here. Imagine you have several buttons in your app, but want to change an attribute, style or behavior for the button. Without the component you would have to refactor every button. Instead the Button component ensures to have only one single source of truth. One Button to refactor all buttons at once.

Since you already have a button element, you can use the Button component instead. It omits the type attribute.

{lang=javascript}
~~~~~~~~
class Table extends Component {
  render() {
    const { list, pattern, onDismiss } = this.props;
    return (
      <div>
        { list.filter(isSearched(pattern)).map(item =>
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

The Button component expects a className property in the props. But we didn't pass any className when the Button was used. It should be more explicit in the Button component that the className is optional. Thus you can use an ES6 default parameter.

{lang=javascript}
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

Now whenever there is no `className` property the value will be an empty string.

### Exercises:

* read more about [ES6 default parameters](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Default_parameters)

## Component Declarations

By now you have four ES6 class components. But you can do better. Let me introduce functional stateless components as replacements for ES6 class components. Before you will refactor your components, let's introduce the three different types of components.

* Functional Stateless Components: These components are functions which get an input and return an output. The input is state (properties). The output is a component instance. Same as in an ES6 class component. Functional stateless components are functions (functional) and they have no internal state (stateless). You cannot access the state with `this.state` because there is no `this` object. Additionally they have no lifecycle methods. You didn't learn about lifecycle methods yet, but you already used two: `constructor()` and `render()`. Keep this in mind, when you arrive at the lifecycle methods chapter later on.

* ES6 Class Components: You already used these type of components. In the class definition they extend from a React component. The `extend` hooks all the lifecycle methods - available in the React component API - to the component. As mentioned you already used two of them. Additionally you can store and manipulate state in ES6 class components.

* React.createClass: The component declaration was used in older versions of React and still in ES5 React applications. But [Facebook declared it as deprecated](https://facebook.github.io/react/blog/2015/03/10/react-v0.13.html) in favor of ES6.

But when to use functional stateless components or ES6 class components? A rule of thumb is to use functional stateless components when you don't need internal component state or component lifecycle methods. Usually you start to implement your components as functional stateless components. Once you need access to the state or lifecycle methods, you have to refactor it to an ES6 class component.

Let's get back to our application. The App component uses internal state. That's why it has to stay as an ES6 class component. But the other three of your ES6 class components are stateless without lifecycle methods. Let's refactor the Search component to a stateless functional component. The Table and Button component refactoring will remain as your exercise.

{lang=javascript}
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

You already know about the props destructuring. The best practice is to use it in the function signature in the first place.

{lang=javascript}
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

But it can get better. You know that arrow functions allow you to keep your functions concise. Since your functional stateless component is a function, you can keep it concise as well.

{lang=javascript}
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

The last step was especially useful to enforce only to have props as input and an element as output. Nothing in between. Still you could do something in between by using a block body in your arrow function.

{lang=javascript}
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

Now you have one lightweight functional stateless component. Once you would need access to its internal component state or lifecycle methods, you would refactor it to an ES6 class component.

### Exercises:

* refactor the Table and Button component to stateless functional components
* read more about [ES6 class components and functional stateless components](https://facebook.github.io/react/docs/components-and-props.html)

## Styling Components

Let's add some basic styling to your app and components. You can reuse the *src/App.css* and *src/index.css* files. I prepared some CSS to copy and paste, but feel free to use your own style.

*src/index.css*

{lang=css,linenos=off}
~~~~~~~~
body {
  color: #222;
  background: #f4f4f4;
  font: 400 14px CoreSans, Arial,sans-serif;
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

*src/App.css*

{lang=css,linenos=off}
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

Now you can use the style in your some of your components. Don't forget to use React `className` instead of `class` as attribute.

First apply it in your App ES6 class component.

{lang=javascript}
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

Second apply it in your Table functional stateless component.

{lang=javascript}
~~~~~~~~
const Table = ({ list, pattern, onDismiss }) =>
# leanpub-start-insert
  <div className="table">
# leanpub-end-insert
    { list.filter(isSearched(pattern)).map(item =>
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

Now you have styled your app and components with basic CSS. It should look decent. As you know JSX is mixed up HTML and JavaScript. One could argue to add CSS in the mixup as well. That's called inline style. You can define JavaScript objects and pass them to the style attribute of an element.

Let's keep the Table column width flexible by using inline style.

{lang=javascript}
~~~~~~~~
const Table = ({ list, pattern, onDismiss }) =>
  <div className="table">
    { list.filter(isSearched(pattern)).map(item =>
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

It is really inlined now. You could define the style objects outside of your elements to make it cleaner.

{lang=javascript}
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

After that you could use it in your columns: `<span style={smallColumn}>`.

In general you will find different opinions and solutions for style in React. You used pure CSS and Inline Style now. It's sufficient to get started.

I don't want to be opinionated here, but I want to leave you some more options. You can read about them and apply them on your own. I'm open to your thoughts about the options as well.

* [radium](https://github.com/FormidableLabs/radium)
* [aphrodite](https://github.com/khan/aphrodite)
* [styled-components](https://github.com/styled-components/styled-components)
* [CSS Modules](https://github.com/css-modules/css-modules)

The chapter might get overhauled in the future to give you an opinionated approach.

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

const isSearched = (searchTerm) => (item) =>
  !searchTerm || item.title.toLowerCase().includes(searchTerm.toLowerCase());

class App extends Component {

  constructor(props) {
    super(props);

    this.state = {
      list,
      searchTerm: '',
    };

    this.onSearchChange = this.onSearchChange.bind(this);
    this.onDismiss = this.onDismiss.bind(this);
  }

  onSearchChange(event) {
    this.setState({ searchTerm: event.target.value });
  }

  onDismiss(id) {
    const isNotId = item => item.objectID !== id;
    const updatedList = this.state.list.filter(isNotId);
    this.setState({ list: updatedList });
  }

  render() {
    const { searchTerm, list } = this.state;
    return (
      <div className="page">
        <div className="interactions">
          <Search
            value={searchTerm}
            onChange={this.onSearchChange}
          >
            Search
          </Search>
        </div>
        <Table
          list={list}
          pattern={searchTerm}
          onDismiss={this.onDismiss}
        />
      </div>
    );
  }
}


const Search = ({ value, onChange, children }) =>
  <form>
    {children} <input
      type="text"
      value={value}
      onChange={onChange}
    />
  </form>

const Table = ({ list, pattern, onDismiss }) =>
  <div className="table">
    { list.filter(isSearched(pattern)).map(item =>
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

const Button = ({ onClick, className = '', children }) =>
  <button
    onClick={onClick}
    className={className}
    type="button"
  >
    {children}
  </button>

export default App;
~~~~~~~~

You have learned the basics to write your own React app! Let's recap the last chapters:

* React
  * this.state and setState to manage your internal component state
  * forms and events in React
  * unidirectional data flow
  * compose components with children and reusable components
  * usage and implementation of ES6 class components and functional stateless components
  * approaches to style your components
* ES6
  * arrow functions with block and concise bodies to shorten your function declarations
  * functions that are bound to a class are class methods
  * destructuring of objects and arrays
  * default parameters
* General
  * higher order functions

Again it makes sense to take a break. Internalize the learnings and apply them on your own. You can experiment with the source code you have written so far. Additionally you can read more in the official [documentation](https://facebook.github.io/react/docs/installation.html).
