---
tags:
  - body
  - json
  - payload
---

# withJson

Request body as json payload.

## Syntax

```js
withJson(payload)
withJson(template-name)
withJson(file_path)
withJson(file_name)
```

## Usage

### ✅  Correct Usage

```js
await spec()
  .post('/api/users')
  .withJson({
    "name": "morpheus",
    "job": "leader"
  })
  .expectStatus(201);
```

## Arguments

#### > payload (object)

Payload is a js object.

#### > file_path (string)

relative path to the json file.

#### > file_name (string)

name of the file. This will looks for a file inside the `./data` folder and any folders nested within it.

::: tip Tip
We can customize the data folder path using [setDataDirectory](/api/settings/setDataDirectory).
:::

## Examples

#### Payload

```js
const { spec } = require('pactum');

await spec()
  .post('https://reqres.in/api/users')
  .withJson({
    "name": "morpheus",
    "job": "leader"
  })
  .expectStatus(201);
```

#### Data Template

```js
const { spec, stash } = require('pactum');

stash.addDataTemplate({
  'USER_PAYLOAD': {
    "name": "morpheus",
    "job": "leader"
  }
});


await spec()
  .post('https://reqres.in/api/users')
  .withJson('USER_PAYLOAD')
  .expectStatus(201);
```

#### File Path

```js
const { spec } = require('pactum');

await spec()
  .post('https://reqres.in/api/users')
  .withJson('./data/user.json')
  .expectStatus(201);
```

#### File Name

```js
const { spec } = require('pactum');

await spec()
  .post('https://reqres.in/api/users')
  .withJson('user.json') // searches for the file inside the data folder
  .expectStatus(201);
```


## See Also

- [withBody](/api/requests/withBody)
- [setDataDirectory](/api/settings/setDataDirectory)