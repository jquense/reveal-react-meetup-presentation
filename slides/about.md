I am the frontend and UX dev at __International Justice Mission__

<div class="fragment">
    <p>
        IJM is a global organization that works to protect the poor in the developing world from:
    </p>
    <div class='flex center' style='font-size: 85%;margin-top: 30px;'>
         <div class='inline-item'>forced labor</div>
         <div class='inline-item'>sex trafficking</div>
         <div class='inline-item'>sexual violence</div>
         <div class='inline-item'>police brutality</div>
         <div class='inline-item'>property grabbing</div>
         <div class='inline-item'>citizens rights abuse</div>
    </div>
</div>
<br/>

My team builds software to help support field work and to capture data about these abuses <!-- .element: class="fragment" -->

<br/>
I also build a react components <!-- .element: class="fragment" -->

- - -

###react-widgets

A suite of typed or data "bound" form input components for React.

- Minimal time spent "wiring" components
    + controlled/uncontrolled
- Highly extensible with reasonable defaults
- Accessible
    + ARIA roles
    + Keyboard navigation
    + Right-to-left
    + Localizable where appropriate

vvv

It should be simple to take a list of data and generate a dropdown from it, React makes its simplier then other frameworks.

```js
function change(selected){
    var value = _.find(this.state.data, { id: selected })
    this.setState({ value })
}

<select value={this.state.value} onChange={change.bind(this)}>
    { this.state.data.map( d => 
        <option value={d.id}>{ d.text}</option>)
    }
</select>
```

It'ss still more boilerplate than you want to write for form inputs (and hard to style).

vvv

Alternatively:

```js
    <DropdownList 
        data={this.state.data} 
        value={this.state.value} 
        onChange={ value => this.setState({ value })}
        textField='text'/>
```


- - -

### Minimal Wiring

Widgets provide prop/handler pairs that can be controlled by the user, or be left to the component to manage.

```js
    <DropdownList value={state.value} onChange={this._handleChange}/>
    // or
    <DropdownList defaultValue={props.value}/>
    // works with other props as well
    <DropdownList open={state.isOpen} onToggle={this._handleOpen}/>
```






