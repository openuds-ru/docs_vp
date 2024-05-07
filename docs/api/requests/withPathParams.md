---
tags:
  - url
  - path params
---

# withPathParams

Path parameters are named URL segments that are used to capture the values specified at their position in the URL.

## Syntax

```js
withPathParams(name, value)      // key-value pair
withPathParams(path-params)      // key-value pairs as object
```

## Usage

### ✅  Correct Usage

```js
// {user_id} is replaced to 1
await spec()
  .get('/api/users/{user_id}')
  .withPathParams('user_id', 1)
  .expectStatus(200);
```

```js
// url is updated to '/api/users/1/accounts/CY001001'
await spec()
  .get('/api/users/{user_id}/accounts/{account_id}')
  .withPathParams('user_id', 1)
  .withPathParams('account_id', 'CY001001')
  .expectStatus(200);
```

```js
// url is updated to '/api/users/1/accounts/CY001001'
await spec()
  .get('/api/users/{user_id}/accounts/{account_id}')
  .withPathParams({
    'user_id': 1,
    'account_id': 'CY001001'
  })
  .expectStatus(200);
```

### ❗ Incorrect Usage

```js
 // don't use both overridden methods in same spec
await spec()
  .get('/api/users/{user_id}/accounts/{account_id}')
  .withPathParams('user_id', 1)
  .withPathParams({
    'account_id': 'CY001001'
  })
  .expectStatus(200);
```

## Arguments

#### > name (string)

Name of the path param. It is represented by a special syntax in the url which is wrapped inside curly braces - `{<name>}`.

#### > value (string)

Value of the path param. The specified values are replaced in the url.

#### > path-params (object)

key-value pairs of path params. 

## Examples

#### Single path param

```js
const { spec } = require('pactum');

await spec()
  .get('/api/users/{user_id}')
  .withPathParams('user_id', 1)
  .expectStatus(200);
```

#### Multiple path params

```js
const { spec } = require('pactum');

await spec()
  .get('/api/users/{user_id}/accounts/{account_id}')
  .withPathParams('user_id', 1)
  .withPathParams('account_id', 'CY001001')
  .expectStatus(200);

// or

await spec()
  .get('/api/users/{user_id}/accounts/{account_id}')
  .withPathParams({
    'user_id': 1,
    'account_id': 'CY001001'
  })
  .expectStatus(200);
```

## See Also

- [withPath](/api/requests/withPath)