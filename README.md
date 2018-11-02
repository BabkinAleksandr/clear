# Clear Project

Just another javascript library for building interfaces.

### Proposal API

**useState**

Function, that returns current state object of a component and callback to change it.
The only argument for `useState` is initial state, that would be used in the first render.

Every state change calls the rerender of a component.
State would be merged with existing one. You could pass only state changes, but not all changed state

```js
const { state: {}, changeState: () => void } = useState(initialState)
```

Examples:

```jsx
import Clear, { useState } from 'clear'

const initialState = { count: 0 }
const { state, changeState } = useState({ count: 0 })
const increaseCount = changeState({ count: state.count + 1 })
const resetCounter = changeState(initialState)

function MyComponent() {
  return () {
    <div>
      <p>The count is {count}</p>
      <button onClick={increaseCount}>Increase count</button>
    </div>
   }
}
```


```jsx
import Clear, { useState } from 'clear'

const { state, changeState } = useState({ name: '', lastname: '' })
const changeName = (e) => {
  changeState({ name: e.target.value }) // no need to pass the whole state, it would be merged internally with the last one
}
const changeLastName = (e) => {
  changeState({ lastname: e.target.value })
}

function MyComponent() {
  return () {
    <form>
      <p>Name: {state.name}</p>
      <p>Lastname: {state.lastname}</p>
      <input type="text" onChange={changeName} value={state.name}/>
      <input type="text" onChange={changeLastName} value={state.lastname}/>
    </form>
   }
}
```

**useCycle**

The custom lifecycle hooks of a component. Function accepts three functions, that will be fired at the appropriate time:
- didMount: hook will be fired just after component was rendered to DOM
- didUpdate: the props or state of component was changed, accepts previous props and state objects
- willUnmount: hook will be fired just before component would be deleted from the DOM

```js
useCycle(didMount: () => void, didUpdate: (prevProps: {}, prevState: {}) => void, willUnmount: () => void)
```

Example:

```jsx
import Clear, { useCycle } from 'clear'

const { state, changeState } = useState({ count: 0 })
const increaseCount = changeState({ count: state.count + 1 })

const didMount = () => { console.log('did mount') }
const didUpdate = (prevProps, prevState) => {
  if (state.count > 5) document.title = ''
}
const willUnmount = () => { console.log('will unmount') }

useCycle(didMount, didUpdate, willUnmount)
// useCucle(didMount)
// useCycle(null, didUpdate)
// useCycle(null, null, willMount

function MyComponent() {
  return () {
    <div>
      <p>The count is {count}</p>
      <button onClick={increaseCount}>Increase count</button>
    </div>
   }
}
```

**useControlledUpdate**

Controls the component update. Accepts array of values, that would be compared for rerender.

The second parameter is for deep comparing.
- true: deep compare. All structure values would be destructed for comparing
- false: shallow compare. All values would be compared by the link

If the first argument is boolean - it used as deep comparing parameter and the second one is omited.

_The shallow compare is set to default!_

```js
useControlledUpdate(values: []any|boolean, false: boolean)
```

```jsx
import Clear, { useControlledUpdate } from 'clear'

const { state, changeState } = useState({ count: 0 })
const increaseCount = changeState({ count: state.count + 1 })

// if props.externalCount changes, component would not be rerendered!
useControlledUpdate([state.count], true)

function MyComponent({ externalCount }) {
  return () {
    <div>
      <p>The count is {count}</p>
      <p>The external count is {count}</p>
      <button onClick={increaseCount}>Increase count</button>
    </div>
   }
}
```

Also it could be used just for setting deep compare.

```js
useControlledUpdate(true) // now all props and state values would be deep compared
```
