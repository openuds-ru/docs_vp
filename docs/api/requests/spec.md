---
tags:
  - request
  - url
---

# spec

Builds a single request and respective expectations. In general, it represents a single API call or a single test case. It forms the base for other types of testing like `e2e`, `flow` and `fuzz`.

## Syntax

```js
spec()
spec(handler-name)
spec(handler-name, handler-options)
```

## Usage

### ✅  Correct Usage

```js
// always use 'await' statement
await spec()
  .get('/api/users/1')
  .expectStatus(200);
```

```js
// invoke the 'toss' method at the end to return a promise
spec()
  .get('/api/users/1')
  .expectStatus(200)
  .toss().then().catch();
```

```js
// use without chaining method calls
const sp = spec();
sp.get('/api/users/1');
sp.expectStatus(200);
await sp.toss(); // runs the test
```

```js
// using handler
await spec('get user')
  .expectJson('data.first_name', 'George');
```

```js
// using handler and custom handler-options
await spec('get user', 2)
  .expectJson('data.first_name', 'Janet');
```

### ❗ Incorrect Usage

```js
// will not make an api call as there is no 'await' statement or 'toss' method
spec().get('url').expectStatus(200);
```

```js
// cannot make multiple requests with the same spec object.
// Instead use multiple 'spec()' methods.
await spec()
  .get('/api/users/1')
  .expectStatus(200)
  .post('/api/users/2')
  .expectStatus(201);
```

```js
// empty spec details will fail
await spec();
```

## Arguments

#### > handler-name *(string)*

Name of the spec handler to use.

> Handlers should be defined before the usage. Else it will throw an error.

#### > handler-data? *(any)*

Handler data could be anything. With the help of this data, we can make the spec handlers dynamic.

#### > handler-options? *(object)*

Handler options to control the behavior of the spec handler.

- `memo` *(any)* - Memoize the spec handler. If the spec handler is already memoized, it will not run the spec handler. The value of memo can be anything. Based on this value, it will decide if to run the spec handler or not.


## Yields

Returns an object of spec which contains all the methods for making a request and validating the response.

## Examples

### Normal

```js
const { spec } = require('pactum');

await spec()
  .get('https://reqres.in/api/users/1')
  .expectStatus(200);
```

### Multiple Request Calls

```js
const { spec } = require('pactum');

await spec()
  .get('https://reqres.in/api/users/1')
  .expectStatus(200);

await spec()
  .get('https://reqres.in/api/users/2')
  .expectStatus(200);
```

### Using Handlers

```js
const { spec, handler } = require('pactum');

handler.addSpecHandler('get user', (ctx) => {
  const { spec, data } = ctx;
  spec.get('https://reqres.in/api/users/{id}');
  spec.withPathParams('id', data || 1)
  spec.expectStatus(200);
});

await spec('get user').expectJson('data.first_name', 'George');
await spec('get user', 2).expectJson('data.first_name', 'Janet');
```

### Using Handlers Memo

`memo` as boolean

```js
const { spec, handler } = require('pactum');

handler.addSpecHandler('get settings', (ctx) => {
  const { spec } = ctx;
  spec.get('https://example.com/settings');
  spec.expectStatus(200);
  spec.stores('SETTINGS', '.');
});

await spec('get settings', null, { memo: true }); // this will be executed
await spec('get settings', null, { memo: true }); // this won't be executed
```

`memo` as string

```js
const { spec, handler } = require('pactum');

handler.addSpecHandler('get settings', (ctx) => {
  const { spec, data } = ctx;
  spec.get(`https://example.com/settings/${data}`);
  spec.expectStatus(200);
  spec.stores(`SETTINGS:${data}`, '.');
});

await spec('get settings', 'user', { memo: 'user' }); // this will be executed
await spec('get settings', 'org', { memo: 'org' }); // this will be executed
await spec('get settings', 'user', { memo: 'user' }); // this won't be executed
await spec('get settings', 'org', { memo: 'org' }); // this won't be executed
```

## See Also

- [API Testing](/guides/api-testing)