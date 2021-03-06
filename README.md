# RJSDemo5-Redux

# Steps for branches: "starting-point" to "with-redux"

1. Demonstrate the counter app and explain that the counter is being
2. Explain what a store is, and how it would change the structure of our app. Instead of internal states, we would have the app and its corresponding components connect to central store.
3. Show and explain the [Redux flow diagram](https://warehouse.joincoded.com/workshop/redux/intro-to-redux/the-redux-flow/).
4. `yarn add redux` and `yarn add react-redux`

# Adding Redux to the Project

5. In`index.js`, set up the App for redux:

```javascript
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import registerServiceWorker from "./registerServiceWorker";

import { createStore } from "redux"; //STEP 1
import { Provider } from "react-redux"; // STEP 2

const store = createStore(()=>{}); //STEP 3 
// createStore takes an empty function for now (for demoing purposes), but will be replaced with the actual reducer later

//STEP 4
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
registerServiceWorker();
```

# Actions

6. Create `store` folder. In store, create `actions.js`
7. In `stores/action.js`:

```javascript

export const increment = () => {
  alert("Increment");
};

export const decrement = () => {
  alert("Decrement");
};
```

# Actions - Wiring

8.  Time to map and dispatch actions! In `Counter.js` (mapDispatchToProps goes AFTER the class delcaration):

```javascript

import * as actionCreators from '../../store/actions';
import {connect} from react-redux;
....
const mapDispatchToProps = dispatch => {
  return {
    onIncrementCounter: () => dispatch(actionCreators.increment()),
    onDecrementCounter: () => dispatch(actionCreators.decrement()),
  };
};

export default connect(null, mapDispatchToProps)(Counter); //null because the first argument takes a mapDispatchToState ... which we do not have yet. 

```

# Actions - Dispatching

9. Now that our actions are wired, let us dispatch:

```javascript
<button onClick={this.props.onIncrementCounter}>Increment</button>
<button onClick={this.props.onDecrementCounter}>Decrement</button>
```

# Actions - Modifying

10. In `stores/action.js` let the functions return objects that specify the type of the actions:

```javascript
const INCREMENT = "INCREMENT";
const DECREMENT = "DECREMENT";

export const increment = () => {
  return {
    type: INCREMENT
  };
};

export const decrement = () => {
  return {
    type: DECREMENT
  };
};
```

# Reducers

11. Create `stores/reducers.js` and inside that:

```javascript
const initialState = {
  copyMe: "Remember to copy me!",
  counter: 0
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case "INCREMENT":
      return {
        ...state,
        counter: state.counter + 1 //copy all of the old state, and only manipulate the part we want to manipulate.
      };
    case "DECREMENT":
      return {
        ...state,
        counter: state.counter - 1
      };
    default:
      return state;
  }
};

export default reducer;
```

# Adding The Reducer to the Project

12. In`index.js`, set up the App for redux:

```javascript
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import registerServiceWorker from "./registerServiceWorker";

import { createStore } from "redux"; 
import { Provider } from "react-redux"; 
import reducer from "./store/reducer"; //STEP 5

const store = createStore(reducer); //STEP 6


ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
registerServiceWorker();
```

# Connect Component to Central Store

13. In `Counter.js`:

```javascript

import {connect} from react-redux;

...

const mapStateToProps = state => {
  return {
    ctr: state.counter,
  }
};

...

export default connect(mapStateToProps)(Counter);
```

Now our component has access to the "counter" variable in our global store. The component can access the counter variable via its props.

`{this.props.ctr}`

Change the initial value of the counter state to show that we are not using the internal state anymore. 

# The Importance of Returning a Copying State

14. In `Counter.js`: 

```
javascript

const mapStateToProps = state => {
  return {
    ctr: state.counter,
    copyMe: state.copyMe
  }
};
```

Now our component has access to the "copyMe" variable in our global store. The component can access the counter variable via its props.

`{this.props.copyMe}`

15. After displaying it on the screen, remove the spread operator from the decrement `reducer.js` to show that if we do not copy the older state, the key:value pair get overwritten since they were not included in the new state. 

```
javascript
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case "INCREMENT":
      return {
        ...state,
        counter: state.counter + 1
      };
    case "DECREMENT":
      return {
        counter: state.counter - 1
      };

    default:
      return state;
  }
};
```



# STOP HERE. GIVE THEM THE CORRESPONDING TASK. After demonstrating the solution, come back to explaining the below concepts before moving on to advanced-redux. 
# Using Multiple Reducers

16. Add a new "Log" button that increments it's own counter, `logCounter`. In `reducer.js`:

```javascript
const initialState = {
  copyMe: "Remember to copy me!",
  counter: 0,
  logCounter: 0
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case "INCREMENT":
      return {
        ...state,
        counter: state.counter + 1 //copy all of the old state, and only manipulate the part we want to manipulate.
      };
    case "DECREMENT":
      return {
        ...state,
        counter: state.counter - 1
      };
    case "LOG":
      const logCounter = state.logCounter + 1;
      return {
        ...state,
        logCounter: logCounter
      };
    default:
      return state;
  }
};

export default reducer;
```

17. Seperate reducers by creating a `reducers` folder with `counter.js` and `log.js` inside. Now, inside `store/reducers/counter.js`:

```javascript
const initialState = {
  copyMe: "Remember to copy me!",
  counter: 0
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case "INCREMENT":
      return {
        ...state,
        counter: state.counter + 1 //copy all of the old state, and only manipulate the part we want to manipulate.
      };
    case "DECREMENT":
      return {
        ...state,
        counter: state.counter - 1
      };
    default:
      return state;
  }
};

export default reducer;
```

Inside `store/reducers/log.js`:

```javascript
const initialState = {
  copyMe2: "Remember to copy me too!",
  logCounter: 0
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case "LOG":
      const logCounter = state.logCounter + 1;
      return {
        ...state,
        logCounter: logCounter
      };
    default:
      return state;
  }
};
export default reducer;
```

18. Now, in `index.js`, combine the reducers:

```javascript
import { Provider } from "react-redux";

import { createStore, combineReducers } from "redux"; //STEP 1
import counterReducer from "./store/reducers/counter"; //STEP 2
import logReducer from "./store/reducers/log"; // STEP 2

//STEP 3
const rootReducer = combineReducers({
  rootCtr: counterReducer,
  rootLog: logReducer
});

const store = createStore(rootReducer); //STEP 4

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById("root")
);
registerServiceWorker();
```

19. Back in `Counter.js`, remap the `mapStateToProps`:

```javascript
const mapStateToProps = state => {
  return {
    ctr: state.rootCtr.counter,
    log: state.rootLog.logCounter
  };
};
```
# End of "starting-point" branch

# Switch to "with-redux" branch to demo Advanced Redux
