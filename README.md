# capnp-json
Represent structs from capnp-ts as enumerable json objects. This is designed to work with [capnp-ts](https://npm.im/capnp-ts).

## Installation
`npm i --save capnp-json`  
or  
`yarn add capnp-json`

## Usage
```js
const capnpJson = require('capnp-json');
const MyStructType = require('./schema/mytype.capnp');

// ...
// has a capnp-ts messages
var root = message.getRoot(MyStructType);
var json = capnpJson(root);

// json is now an enumerable json object
// every value is a getter which calls the get method on the original struct
// when a value is fetched it's cached forever, this structure is read only.
console.log(json.someMember);
```

# API

#### `capnpJson(struct)` => `Object`
Scans through all of `struct` and creates a result object containing all of the data found in struct. Each value is added using `Object.defineProperty` such that the value is not parsed from the binary structure until it is actually fetched. It is recursively lazy, as well, in that if a member value is found that is also another `capnp-ts` struct the value returned when fetching it will be another lazy `capnp-json` object.

 * `struct`: (`capnp-ts Struct`): The struct to convert into json.

### Why?
`capnp-ts` does a very poor job at letting you know which properties are available, and unforunately the slowest part of the library is the error handling code. By converting the structs into enumerable json objects we get rid of the ability to call getters which lack underlying data, avoiding these laggy awkward corner cases.

In my specific usecase I brought parsing time of a logfile down from 7 minutes to 7.8 seconds.

# License
MIT
