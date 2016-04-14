# react-gettext-parser

A gettext utility that extract translatable strings from JSX (and regular JavaScript) and puts them into a .pot file. It uses the [babylon](https://github.com/babel/babylon) AST parser.

It can be used directly in JavaScript, in gulp, [via babel](https://github.com/alexanderwallin/babel-plugin-react-gettext-parser) or as a standalone CLI utility to be used in the terminal or from npm scripts.

## Features

* Map component names and properties to gettext variables
* Map function names and arguments to gettext variables
* Merges identical strings found in separate files and concatenates their references
* Supports globs

## Usage

### Using the CLI

```bash
react-gettext-parser --config path/to/config.js --target messages.pot 'src/**/{*.js,*.jsx}'
```

### Using the API

```js
// Node script somewhere
import fs from 'fs';
import { getMessages, parseFile } from 'react-gettext-parser';

// Get array of message objects
const messages = getMessages(fs.readFileSync('MyComponent.jsx'), {
  filename: 'MyComponent.jsx'
});

// Parse a file and put it into a pot file
parseFile('MyComponent.jsx', { target: 'messages.pot' }, () => {
  // Done!
});
```

### Via [`babel-plugin-react-gettext-parser`](http://github.com/alexanderwallin)

```bash
babel --plugins react-gettext-parser src
```

```js
// .babelrc
{
  "presets": ["es2015", "react"],
  "plugins": [
    ["react-gettext-parser", {
      // Options
    }]
  ]
}
```

### In an npm script

```js
{
  "scripts": {
    "build:pot": "react-gettext-parser --config path/to/config.js --target messages.pot 'src/**/*.js*'"
  }
}
```

### As a gulp task

```js
var reactGettextParser = require('react-gettext-parser').gulp;

gulp.task('build:pot', function() {
  return gulp.src('src/**/*.js*')
    .pipe(reactGettextParser({
      target: 'messages.pot',
      // ...more options
    }))
    .pipe(gulp.dest('translations'));
});
```

## Options

#### `target`

The destination path for the .pot file.

#### `componentPropsMap`

A two-level object of prop-to-gettext mappings.

The defaults are:

```js
{
  GetText: {
    message: 'msgid',
    messagePlural: 'msgid_plural',
    context: 'msgctxt',
    comment: 'comment',
  }
}
```

The above would make this component...

```js
// MyComponent.jsx
<GetText
  message="One item" 
  messagePlural="{{ count }} items" 
  count={numItems}
  context="Cart"
  comment="The number of items added to the cart"
/>
```

...would result in the following translation definition:

```pot
# The number of items added to the cart
#: MyComponent.jsx:2
msgctxt "Cart"
msgid "One item"
msgid_plural "{{ count }} items"
msgstr[0] ""
msgstr[1] ""
```

#### `funcArgumentsMap`

An object of function names and corresponding arrays of strings that matches arguments against gettext variables.

Defaults:

```js
{
  gettext: ['msgid'],
  dgettext: [null, 'msgid'],
  ngettext: ['msgid', 'msgid_plural'],
  dngettext: [null, 'msgid', 'msgid_plural'],
  pgettext: ['msgctxt', 'msgid'],
  dpgettext: [null, 'msgctxt', 'msgid'],
  npgettext: ['msgctxt', 'msgid', 'msgid_plural'],
  dnpgettext: [null, 'msgid', 'msgid_plural'],
}
```

This configs means that this...

```js
// Menu.jsx
<Link to="/inboxes">
  { npgettext('Menu', 'Inbox', 'Inboxes') }
</Link>
```

...would result in the following translation definition:

```pot
#: Menu.jsx:13
msgctxt "Menu"
msgid "Inbox"
msgid_plural "Inboxes"
msgstr[0] ""
msgstr[1] ""
```

## Developing

```bash
npm i && npm run dev
npm run lint
npm run build
```

## License

ISC
