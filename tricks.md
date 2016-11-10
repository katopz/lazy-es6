> Ref : https://engineering.haus.com/dumb-es6-tricks-53ecadd1b29f#.2oczyyizv

### Destructuring
```js
// can destructure arrays
const [a, b] = [1,2]; // a = 1, b = 2

// can destructure object keys
const { a } = { a: 3 }; // a = 3

// even function parameters
function foo({ a }) {
  console.log(a);
}

foo({ a: 4 }); // prints 4
```

### Renaming
```js
const a = 5;
const { a: b } = { a: 3 };

console.log(b); // prints 3

// rename for another API and use enhanced object literal notation.
function verifyUser({ userId: id }) {
  return User.update({ id, verified: true });
}
```

### In conjunction with default parameters
```js
function defaults({ a, b } = { a: 1, b: 2 }) {
  console.log('a', a); 
  console.log('b', b);
}

defaults();
// a 1
// b 2

defaults({ a: 42, b: 32 });
// a 42
// b 32

// be careful with multiple params! The whole right-hand side
// is replaced with your argument!
defaults({ a: 87 });
// a 87
// b undefined
```
Destructuring itself also has default values, so you can intermix the two!
```js
function defaults({ a = 1, b = 2 } = {}) {
  console.log('a', a); 
  console.log('b', b);
}

defaults();
// a 1
// b 2

defaults({ a: 42, b: 32 });
// a 42
// b 32

// multiple params? no problem, just supply the ones you want
defaults({ a: 87 });
// a 87
// b 2
```

### Assigns, branches, and leaves
```js
const {
  topLevel: {
    intermediate,    // declaration of intermediate
    intermediate: {
      nested         // declaration of nested
    }
  }
} = {
  topLevel: {
    intermediate: {
      nested: 56
    }
  }
};

// intermediate is { nested: 56 }
// nested is 56
```
### Idiomatic command line arguments for Node
```js
#!/usr/bin/env node

// skip the first two args as they are node and this script
const [,,filepath] = process.argv;

doSomethingFSLike(filepath);
```
### Enhanced Object Literals
```js
const moment = require('moment');
const PERIOD = moment.duration(3, 'days');

module.exports = {
  PERIOD,
  
  isWithinPeriod(test) {
    return moment().add(PERIOD).isAfter(test);
  },
};

/// meanwhile, in our test file...

const timeUtils = require('./time-utils');

describe('timeUtils.isWithinPeriod', () => {
  it('is false for a time beyond its own defined period', () => {
    const beyondPeriod = moment().add(timeUtils.PERIOD).add(timeUtils.PERIOD);
    assert(timeUtils.isWithinPeriod(beyondPeriod) === false);
  });
});
```
### Object.assign
```js
const original = [0,1,2,3];
const copy = Object.assign([], original, { 2: 42 }); // [0,1,42,3]

console.log(original);
// [ 0, 1, 2, 3 ]

console.log(copy);
// [ 0, 1, 42, 3 ]
```
