# lazy-es6
Lazy copy and paste es6

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
