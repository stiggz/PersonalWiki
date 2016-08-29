## Reducers [Back](./../react_redux.md)

Reducers are used to describe how state changes in an application.

`Reducer<S, A> => S`

### Desigining the State Shape

In Redux, all application state is stored as a single object. Therefore, it's recommended by official documents to think of an application's state shape before writing any code.

For our todo App, we are going to store two different things:

- The currently selected visibility filter
- The actual list of todos

### Handling Actions

Now that we've decided what our state object looks like, we're ready to write a reducer for it. The reducer is a pure function that takes the previous state and an action, and returns the next state.

It's very important that the reducer stays pure. Things you should **NEVER** do inside a reducer:

- Mutate its arguments;
- Perform side effects like API calls and routing transitions;
- Call non-pure functions, e.g. Date.now() or Math.random().