---
tags:
  - data template
  - data map
  - data store
  - data function
---

# parse

Parses data templates, maps, functions and stores. It helps to quickly see the outcome of data management items to troubleshoot issues.

## Syntax

```js
parse(input)
```

## Usage

### ✅  Correct Usage

```js
parse({ '@DATA:TEMPLATE@': 'User' }); // parses data templates
parse('$M{User.FirstName}'); // parses data maps
parse('$F{GetTimeStamp}'); // parses data functions
parse('$S{FirstPostId}'); // parses data stores

// parse all of them together
parse({
  'name': '$M{User.FirstName}',
  'createAt': '$F{GetTimeStamp}',
  'postId': '$S{FirstPostId}',
  'address': {
    '@DATA:TEMPLATE@': 'Address'
  }
});
```

## Arguments

#### > input (string|object)

Input to be parsed.

If the input doesn't contain any data management items, it will simply return the original input.

> Make sure to define and load the data management items before invoking this function.

## Examples

#### Parse a data template

```js
const { parse, stash } = require('pactum');

stash.addDataTemplate({
  'Address': { street: 'some street', pin: 100100 }
});

const address = parse({ '@DATA:TEMPLATE@': 'Address' });
// prints { street: 'some street', pin: 100100 }
console.log(address); 
```

## See Also

- [Data Management](/guides/data-management)