# addDataTemplate

Adds a data template which can be used across pactum tests and mock server.

## Syntax

```js
addDataTemplate(templates)
```

## Usage

### ✅  Correct Usage

```js
stash.addDataTemplate({
  'User': {
    "name": "morpheus",
    "job": "leader"
  }
});
```

## Arguments

#### > templates *(object)*

A template object.

## Examples

```js
const { stash, spec } = require('pactum');

stash.addDataTemplate({
  'User': {
    "name": "morpheus",
    "job": "leader"
  }
});

await spec()
  .post('https://reqres.in/api/users')
  .withJson({
    '@DATA:TEMPLATE@': 'User',
  });
```