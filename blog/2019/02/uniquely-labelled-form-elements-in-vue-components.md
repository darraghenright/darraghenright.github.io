Wednesday 27th February 2019

# Uniquely labelled form elements in Vue Components

## Introduction

It's generally good practice to include a `<label>` when you create a form element. Additionally, it's helpful to associate these elements to allow the user to click the label and focus on that form element.

## Labelled Controls

By referencing the form element's `id` attribute with the label's `for` attribute, you define the form element as a **labelled control** of that label. This should look familiar:

```html
<label for="name">What is your name?</label>
<input id="name" type="text">
```

There's a point to note however, according to MDN:

> The first element in the document with an id matching the value of the for attribute is the labeled control for this label element, if it is a labelable element.

Each `id` attribute must be unique in a given document, so if we create a reusable component in Vue that contains correctly labelled form elements, we need to make sure to generate unique ids.

## Example

Let's imagine our Vue app has a self-contained, reusable `search-box` component that emits a `start-search` event. We can easily reuse this component in various parts of our page by hooking into a parent method to perform a search request:

```html
<search-box @start-search="performSearchRequest"/>
```

An implementation of a `search-box` component might look something like:

```html
<template>
  <div class="form-group">
    <label for="search-term">What are you looking for?</label>
    <input id="search-term" type="text" v-model.trim="searchTerm">
    <button class="btn btn-primary"
            type="button"
            @click="startSearch"
            :disabled="isSearchButtonDisabled">
      <i class="fa fa-search"></i> Search
    </button>
  </div>
</template>

<script>

export default {
  computed: {
    isSearchButtonDisabled: ({searchTerm}) => searchTerm === null,
  },
  data () {
    return {
      searchTerm: null
    }
  },
  methods: {
    startSearch () {
      this.$emit('start-search', {searchTerm: this.searchTerm})
      this.searchTerm = null
    }
  }
}

</script>
```

A user must enter a search term to enable the search button. When they click the search button the component emits a `start-search` event containing the `searchTerm` to be handled by its parent, and resets the `searchTerm`.

## But...

There's a problem here—if we reuse this component in various parts of our document, the `input#search-term` is no longer a _unique_ id and is no longer an accurately identifiable labelled control. Clicking on any `search-box` label will always focus the _first_ input with that id. Not much good if you have more than one instance on the page.

## Solution

Fortunately, a solution is very straightforward! We need to create a computed property with a unique id for each component.

`_uid` to the rescue. It's an internal property of each component—an integer id that Vue assigns to uniquely identify each component it creates.

We could choose a more robust method to create a unique identifier, and I would normally be shy about relying on the internal properties of a Vue component instance.

However, given this particular use-case it's worth the trade-off and not have to worry about generating a unique value ourselves.

Let's create a computed property:

```js
computed: {
  id: ({_uid}) => `search-box-${_uid}`,
  // etc.
}
```
And use it like this:

```html
<label :for="id">
<input :id="id">
```

Done! :)
