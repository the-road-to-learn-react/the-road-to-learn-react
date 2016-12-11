*src/App.js*

{lang=javascript,style=monokai,hl_lines="1 3"}

{lang=javascript}
~~~~~~~~
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

# leanpub-start-insert
class App extends Component {
  render() {
# leanpub-end-insert
    const helloWorld = 'Welcome to React';
    return (
      <div className="App">
        <div className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h2>{helloWorld}</h2>
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

headers:
#
##

divider to {pagebreak}

{pagebreak} after each header

a_blank