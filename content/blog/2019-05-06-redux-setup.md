---
title: "Basic Redux setup"
date: "2019-05-06"
tags: ["React", "Redux"]
---

Redux, boy. I don't know of anything that has a steeper learning curve. Root reducers? Store factories? `mapStateToProps`? Higher-order functions? Connected components? Action creators? Thunks? Dispatch? For someone who's feeling smug about getting a hang of basic React, it is a lot of moving parts to get a grip on. Anyways, here's a basic cheat-sheet for getting started.

First, a few imports:

```
m i redux react-redux redux-thunk
```

Next, create a store and include <a href="https://github.com/reduxjs/redux-thunk">Thunk</a> in `store.js`:

```
import { createStore, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'
import rootReducer from './reducers'

const store = createStore(
  rootReducer,
  applyMiddleware(thunk)
)

export default store

```

<!--more-->

We're importing a root reducer, so let's create that next. We'll start with one reducer, here I'm calling it albumsReducer `reducers/index.js`:

```
import { combineReducers } from 'redux'
import albumReducer from './albumReducer'

export default combineReducers({
  albumsReducer
})

```

Here's a starting point for `reducers/albumsReducer.js`:

```
export default function(state=[], action) {
  switch (action.type) {
    default:
      return state
  }
}

```

Finally we need to use Provider to connect this to our App component:

```
import React, { Component } from 'react'
import { Provider } from 'react-redux'
import store from './store'

class App extends Component {
  render() {
    return (
      <Provider store={store} >
        <div className="App">
        </div>
      </Provider>
    )
  }
}

export default App

```

Now we're ready to start creating action types and actions, and to bring our state into components with connect.
