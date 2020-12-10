Sunday 16th March 2019

# Quickly rename a JS object property with Rest Parameters, Spread Syntax and Object Destructuring

The other day I was working on a JS project where I had a small array of object literals from an API call, which looked something like this:

```js
let cats = [
  { id: 1, name: 'Aggie', gender: 'female', personality: 'cuddly' },
  { id: 2, name: 'Pixel', gender: 'female', personality: 'friendly' },
  { id: 3, name: 'Bob',   gender: 'male',   personality: 'hungry' },
  { id: 4, name: 'Ozzie', gender: 'male',   personality: 'bitey' }
]
```

I was using this data in a third-party library that accepts an array of object literals, but required a `text` property instead of a `name` property.

For various reasons I could not (and did not want to) change the API endpoint, or add a new one, so my remaining option was to rename the `name` property to `text` on the client. Since new properties might get added to this call in future, I wanted to to **explicitly** rename the `name` property while **implicitly** handling the others.

With some of ES6 goodness—namely [object destructuring and renaming](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Assigning_to_new_variable_names), [spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) and [rest parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)—we can succinctly map over the array of objects like so:

```js
let renamed = cats.map(({name: text, ...props}) => ({text, ...props}))
```

Which yields the following transformation:

```js
[
  { id: 1, text: 'Aggie', gender: 'female', personality: 'cuddly' },
  { id: 2, text: 'Pixel', gender: 'female', personality: 'friendly' },
  { id: 3, text: 'Bob',   gender: 'male',   personality: 'hungry' },
  { id: 4, text: 'Ozzie', gender: 'male',   personality: 'bitey' }
]
```

Of course, it's also perfectly acceptable perform a map to add a `text` field, especially in a codebase where using ES6 features is not possible:

```js
var renamed = cats.map(function (cat) {
  cat.text = cat.name
  return cat
})
```

Indeed, if the data to be mapped was large, it may also warrant seeing which strategy performs better. In the above scenario, these were not considerations and it was enjoyable to use modern, expressive ES6 features to create a concise solution :)
