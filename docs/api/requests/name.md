# name

name method is used to uniquely identify the current test case name which can be used in snapshot testing.

```js
name(test-name)
```

- `test-name` (**string**) - unique name for the test.

## Usage

### ✅  Correct Usage

```js
await spec()
  .name('get a user')
  .get('/api/users/1')
  .expectStatus(200);
```

## Examples

### Normal

```js
const { spec } = require('pactum');

await spec()
  .name('get a user')
  .get('https://reqres.in/api/users/1')
  .expectStatus(200);
```

## See Also

- [expectJsonSnapshot](/api/assertions/expectJsonSnapshot)