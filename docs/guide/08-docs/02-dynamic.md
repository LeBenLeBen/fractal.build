---
handle: dynamic-docs
label: Dynamic docs
title: Dynamic Documentation
---

Because documentation pages are {{ link('@views', 'views') }} that are run through a template engine, it is possible to dynamically embed information about your components into your documentation.

This means that you can have documentation describing your components that stays up-to-date when aspects of your components change.

## Using bundled helpers

You can use the {{ link('@views#handlebars-helpers', 'Handlebars helpers') }} that ship with Fractal to embed the view template or context data for a component into your documentation. For example, if you were documenting a `button` component, you might have documentation that looks like this:

{% raw %}
````markdown

## Button component

The button component can be included within other components like this:

```
\{{> @button }}
```

This template for this component looks like this:

```
{{view '@button'}}
```

and it therefore expects a set of data to render it that is in the following format:

```
{{context '@button'}}
```

````



When rendered, the `{{view '@button'}}` and `{{context '@button'}}` expressions would be evaluated, and the _actual_ view template code and sample context data would be rendered in place.

Later on, if the `button` component template or context data is updated, then the documentation will also be automatically updated to match the changes, so it will never go out of date.

{% endraw %}

## Using the API

It's possible to take this one step further using {{ link('@api', 'Fractal\'s API') }} in combination with creating your own helpers.

When you set up your project, you can {{ link('@views#customising-handlebars', 'customise the Handlebars engine') }} to add in your own helpers. These helpers can use Fractal API methods to provide you access to whatever data you need about your component library.

For instance, if you wanted to create a Handlebars [block helper](http://handlebarsjs.com/block_helpers.html) that iterated over all of the available components in your project to create a list, you could use something like the following:

```js
const hbs = require('@frctl/handlebars')({
    helpers: {
        componentList: function() {
            let ret = "<ul>";
            const options = Array.from(arguments).pop();
            for (let component of fractal.components.flatten()) {
                ret = ret + "<li>" + options.fn(component.toJSON()) + "</li>";
            }
            return ret + "</ul>";
        }
    }
});

fractal.docs.engine(hbs);
```

You could then use this in your documentation pages to iterate over your components, for example to create a list of direct links to the component preview pages:

{% raw %}

```markdown
## Component preview links:

{{#componentList}}
<a href="{{path '/components/preview/{{ this.handle }}' }}">{{ this.title }}</a>
{{/componentList}}
```
{% endraw %}

Which would output something like the following (depending on your components, obviously!):

```html
<ul>
    <li><a href="/components/preview/button">Button</a></li>
    <li><a href="/components/preview/button-list">Button List</a></li>
    <li><a href="/components/preview/list">List</a></li>
    <li><a href="/components/preview/logo">Logo</a></li>
</ul>
```

See the {{ link('@api', 'API documentation') }} for details on how to access data about your component library programatically.
