## [React in patterns](../../README.md) / Controlled and uncontrolled inputs

* [Source code](https://github.com/krasimir/react-in-patterns/tree/master/patterns/controlled-uncontrolled/src)

---

These two terms *controlled* and *uncontrolled* are very often used in the context of forms management. *controlled* input is an input that gets its value from a single source of truth. For example the `App` component below has a single `<input>` field which is *controlled*:

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: 'hello' };
  }
  render() {
    return <input type='text' value={ this.state.value } />;
  }
};
```

The result of this code is an input element that we can type in but the value stays the same - `'hello'`. It is never updated because we have a single source of truth - the `App`'s component state. To make the input works as expected we have to add an `onChange` handler and update the state (the single source of truth). Which will trigger a new rendering cycle and we will see our updates.

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: 'hello' };
    this._change = this._handleInputChange.bind(this);
  }
  render() {
    return (
      <input
        type='text'
        value={ this.state.value }
        onChange={ this._change } />
    );
  }
  _handleInputChange(e) {
    this.setState({ value: e.target.value });
  }
};
```

The `<input>` element is still *controlled* but now we see the screen updating while we type.

On the opposite side is the *uncontrolled* input where we let the browser handles the user's updates. We may still provide an initial value by using the `defaultValue` prop.

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: 'hello' };
  }
  render() {
    return <input type='text' defaultValue={ this.state.value } />;
  }
};
```

That is nice but that `<input>` above is a little bit useless because the user updates the input's value but our component has no idea about that. We then have to use [`Refs`](https://reactjs.org/docs/glossary.html#refs) to get an access to the actual element.

```js
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { value: 'hello' };
    this._change = this._handleInputChange.bind(this);
  }
  render() {
    return (
      <input
        type='text'
        defaultValue={ this.state.value }
        onChange={ this._change }
        ref={ input => this.input = input }/>
    );
  }
  _handleInputChange() {
    this.setState({ value: this.input.value });
  }
};
```

The `ref` prop receives a string or a callback. The code above uses a callback and stores the DOM element into a *local* variable called `input`. Later when the `onChange` handler is fired we get the new value and send it to the `App`'s state.

*Using a lot of `refs` is not a good idea. If it happens in your app consider using `controlled` inputs and re-think your components.*

## Final thoughts

*controlled* versus *uncontrolled* inputs is very often underrated. However I believe that it is a fundamental decision because it dictates the data flow in the React component. I personally thing that *uncontrolled* inputs are kind of an anti-pattern and try to avoid them if possible.


