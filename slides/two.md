Each widget handles complex values as well as primitives

```js
  var model;

  listOfPeople.push(model = {
    id: 2453,
    name: 'Jason Quense'
  })

  // set the whole model object as the value, 
  // and just tell it what property to display
  <DropdownList 
    data={listOfPeople}
    value={model}
    textField='name'/>

  // or if you only have the id you can just use that
  <DropdownList 
    data={listOfPeople}
    value={2453}
    valueField='id'
    textField='name'/>

```

Note:
  - this is helpful for easily binding some sort of model entity to an input with little fuss
  
- - -

I wanted to make inputs that worked great by themselves or could be easily 
used as building blocks for more complicated components.

To that end they need to be __simple__ to use while also unopinionated and configurable so that they can be easily __composed__

Note: 
  + an Annoyance with other libraries is that if you want to really extend, or tweak a widget you end up needing to override a lot and reimplmenet a lot. its very fragile.
  + When you want to adjust behavior you don't want to make a whole new thing :(.
  + Inputs are the sort of thing that you use all the time (in a CRUD app anyway), they can't be a pain to wire up, and I also need to be able to build stuff with them
s
vvv

To meet that goal I try to make sure that widgets:<!-- .element: style="text-align: left;" -->

- Manage the smallest possible amount of state
- Expose as a much as possible through `props`
- Have defaults that allow for progessive complexity; "opt-in" features

Note:
 - Side comment: inputs are not "submittable" in the traditional way. I am not please about "breaking" the form in this way but the alternative means i need to sync/store state in the DOM which is really bad for complex inputs.

vvv

Internationalization and accessibility part of "simple to use"

- Provide a localization strategy out of the box via Globalize.js <!-- .element: class="fragment" -->
- All functionality is accessible via keyboard navigation <!-- .element: class="fragment" -->
- Components implement relevant ARIA attributes/roles <!-- .element: class="fragment" -->
- Components work "right to left".<!-- .element: class="fragment" -->

Note:
 - keyboard nav is helpful for power users as well as users with disabilities
 - localization is critical for other cultures, no one else wants to read american date formats

- - -

### State

localized state limits composability; it represents codified behavior

State that can't be controlled by a parent must be duplicated or invert data flow
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

Note:
 - by behavior i mean that the pattern of storing state represents a behavior. Open when you click, close when you select. by deciding when and where to persist that state, you can adjust behavior.

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

Note:
- this is an aspect of the React ecosystem i sometimes find frustrating; while explicitness is good, it is sometimes unnecessary and counter-productive.

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
