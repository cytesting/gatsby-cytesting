---
title: Cypress Custom Commands
date: "2022-01-21T10:40:32.169Z"
description: Custom commands are helpful to encapsulate repetitive behavior
---

Writing custom commands helps us reuse code for repetitive tasks. Let's say we need to check the contents
of a nav bar against a given list **['Home', 'Library', 'Data']**:

```
const url = 'https://example.cypress.io/commands/traversal';
const listItems = ['Home', 'Library', 'Data'];

describe('Custom Command', () => {
    it('Check nav items', () => {
      cy.visit(url);
      cy.get('.traversal-breadcrumb')
        .children()
        .spread((a, b, c) => {
          expect(a).to.contain(listItems[0]);
          expect(b).to.contain(listItems[1]);
          expect(c).to.contain(listItems[2]);
        });
    });
});
```

One way to have a more compact code organization is to write a command in the same test spec:

```
const url = 'https://example.cypress.io/commands/traversal';
const listItems = ['Home', 'Library', 'Data'];

const checkNavItems = (selector, items) => {
  cy.get(selector)
    .children()
    .spread((a, b, c) => {
      expect(a).to.contain(items[0]);
      expect(b).to.contain(items[1]);
      expect(c).to.contain(items[2]);
     });
}
describe('Custom Command', () => {
    it('Check nav items', () => {
      cy.visit(url);
      checkNavItems('.traversal-breadcrumb', listItems);
    });
});
```

The other way is to write a custom command in the file **cypress/support/commands**. This is a more
convenient way if the function is needed in multiple test specs:

```
Cypress.Commands.add('checkNavItems', (selector, items) => {
    cy.get(selector)
    .children()
    .spread((a, b, c) => {
      expect(a).to.contain(items[0]);
      expect(b).to.contain(items[1]);
      expect(c).to.contain(items[2]);
     });
})
```

Then, in order to call the new command the name is prefixed with the *cy* object: **cy.checkNavItems()**

```
describe('Custom Command', () => {
    it('Check nav items', () => {
      cy.visit(url);
      cy.checkNavItems('.traversal-breadcrumb', listItems);
    });
});
```

The advantage of having commands in the **cypress/support/commands** file is that we can create custom logs too. Here **message** is printed in Cypress test runner while **consoleProps** is printed in the browser console:

```
Cypress.Commands.add('checkNavItems', (selector, items) => {
  let log = Cypress.log({name: 'navLog'});
  cy.get(selector)
    .children()
    .spread((a, b, c) => {
      expect(a).to.contain(items[0]);
      expect(b).to.contain(items[1]);
      expect(c).to.contain(items[2]);
    })
  log.set({
    message: `Checking selector: ${selector}`,
    consoleProps: () => {
      return {
        message: `Checking selector: ${selector}`
      };
    }
  });
});
```