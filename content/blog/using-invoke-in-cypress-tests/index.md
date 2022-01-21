---
title: Using invoke() in Cypress tests
date: "2022-01-21T22:11:03.284Z"
description: The function invoke() can be used to extract information from a DOM node
---

### Calling a function with invoke():

Invoke calls a function on a yielded subject. In the next simple example, invoke executes the function in the object:

{% highlight javascript %}
const obj = {getNumber: () => { return 10;}};
cy.wrap(obj).invoke('getNumber').should('eq', 10); 
{% endhighlight %}

It is possible to add arguments to the function if needed:

{% highlight javascript %}
const obj = {
  sum: function(a, b) {return a + b}
};
cy.wrap(obj).invoke('sum', 2, 3).should('eq', 5);
{% endhighlight %}


### Extracting attributes from HTML elements:

We can extract text from an HTML element. Let's say we have an *h1* element:

{% highlight html %}
<h1>Cypress testing</h1>
{% endhighlight %}

Then we can confirm the text it contains:

{% highlight javascript %}
cy.get('h1').invoke('text').should('contain', 'testing');
{% endhighlight %}

If we want to confirm that the *src* attribute of an image points to a proper url, we could use this code:

{% highlight javascript %}
cy.get('img').eq(0).invoke('attr', 'src')
  .then((src) => {
    cy.request(src)
      .then((resp) => {
        cy.wrap(resp.status).should('eq', 200);
       })
  });
{% endhighlight %}

### Extract options list from a select:

{% highlight javascript %}
cy.get('.multiple-select')
  .invoke('val')
  .should('include', 'option1')
{% endhighlight %}

If the wrapped object does not have a function, you can use **its** instead of **invoke** to extract a property:

{% highlight javascript %}
const obj = {
  value: 23
};
cy.wrap(obj).invoke('value').should('eq', 23); // This does not work!
cy.wrap(obj).its('value').should('eq', 23);
{% endhighlight %}