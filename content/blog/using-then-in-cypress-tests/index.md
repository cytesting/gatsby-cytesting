---
title: Using then() in Cypress tests
date: "2022-01-21T12:46:37.121Z"
description: The function then() reflects the asynchronous nature of Cypress
---

Built-in Cypress commands like **cy.get()** are asynchronous. For example, this code won't work because there is no returned value, but rather a javascript promise:

```
const $div = cy.get('div'); // won't work!
const text = $div.text();
```

As stated in in the [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises) about promises, what can be done is to attach a callback:

> Essentially, a promise is a returned object to which you attach callbacks, instead of passing callbacks into a function.

**Then** is used to further process what the promise yields:

```
cy.get('div').then(($div) => {
    const text = $div.text();
    console.log(text);
})
```

It is possible to send a new value and create a chain of callbacks:

```
cy.get('div').then(($div) => {
    const text = $div.text();
    return text;
}).then(($text) => {
    expect($text).to.contain('testing');
})
```

It is also possible to chain **then** with **should**. This happens because **should** returns the same subject it received previously:

```
cy.get('div').should('have.class', 'main').then(($div) => {
    const text = $div.text();
    return text;
}).then(($text) => {
    expect($text).to.contain('testing');
})
```

### Example: get the values from list items

Let's say we have the next **html** of list items:

```
<ul class="nav">
    <li>1</li>
    <li>2</li>
    <li>3</li>
</ul>
```

The code to extract the values from list items and do assertions in Cypress is next:

```
describe('Assertions', () => {
  it('Check list items', () => {
    cy.visit(localhost);
    cy.get('ul.nav > li').then(($items) => {
      expect($items).to.have.length(3);
      expect($items[0]).to.contain('1');
      expect($items[1]).to.contain('2');
      expect($items[2]).to.contain('3');
    });
  });
})
```

And these are the results as seen in the test runner:
