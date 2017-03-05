# Final Words

That was the last chapter of the book. I hope you liked it so far. Share it with your friends, it would mean a lot to me.

**But where can you go from here?** You can either extend the application on your own or dive into your own React project. Before you dive into another book or tutorial, you should create your own hands-on React project. Do it for one week, release it somewhere and reach out to me on [Twitter](https://twitter.com/rwieruch). I am curious what you will build after you have read the book. You can also find me on [GitHub](https://github.com/rwieruch) to share your repository.

If you are looking for further extensions for your application, I can recommend several learning paths:

* Code Organization: On your way reading the book you came across one chapter about Code Organization. You could apply these changes now, if you haven't done it yet. It will organize your components in structured files and folders (modules). In addition it helps to understand and learn the principles of code splitting, reusability, maintainability and module API design.
* Asynchronous Requests: You can substitute the native fetch API with third party alternatives to perform asynchronous requests: [superagent](https://github.com/visionmedia/superagent) or [axios](https://github.com/mzabriskie/axios). There is no perfect solution to make asynchronous requests. But by exchanging the buildings blocks around React, you make the experience how powerful it can be to have this flexibility. In frameworks you stick to one solution, in a flexible ecosystem like React you can exchange the solutions.
* Type Checking: In one chapter you have used React PropTypes to define component interfaces. It is a good practice to prevent bugs. But the PropTypes are only checked on runtime. You can go one step further to introduce static type checking in your editor. [TypeScript](https://www.typescriptlang.org/) is one popular approach. But in the React ecosystem people prefer to use [Flow](https://flowtype.org/). I can recommend to give Flow a shot.
* Tooling with Webpack and Babel: You have setup your application with *create-react-app*. At some point, when you learned React, you want to learn the tooling around it to setup your own project without *create-react-app*. I can recommend to follow a minimal setup with [Webpack and Babel](https://www.robinwieruch.de/minimal-react-webpack-babel-setup/). Afterward you could [setup ESLint](https://www.robinwieruch.de/react-eslint-webpack-babel/) to follow a unified code style in your application.
* State Management: You have used React `setState` to manage internal component state. That's a perfect start. However, in a scaling application you will experience the limits of internal component state. Therefore you have third party state management libraries like Redux or MobX.
* Routing: You can implement routing for your application with [react-router](https://github.com/ReactTraining/react-router). So far you only have one page in your application. React Router helps you to have multiple pages across different URLs.

**More Readings:**

* [The Minimal React Webpack Babel Setup](https://www.robinwieruch.de/minimal-react-webpack-babel-setup/)
* [Reasons why I moved from Angular to React](https://www.robinwieruch.de/reasons-why-i-moved-from-angular-to-react/)
* [Tips to learn React + Redux](https://www.robinwieruch.de/tips-to-learn-react-redux/)
* [All the Conditional Renderings in React](https://www.robinwieruch.de/conditional-rendering-react/)
* [Redux or MobX: An attempt to dissolve the Confusion](https://www.robinwieruch.de/redux-mobx-confusion/)
* [SoundCloud Client in React + Redux](https://www.robinwieruch.de/the-soundcloud-client-in-react-redux) with [Extensions like ESLint, Flow, Normalizr, MobX and Observables](https://github.com/rwieruch/react-redux-soundcloud)

**More Resources:**

* A real SoundCloud Client in React + Redux: [Live](http://www.favesound.de/) & [Source](https://github.com/rwieruch/favesound-redux)
* A real SoundCloud Client in React + MobX: [Source](https://github.com/rwieruch/favesound-mobx)

In general I invite you to visit my website [www.robinwieruch.de](https://www.robinwieruch.de/) to find more interesting topics. You can [subscribe](https://www.getrevue.co/profile/rwieruch) to my Newsletter to get content updates.

If you liked the book, I hope you will share it with your friends. I would really appreciate it. Thanks for reading - Robin.
