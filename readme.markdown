# Horsey

> Progressive and customizable autocomplete component

Browser support includes every sane browser and **IE7+**.

# Demo!

You can see a [live demo here][3].

[![screenshot.png][5]][3]

# Inspiration

I needed a fast, easy to use, and reliable autocomplete library. The ones I stumbled upon were too bloated, too opinionated, or provided an unfriendly human experience.

The goal is to produce a framework-agnostic autocomplete that is easily integrated into your favorite MVC framework, that doesn't translate into a significant addition to your codebase, and that's **enjoyable to work with**. Horsey shares the modular design philosophy of [Rome, the datetime picker][1]. Furthermore, it plays well with [Insignia, the tag editor][2] component, and pretty much any other well-delimited component out there.

# Features

- Small and focused
- Natural keyboard navigation
- Progressively enhanced
- Extensive browser support
- Fuzzy searching

# Install

You can get it on npm.

```shell
npm install horsey --save
```

Or bower, too.

```shell
bower install horsey --save
```

# Options

Entry point is `horsey(input, options)`. Configuration options are detailed below. This method [returns a small API](#api) into the `horsey` autocomplete list instance.

### `suggestions`

An array containing a list of suggestions to be presented to the user. Each suggestion can be either a string or an object. If an object is used, the `text` property will be used for displaying the suggestion and the `value` property will be used when a suggestion is selected.

Alternatively, the `suggestions` can be a function. In this case, the function will be called when the input is focused, and expected to return a list of suggestions through a callback.

###### Example

The example below would create an instance with a predefined set of suggestions.

```js
horsey(input, {
  suggestions: ['sports', 'drama', 'romantic comedy', 'science fiction', 'thriller']
});
```

###### Example

Here's how you would lazy load your suggestions, except, you know, using actual AJAX calls. Note that this method is called a single time.

```js
horsey(input, {
  suggestions: function (done) {
    setTimeout(function () {
      done(['sports', 'drama', 'romantic comedy', 'science fiction', 'thriller']);
    }, 2000);
  }
});
```

### `filter`

Allows you to hide suggestions based on user input. The default implementation uses the [fuzzysearch][4] module to discard suggestions that don't contain anything similar to the user input.

###### Default

```js
function defaultFilter (q, suggestion) {
  return fuzzysearch(q, getText(suggestion)) || fuzzysearch(q, getValue(suggestion));
}
```

###### Example

The example below would always display every suggestion, except when the user input looks like `'seahawks/managers'`, in which case it would only return suggestions matching the `'seahawks'` team.

```js
horsey(input, {
  filter: function (q, suggestion) {
    var parts = q.split('/');
    return parts.length === 1 ? true : suggestion.team === parts[0];
  }
});
```

### `getText`

A function that returns the textual representation to be displayed on the suggestion list. The result of `getText` is also used when filtering _under the default implementation_.

###### Default

```js
function defaultGetText (suggestion) {
  return typeof suggestion === 'string' ? suggestion : suggestion.text;
}
```

###### Example

The example below would return a model's `displayName` for convenience.

```js
horsey(input, {
  getText: function (suggestion) {
    return suggestion.displayName;
  }
});
```

### `getValue`

A function that returns the value to be given to the `input` when a suggestion is selected.

###### Default

```js
function defaultGetValue (suggestion) {
  return typeof suggestion === 'string' ? suggestion : suggestion.value;
}
```

###### Example

The example below would return a model's `username` for convenience.

```js
horsey(input, {
  getValue: function (suggestion) {
    return suggestion.username;
  }
});
```

### `set`

A function that gets called when an option has been selected on the autocomplete.

###### Default

```js
function defaultSetter (value) {
  input.value = value;
}
```

###### Example

The example below would append values instead of overwriting them.

```js
horsey(input, {
  set: function (value) {
    input.value += value + ', ';
  }
});
```

### `autoHideOnClick`

Hides the autocomplete list whenever something other than the `input` or any child of the autocomplete's `<ul>` element is clicked. Defaults to `true`.

### `autoHideOnBlur`

Hides the autocomplete list whenever something other than the `input` or any child of the autocomplete's `<ul>` element is focused. Defaults to `true`.

### `render`

A function that's used to decide what to display in each suggestion item. `render` will take the `<li>` element as the first argument, and the suggestion model as the second argument.

###### Default

```js
function defaultRenderer (li, suggestion) {
  li.innerText = li.textContent = getText(suggestion);
}
```

###### Example

The example below would assign arbitrary HTML found in the `suggestion` model to each list item. Note that rendering doesn't necessarily have to be synchronous.

```js
horsey(input, {
  render: function (li, suggestion) {
    li.innerHTML = suggestion.html;
  }
});
```

### `appendTo`

Where should the `<ul>` element containing the autocomplete options be placed? Generally an irrelevant option, but useful if you're dealing with a SPA, where you want to _keep the element inside your view instead of the body_, so that it gets cleaned up as the view goes away.

Defaults to `document.body`.

### `form`

The `form` the input belongs to. If provided, the autocomplete list will be hidden whenever the form is submitted.

# API

Once you've instantiated a `horsey`, you can do a few more things with it.

### `.add(suggestion)`

Just like when passing `suggestions` as an option, you can add individual suggestions by calling `.add(suggestion)`. Returns the `<li>` element for this suggestion in the autocomplete list. There isn't an API method to remove the suggestion afterwards, so you'll have to grab onto the `<li>` reference if you want to remove it later on.

### `.clear()`

You can however, remove every single suggestion from the autocomplete, wiping the slate clean. Contrary to `.destroy()`, `.clear()` won't leave the `horsey` instance useless, and calling `.add` will turn it back online in no time.

### `.show()`

Shows the autocomplete list.

### `.hide()`

Hides the autocomplete list.

### `.destroy()`

Unbind horsey-related events from the `input`, remove the autocomplete list. It's like `horsey` was never here.

# Events

Once you've instantiated a `horsey`, some propietary synthetic events will be emitted on the provided `input`.

Name              | Description
------------------|---------------------------------------------------------------
`horsey-selected` | Fired after a suggestion is selected from the autocomplete

# License

MIT

[1]: https://github.com/bevacqua/rome
[2]: https://github.com/bevacqua/insignia
[3]: http://bevacqua.github.io/horsey
[4]: https://github.com/bevacqua/fuzzysearch
[5]: http://i.imgur.com/imDFC0C.png
