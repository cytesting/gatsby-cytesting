---
title: Reporting with Cypress and Mochawesome
date: "2022-02-21T10:40:32.169Z"
description: Mochawesome is a reporter that can be integrated with Cypress
---

Mocha is a test framework that runs on Node.js and is able to make asynchronous tests in the browser. Cypress uses Mocha bdd syntax structure to carry out tests. Mocha allows the use of reporters in cypress tests. Mochawesome is a reporter that can generate HTML/CSS reports to visualize test run executions. It works well with Cypress.

In order to create a report, first you have to install mochawesome and the report generator:

{% highlight javascript %}
npm install --save-dev mochawesome mochawesome-merge mochawesome-report-generator  
{% endhighlight %}

In cypress.json we can configure the reporter to have mochawesome create JSON files in the cypress/results folder:

{% highlight javascript %}
{
  "reporter": "mochawesome",
  "reporterOptions": {
    "reportDir": "cypress/results",
    "overwrite": false,
    "html": false,
    "json": true
  }
}  
{% endhighlight %}

The above setting will generate JSON files like these:

{% highlight javascript %}
cypress/results/mochawesome.json or cypress/results/mochawesome_001.json
{% endhighlight %}

which can be merged in one only file with *mochawesome-merge*:

{% highlight bash %}
npx mochawesome-merge "cypress/results/*.json" > mochawesome.json
{% endhighlight %}

Then we can create an HTML file using *mochawesome-report-generator* (marge):

{% highlight javascript %}
npx marge mochawesome.json
{% endhighlight %}

Let's say we have this spec code to go to Wikipedia page and interact with the landing page:

{% highlight javascript %}
describe('Mochawesome example', () => {
  it('Visit wikipedia', () => {
    cy.visit('https://www.wikipedia.org/');
    cy.get('#searchLanguage').select('English');
    cy.get('#searchInput').type('software testing');
    cy.get('.pure-button').click();
    cy.get('h1').should('contain', 'Software testing');
  });
});
{% endhighlight %}

After running in headless mode with the command:
{% highlight javascript %}
npx cypress run
{% endhighlight %}

Then we call the commands mentioned above:

{% highlight javascript %}
  npx mochawesome-merge "cypress/results/*.json" > mochawesome.json
  npx marge mochawesome.json
{% endhighlight %}

We get this report in mochawesome-report/mochawesome.html:


What if the test fails? For example, in this code snippet:

{% highlight javascript %}
describe('Mochawesome example', () => {
  it('Visit wikipedia', () => {
    cy.visit('https://www.wikipedia.org/');
    cy.get('#searchLanguage').select('English');
    cy.get('#searchInput').type('software testing');
    cy.get('.pure-button').click();
    cy.get('h1').should('contain', 'Api testing'); // Here the test will fail
  });
})
{% endhighlight %}

We still get a report on the failed test and the output error:


With mochawesome you can present the results of your test automation which include passing or failing specs, the duration of each spec, and the error messages when tests fail. 