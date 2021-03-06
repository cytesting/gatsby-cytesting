---
title: Reporting with Cypress and Mochawesome
date: "2022-01-20T10:40:32.169Z"
description: Mochawesome is a reporter that can be integrated with Cypress
---

![mochawesome reporter](./mochawesome-featured.png)

Mocha is a test framework that runs on Node.js and is able to make asynchronous tests in the browser. Cypress uses Mocha bdd syntax structure to carry out tests. Mocha allows the use of reporters in cypress tests. Mochawesome is a reporter that can generate HTML/CSS reports to visualize test run executions. It works well with Cypress.

In order to create a report, first you have to install mochawesome and the report generator:

```
npm install --save-dev mochawesome mochawesome-merge mochawesome-report-generator  
```

In cypress.json we can configure the reporter to have mochawesome create JSON files in the cypress/results folder:

```
{
  "reporter": "mochawesome",
  "reporterOptions": {
    "reportDir": "cypress/results",
    "overwrite": false,
    "html": false,
    "json": true
  }
}  
```

The above setting will generate JSON files like these:

```
cypress/results/mochawesome.json or cypress/results/mochawesome_001.json
```

which can be merged in one only file with *mochawesome-merge*:

```
npx mochawesome-merge "cypress/results/*.json" > mochawesome.json
```

Then we can create an HTML file using *mochawesome-report-generator* (marge):

```
npx marge mochawesome.json
```

Let's say we have this spec code to go to Wikipedia page and interact with the landing page:

```
describe('Mochawesome example', () => {
  it('Visit wikipedia', () => {
    cy.visit('https://www.wikipedia.org/');
    cy.get('#searchLanguage').select('English');
    cy.get('#searchInput').type('software testing');
    cy.get('.pure-button').click();
    cy.get('h1').should('contain', 'Software testing');
  });
});
```

After running in headless mode with the command:

```
npx cypress run
```

Then we call the commands mentioned above:

```
npx mochawesome-merge "cypress/results/*.json" > mochawesome.json
npx marge mochawesome.json
```

We get this report in mochawesome-report/mochawesome.html:

![passing test](./mocha-pass-test.png)

What if the test fails? For example, in this code snippet:

```
describe('Mochawesome example', () => {
  it('Visit wikipedia', () => {
    cy.visit('https://www.wikipedia.org/');
    cy.get('#searchLanguage').select('English');
    cy.get('#searchInput').type('software testing');
    cy.get('.pure-button').click();
    cy.get('h1').should('contain', 'Api testing'); // Here the test will fail
  });
})
```

We still get a report on the failed test and the output error:

![failing test](./mocha-fail-test.png)

With mochawesome you can present the results of your test automation which include passing or failing specs, the duration of each spec, and the error messages when tests fail. 