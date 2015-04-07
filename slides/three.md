Generally I try to make use of the public API to add features.

e.g grouped dropdowns could have been implemented externally

- - - 
### Challenges

- Tracking React versions accross breaking changes
    + I try not to bump a major if I can hide an api change in React
- Not breaking HTML input paradigms <!-- .element: class="fragment" -->
    + accessibility: keyboard navigation, tabbing, ARIA
    + passing the right props to the right sub components 
    + handling disabled, readonly, etc correctly
- Some things are just more imperative in nature <!-- .element: class="fragment" -->
    + focus management
    + animation is __hard__ to generalize in React
    + scroll management
- Anytime you need to drop into the DOM introduces a plethora of potential issues <!-- .element: class="fragment" -->

- - - 
- 
###Questions?
