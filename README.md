# lazy-es6
Lazy copy and paste es6

### Array
- https://hackernoon.com/what-you-should-know-about-es6-arrays-34b2a166aaed#.hhf1jx9w5

### Cheats sheet
- https://github.com/DrkSephy/es6-cheatsheet

### Date
```js
// timestamp, equal to new Date().valueOf()
+new Date()
```

### Delay
```js
// Will delay for (ms) millisecond and return promise
var delay = ms => new Promise(resolve => setTimeout(resolve, ms));

// Example : Delay for 1000ms and log 'foo'
delay(1000).then(() => console.log('foo'));
```
### Required text
```js
// Will enabled require txt
require.extensions['.txt'] = (module, filename) => module.exports = require('fs').readFileSync(filename, 'utf8')

// Example : Load `foo.txt` as String
const foo = require('foo.txt')
```
