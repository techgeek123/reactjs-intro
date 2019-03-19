# React Forms, Events, Handling events

## Learning Competencies

By the end of this topic you should be able to know

- What are React Forms
- Making Forms in React
- What are controlled and uncontrolled components, why to use controlled components in forms
- What are events and how to handle events in React
- Implementing forms and events in the app

## Overview

### Forms 

HTML form elements work a little bit differently from other DOM elements in React, because form elements naturally keep some internal state. For example, this form in plain HTML accepts a single name:

```html 
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

This form has the default HTML form behavior of browsing to a new page when the user submits the form. If you want this behavior in React, it just works. But in most cases, it’s convenient to have a JavaScript function that handles the submission of the form and has access to the data that the user entered into the form. The standard way to achieve this is with a technique called “controlled components”.

#### What are controlled and uncontrolled components

An uncontrolled component is a component that maintains its own internal state.A controlled component is a component that does not maintain any internal state. Since a controlled component has no state, it must be controlled by someone else.

### Controlled Components: 

In HTML, form elements such as `<input>, <textarea>, and <select>` typically maintain their own state and update it based on user input. In React, mutable state is typically kept in the state property of components, and only updated with setState().

We can combine the two by making the React state be the “single source of truth”. Then the React component that renders a form also controls what happens in that form on subsequent user input. An input form element whose value is controlled by React in this way is called a “controlled component”.

For example, if we want to make the previous example log the name when it is submitted, we can write the form as a controlled component:

```js
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}

```

Since the value attribute is set on our form element, the displayed value will always be `this.state.value`, making the React state the source of truth. Since handleChange runs on every keystroke to update the React state, the displayed value will update as the user types.

With a controlled component, every state mutation will have an associated handler function. This makes it straightforward to modify or validate user input. For example, if we wanted to enforce that names are written with all uppercase letters, we could write handleChange as:

```js
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()});
}
```

#### Simple Example

In example below we are setting input form with `value = {this.state.data}`. This allow us to update state whenever input value changes. We are using onChange event that will watch input changes and update state accordingly.

App.jsx
```js
import React from 'react';

class App extends React.Component {

   constructor(props) {
      super(props);
		
      this.state = {
         data: 'Initial data...'
      }

      this.updateState = this.updateState.bind(this);

   };

   updateState(e) {
      this.setState({data: e.target.value});
   }

   render() {
      return (
         <div>
            <input type = "text" value = {this.state.data} 
               onChange = {this.updateState} />
            <h4>{this.state.data}</h4>
         </div>
      );
   }
}

export default App;
```
main.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```
When input text value change, the state will update.

#### Complex example 

In this example we will show you how to use forms from child component. onChange method will trigger state update that will be passed to the child input value and rendered on screen. The similar example was shown in our events tutorial. Whenever you need to update state from child component you need to pass function that will handle updating (updateState) as a prop (updateStateProp).

App.jsx
```js
import React from 'react';

class App extends React.Component {

   constructor(props) {
      super(props);
		
      this.state = {
         data: 'Initial data...'
      }

      this.updateState = this.updateState.bind(this);
   };

   updateState(e) {
      this.setState({data: e.target.value});
   }

   render() {
      return (
         <div>
            <Content myDataProp = {this.state.data} 
               updateStateProp = {this.updateState}></Content>
         </div>
      );
   }
}

class Content extends React.Component {

   render() {
      return (
         <div>
            <input type = "text" value = {this.props.myDataProp} 
               onChange = {this.props.updateStateProp} />
            <h3>{this.props.myDataProp}</h3>
         </div>
      );
   }
}
export default App;
```

```js
main.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```

### Event handling

Handling events with React elements is very similar to handling events on DOM elements. There are some syntactic differences:
	1. React events are named using camelCase, rather than lowercase.
	2. With JSX you pass a function as the event handler, rather than a string.

For example, the HTML:

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```
is slightly different in React
```html
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

Another difference is that you cannot return false to prevent default behavior in React. You must call preventDefault explicitly. For example, with plain HTML, to prevent the default link behavior of opening a new page, you can write:

```html
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```
In React, this could instead be:
```html
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

#### Simple Example

This is a simple example where we will only use one component. We are just adding onClick event that will trigger updateState function once the button is clicked.

App.jsx
```js
import React from 'react';

class App extends React.Component {

   constructor(props) {
      super(props);
		
      this.state = {
         data: 'Initial data...'
      }

      this.updateState = this.updateState.bind(this);

   };

   updateState() {
      this.setState({data: 'Data updated...'})
   }

   render() {
      return (
         <div>
            <button onClick = {this.updateState}>CLICK</button>
            <h4>{this.state.data}</h4>
         </div>
      );
   }
}

export default App;
```
```js
main.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```

**NOTE** : You have to be careful about the meaning of `this` in JSX callbacks. In JavaScript, class methods are not bound by default. If you forget to bind `this.updateState` and pass it to onClick, `this` will be undefined when the function is actually called.

This is not React-specific behavior; it is a part of [how functions work in JavaScript](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/). Generally, if you refer to a method without () after it, such as onClick={this.updateState}, you should bind that method.

#### Child Events
When you need to update state of the parent component from its child, you can create event handler (updateState) in the parent component and pass it as a prop (updateStateProp) to your child component where you can just call it.

App.jsx
```js
import React from 'react';

class App extends React.Component {

   constructor(props) {
      super(props);
		
      this.state = {
         data: 'Initial data...'
      }

      this.updateState = this.updateState.bind(this);
   };

   updateState() {
      this.setState({data: 'Data updated from the child component...'})
   }

   render() {
      return (
         <div>
            <Content myDataProp = {this.state.data} 
               updateStateProp = {this.updateState}></Content>
         </div>
      );
   }
}

class Content extends React.Component {

   render() {
      return (
         <div>
            <button onClick = {this.props.updateStateProp}>CLICK</button>
            <h3>{this.props.myDataProp}</h3>
         </div>
      );
   }
}

export default App;
```
```js
main.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App.jsx';

ReactDOM.render(<App/>, document.getElementById('app'));
```

## Exploration

- You can learn more about forms from [here](http://lorenstewart.me/2016/10/31/react-js-forms-controlled-components/) and from [here](https://www.sitepoint.com/work-with-forms-in-react/)
- [Handling events](https://reactjs.org/docs/handling-events.html)