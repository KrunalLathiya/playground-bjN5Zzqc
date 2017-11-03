# Simple Redux Create Delete Contact Application

Simple Redux Create Delete Contact Application is today’s small project. We will use <b>ReactJS</b> as a frontend. We are not using any backend in this tutorial, so a just client-side application. Redux is the most famous library to manage the state at the client side, and it is trendy among React community. We will build a Simple Contact Create Delete Application. In that user can add a new contact and it will display as a list and user can also delete any contact.

First, we need to download the create-react-app globally on the PC and then we can begin our project.

# Step 1: Do one ReactJS Project.

Type the following command one by one.

```
npm install -g create-react-app
create-react-app redux-contact-app
```

It will make some boilerplate for our application and also install all the dependencies for our project. Still, we need to establish some other dependencies as well.

```
npm install --save redux react-redux
```
So, this installs redux and react-redux dependencies.

# Step 2: We need to configure the Redux Store.

First, we need to make three folders inside <b>src</b> directory.
1. actions
2. reducers
3. store

To set the store, we need to create one file inside store folder called <b>configureStore.js</b>.

```javascript
// configureStore.js

import { createStore } from 'redux';

export default function configureStore(initialState) {
    return createStore();
}
```
Also, we need to wrap the Provider component Outside our <b>App.js</b> component in the <b>index.js</b> file.

```javascript
// index.js

import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import { Provider } from 'react-redux';

import registerServiceWorker from './registerServiceWorker';

ReactDOM.render(
<Provider>
    <App />
</Provider>, document.getElementById('root'));
registerServiceWorker();
```
The react-redux library provides us Provider, which wraps the App component and make the available store to the context of our application. 

# Step 3: We need to make one form to add the contact name.

Go to the <b>App.js</b> file and put the following code in it.

```javascript
// App.js

import React, { Component } from 'react';

class App extends Component {

  constructor(props){
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.state = {
      name: ''
    }
  }

  handleChange(e){
    this.setState({
      name: e.target.value
    })
  }

  handleSubmit(e){
    e.preventDefault();
    console.log(this.state.name);
  }

  render() {
    let name;
    return(
      <div>
        <h1>Clientside Contacts Application</h1>
        <hr />
        {/* <ul>
          {this.props.contacts.map((contact, i) => <li key={i}>{contact.name}</li> )}
        </ul> */}
        <div>
          <h3>Add Contact Form</h3>
          <form onSubmit={this.handleSubmit}>
            <input type="text" onChange={this.handleChange} />
            <input type="submit" />
          </form>
        </div>
      </div>
    )
  }
}

export default App;
```
So, it will create a form and when we submit the data, the data logs in the console.
<b>
    Now, if we need to change the add the new contact in the array of contacts or modify it, we do not do it directly. We need to dispatch the actions and that actions call the reducer and reducer returns a new state of the application.
</b>

# Step 4: Make actionType.js constants file.

We need to make one file called actionType.js inside the actions folder.

```javascript
// actionTypes.js

export const GET_ALL_CONTACTS = 'GET_ALL_CONTACTS';
export const CREATE_NEW_CONTACT = 'CREATE_NEW_CONTACT';
```
This file exports our actionType for every action object. So every time, if we want to dispatch any actions then we access the actions quickly to avoid any typos in action types.

# Step 5: Make one action object for creating contact.

Inside actions folder, make one file called <b>contactAction.js</b>.

```javascript
// contactAction.js

import * as actionTypes from './actionTypes';

export const createContact = (contact) => {
    return {
      type: actionTypes.CREATE_NEW_CONTACT,
      contact: contact
    }
  };
```
<b>createContact()</b> function returns an object that describes two things.

1. action type
2. payload

# Step 6: Make one reducer inside reducers folder.

Reducers are used to update the state object in your store. Just like actions, your application can have multiple reducers.

```javascript
// contactReducer.js

import * as actionTypes from '../actions/actionTypes';

export default (state = [], action) => {
    switch (action.type){
      
      case actionTypes.CREATE_NEW_CONTACT:
      return [
        ...state,
        Object.assign({}, action.contact)
      ];
      default:
            return state;
    }
  };
```
Here, one thing to note that, we have not mutated the state directly instead we have returned a new state.

So, the new state is original state array + new contact data and returns a new collection.

# Step 7: Create combineReducer function.

Inside <b>reducers</b> folder, make one file called <b>index.js</b> file.

```javascript
// index.js

import { combineReducers } from 'redux';
import contacts from './contactReducer';

export default combineReducers({
    contacts: contacts
});
```
Now, update the <b>configureStore</b> function and pass the <b>rootReducer</b> to it.

```javascript
// configureStore.js

import {createStore} from 'redux';
import rootReducer from '../reducers';

export default function configureStore(initialState) {
  return createStore(rootReducer, initialState);
}
```
Also, we need to pass the store in our application. So we need also to update the <b>index.js</b> file.

```javascript
// index.js

import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import { Provider } from 'react-redux';
import registerServiceWorker from './registerServiceWorker';
import configureStore from './store/configureStore';

const store = configureStore();

ReactDOM.render(
<Provider store={store}>
    <App />
</Provider>, document.getElementById('root'));
registerServiceWorker();
```

# Step 8: We need to connect this store to the App.js component.

If we want to access all the contacts data from the store, then we must the store state as props to the react components. 

<b>Connect()</b> function bridge the gap between store and components and provide a way to pass state as props to display data or dispatch any actions to the Redux store.

```javascript
// App.js

import React, { Component } from 'react';
import { connect } from 'react-redux';
import * as contactAction from './actions/contactAction';

class App extends Component {

  constructor(props){
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
    this.state = {
      name: ''
    }
  }

  handleChange(e){
    this.setState({
      name: e.target.value
    })
  }

  handleSubmit(e){
    e.preventDefault();
    let contact = {
      name: this.state.name
    }
    this.props.createContact(contact);
  }

  render() {

    return(
      <div>
        <h1>Clientside Contacts Application</h1>
        <hr />
        { <ul>
          {this.props.contacts.map((contact, i) => <li key={i}>{contact.name}</li> )}
        </ul> }
        <div>
          <h3>Add Contact Form</h3>
          <form onSubmit={this.handleSubmit}>
            <input type="text" onChange={this.handleChange} />
            <input type="submit" />
          </form>
        </div>
      </div>
    )
  }
}

const mapStateToProps = (state, ownProps) => {
  return {
    contacts: state.contacts
  }
};

const mapDispatchToProps = (dispatch) => {
  return {
    createContact: contact => dispatch(contactAction.createContact(contact))
  }
};

export default connect(mapStateToProps, mapDispatchToProps)(App);
```
Now, we can add the contacts, and it will list down the contact one by one from the store.

# Step 9: Make an action to remove the contact list and style the project.

First, update the <b>actionType.js</b> file and add new action type.

```javascript
// actionType.js

export const GET_ALL_CONTACTS = 'GET_ALL_CONTACTS';
export const CREATE_NEW_CONTACT = 'CREATE_NEW_CONTACT';
export const REMOVE_CONTACT = 'REMOVE_CONTACT';
```
We need to make an action described object in the <b>contactAction.js</b> file.

```javascript
// contactAction.js

import * as actionTypes from './actionTypes';

export const createContact = (contact) => {
    return {
      type: actionTypes.CREATE_NEW_CONTACT,
      contact: contact
    }
  };

export const deleteContact = (id) => {
    return {
        type: actionTypes.REMOVE_CONTACT,
        id: id
    }
}
```
Now, add a new case in the <b>contactReducer.js</b> file to delete the contact.

```javascript
// contactReducer.js

import * as actionTypes from '../actions/actionTypes';

export default (state = [], action) => {
    switch (action.type){
      case actionTypes.CREATE_NEW_CONTACT:
      return [
        ...state,
        Object.assign({}, action.contact)
      ];
      case actionTypes.REMOVE_CONTACT:
      return state.filter((data, i) => i !== action.id);
      default:
            return state;
    }
  };
```
In here, I have used the <b>ES6 Filter</b> function to remove the item list match with the id we just pass as a payload.

Finally, our <b>App.js</b> file looks like this with bootstrap styles.

```javascript
// App.js

import React, { Component } from 'react';
import { connect } from 'react-redux';
import * as contactAction from './actions/contactAction';

class App extends Component {

  constructor(props){
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
     
    this.state = {
      name: ''
    }
  }

  handleChange(e){
    this.setState({
      name: e.target.value
    })
  }

  handleSubmit(e){
    e.preventDefault();
    let contact = {
      name: this.state.name
    }
    this.setState({
      name: ''
    });
    this.props.createContact(contact);
  }

  listView(data, index){
    return (
      <div className="row">
        <div className="col-md-10">
          <li key={index} className="list-group-item clearfix">
            {data.name}
          </li>
        </div>
        <div className="col-md-2">
          <button onClick={(e) => this.deleteContact(e, index)} className="btn btn-danger">
            Remove
          </button>
        </div>
    </div> 
    )
  }

  deleteContact(e, index){
    e.preventDefault();
    this.props.deleteContact(index);
  }

  render() {

    return(
      <div className="container">
        <h1>Clientside Contacts Application</h1>
        <hr />
        <div>
          <h3>Add Contact Form</h3>
          <form onSubmit={this.handleSubmit}>
            <input type="text" onChange={this.handleChange} className="form-control" value={this.state.name}/><br />
            <input type="submit" className="btn btn-success" value="ADD"/>
          </form>
          <hr />
        { <ul className="list-group">
          {this.props.contacts.map((contact, i) => this.listView(contact, i))}
        </ul> }
        </div>
      </div>
    )
  }
}

const mapStateToProps = (state, ownProps) => {
  return {
    contacts: state.contacts
  }
};

const mapDispatchToProps = (dispatch) => {
  return {
    createContact: contact => dispatch(contactAction.createContact(contact)),
    deleteContact: index =>dispatch(contactAction.deleteContact(index))
  }
};

export default connect(mapStateToProps, mapDispatchToProps)(App);
```
So, our Application looks like below.

@[Simple Redux Create Delete Contact Application]({"stubs": ["src/app/App.js", "src/main.js", "src/app/actions/actionTypes.js","src/app/actions/contactAction.js", "src/app/reducers/contactReducer.js","src/app/reducers/index.js", "src/app/store/configureStore.js", "src/index.tpl.html"], "command": "./run.sh"})
