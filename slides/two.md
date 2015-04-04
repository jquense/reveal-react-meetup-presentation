Each widget handles **complex** values as well

```js
  var model = {
    id: 0,
    name: 'Jason Quense'
  }

  listOfPeople.push(model)

  // set the whole model object as the value, 
  // and just tell it what property to display
  <DropdownList 
    data={listOfPeople}
    value={model}
    textField='name'/>

  // or if you only have the id you can just use that
  <DropdownList 
    data={listOfPeople}
    value={0}
    valueField='id'
    textField='name'/>

```
- - -

I wanted to make inputs that worked great by themselves or could be easily 
used as primitives for more complicated components.

To that end they need to be __simple__ to use while also unopinionated and configurable so that they can be easily __composed__

vvv

To meet that goal I try to make sure that Widgets:<!-- .element: style="text-align: left;" -->

- Manage the smallest possible amount of state
- Expose as a much as possible through `props`
- Have defaults that allow for progessive complexity; "opt-in" features

- - -

### State

localized state limits composability; it represents codified behavior

State that can't be controlled by a parent must be duplicated which inverts data flow
<!-- .element: class="fragment" -->

```js
// we want to adjust the open behavior of the multiselect
componentDidUpdate(prevProps, prevState){
  let select = this.refs.select;
  
  // if we're lucky there is a api method on the ref, otherwise...
  if (this.state.parentSaysItsOpen && !prevState.parentSaysItsOpen)
    select.setState({ open: true }) // <-- blllaaah

  else if( !this.state.parentSaysItsOpen && prevState.parentSaysItsOpen)
    select.setState({ open: false })
}

render(){
  // multiselect internally managing its `open` state
  return <Multiselect refs='select' />
}
```
<!-- .element: class="fragment" -->

vvv

Alernatively State that isn't managed, requires the parent to do all the wiring

__every__. __single__. __time__.

```jsx
render(){
  return (
    <Multiselect
      value={this.state.value}
      open={this.state.open} 
      searchTerm={this.state.currentFilter}
      onChange={value => this.setState({ value })}
      onToggle={open => this.setState({ open })}
      onSearch={currentFilter => this.setState({ currentFilter })}/>
  )
}
```
<!-- .element: class="" -->

This is terrible when you have 15 inputs in a form, especially since the parent will defer to the input for the state _most_ of the time

- - - 

### Controlled/Uncontrolled inputs

Taking a hint from React, on handling inputs

```js
  // I don't even have an initial value!
  <input type='text' /> 

  // Start with my value, but take it from there
  <input type='text' defaultValue={this.state.initialValue}/>

  // I don't trust you to manage this for me
  <input type='text' value={this.state.value} onChange={this.handleChange}/>
```
<!-- .element: class="fragment current-visible visible current-fragment" -->

```js
  // You pick the starting open position
  <Multiselect /> 

  // Start open, but take it from there
  <Multiselect defaultOpen={this.state.initialOpen}/>

  // I don't trust you to manage this for me
  <Multiselect open={this.state.value} onToggle={this.handleChange}/>
```
<!-- .element: class="fragment current-visible" -->

vvv

This logic adds complexity, but its a complexity that is _really_ easy to abstract away using a higher order component, because the interface is predictable. 

1. Make the "base" component completelly controlled, ie. store no state for specific behaviors: value, open, filtering, etc.<!-- .element: class="fragment" -->

2. Wrap the "base" in another component that defers to the parent if a prop is provided, otherwise manage it in state. <!-- .element: class="fragment" -->

vvv 

<img src='slides/img/uncontrollable.png'/>
`jquense/uncontrollable`

Note:
- The pattern is predictable: store value in state, change when the handler is called
- you can checkout an implementation I refactored out at my github

- - - 
#### Props and Defaults

Widgets expose a lot of props, which is why good defaults are important. 

The simpliest use case should be the simplest to instantiate.

```xml
<DateTimePicker defaultValue={new Date}/>

<DropdownList data={colors}/>
```

And more complex functionality is opt-in via props

```xml
  <DropdownList isRtl
    value={1223} 
    data={contacts}
    valueField='id' 
    textField='full name'
    groupBy={contact => contact.lastName}/>
```

vvv 

Exposing a broad api via props gives a user freedom to redefine any defaults they don't like, or create "shortcut" components by wrapping.

```js
class MyDatePicker extends React.Component {

  static defaultProps = { 
    
    format: 'MMM dd, yyyy', 
  }

  render(){
    return <DateTimePicker {...this.props}/>
  }
}
```

react-widgets tries to expose as much as possible, even down to the components that make up each widget.
