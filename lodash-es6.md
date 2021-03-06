[Source](https://www.sitepoint.com/lodash-features-replace-es6/ "Permalink to 10 Lodash Features You Can Replace with ES6")

# 10 Lodash Features You Can Replace with ES6

###  More from this author 

* [Quick Tip: What Are Factory Functions in JavaScript][1]
* [Understanding ASTs by Building Your Own Babel Plugin][2]

_This article was peer reviewed by [Mark Brown][3]. Thanks to all of SitePoint's peer reviewers for making SitePoint content the best it can be!_

[Lodash][4] is the [most depended on npm package][5] right now, but if you're using ES6, you might not actually need it. In this article, we're going to look at using native collection methods with arrow functions and other new ES6 features to help us cut corners around many popular use cases.

## 1\. Map, Filter, Reduce

These collection methods make transforming data a breeze and with near universal support, we can pair them with arrow functions to help us write terse alternatives to the implementations offered by Lodash.

```js
_.map([1, 2, 3], function(n) { return n * 3; });
// [3, 6, 9]
_.reduce([1, 2, 3], function(total, n) { return total + n; }, 0);
// 6
_.filter([1, 2, 3], function(n) { return n <= 2; });
// [1, 2]

// becomes

[1, 2, 3].map(n => n * 3);
[1, 2, 3].reduce((total, n) => total + n);
[1, 2, 3].filter(n => n <= 2);
```

It doesn't stop here either, if we're using an ES6 polyfill, we can also use [find][6], [some][7], [every][8] and [reduceRight][9] too.

## 2\. Head &amp; Tail

[Destructuring syntax][10] allows us to get the head and tail of a list without utility functions.

```js
_.head([1, 2, 3]);
// 1
_.tail([1, 2, 3]);
// [2, 3]

// becomes

const [head, ...tail] = [1, 2, 3];
```

It's also possible to get the initial elements and the last element in a similar way.

```js
_.initial([1, 2, 3]);
// -> [1, 2]
_.last([1, 2, 3]);
// 3

// becomes

const [last, ...initial] = [1, 2, 3].reverse();
```

If you find it annoying that [reverse][11] mutates the data structure, then you can use the spread operator to clone the array before calling reverse.

```js
const xs = [1, 2, 3];
const [last, ...initial] = [...xs].reverse();
```

## 3\. Rest &amp; Spread

The [rest][12] and [spread][13] functions allow us to define and invoke functions that accept a variable number of arguments. ES6 introduced dedicated syntaxes for both of these operations.

```js
var say = _.rest(function(what, names) {
  var last = _.last(names);
  var initial = _.initial(names);
  var finalSeparator = (_.size(names) > 1 ? ', &amp; ' : '');
  return what + ' ' + initial.join(', ') +
finalSeparator + _.last(names);
});

say('hello', 'fred', 'barney', 'pebbles');
// "hello fred, barney, &amp; pebbles"

// becomes

const say = (what, ...names) => {
  const [last, ...initial] = names.reverse();
  const finalSeparator = (names.length > 1 ? ', &amp;' : '');
  return `${what} ${initial.join(', ')} ${finalSeparator} ${last}`;
};

say('hello', 'fred', 'barney', 'pebbles');
// "hello fred, barney, &amp; pebbles"
```

## 4\. Curry

Without a higher level language such as [TypeScript][14] or [Flow][15], we can't give our functions type signatures which makes [currying][16] quite difficult. When we receive curried functions it's hard to know how many arguments have already been supplied and which we will need to provide next. With arrow functions we can define curried functions explicitly, making them easier to understand for other programmers.

```js
function add(a, b) {
  return a + b;
}
var curriedAdd = _.curry(add);
var add2 = curriedAdd(2);
add2(1);
// 3

// becomes

const add = a => b => a + b;
const add2 = add(2);
add2(1);
// 3
```

These explicitly curried arrow functions are particularly important for debugging.

```js
var lodashAdd = _.curry(function(a, b) {
  return a + b;
});
var add3 = lodashAdd(3);
console.log(add3.length)
// 0
console.log(add3);
//function wrapper() {
//  var length = arguments.length,
//  args = Array(length),
//  index = length;
//
//  while (index--) {
//args[index] = arguments[index];
//  }…

// becomes

const es6Add = a => b => a + b;
const add3 = es6Add(3);
console.log(add3.length);
// 1
console.log(add3);
// function b => a + b
```

If we're using a functional library like [lodash/fp][17] or [ramda][18] then we can also use arrows to remove the need for the auto-curry style.

```js
_.map(_.prop('name'))(people);

// becomes

people.map(person => person.name);
```

## 5\. Partial

Like with currying, we can use arrow functions to make partial application easy and explicit.

```js
var greet = function(greeting, name) {
  return greeting + ' ' + name;
};

var sayHelloTo = _.partial(greet, 'hello');
sayHelloTo('fred');
// "hello fred"

// becomes

const sayHelloTo = name => greet('hello', name);
sayHelloTo('fred');
// "hello fred"
```

It's also possible to use rest parameters with the spread operator to partially apply variadic functions.


const sayHelloTo = (name, ...args) => greet('hello', name, ...args);
sayHelloTo('fred', 1, 2, 3);
// "hello fred"


## 6\. Operators

Lodash comes with a number of functions that reimplement syntactical operators as functions, so that they can be passed to collection methods.

In most cases, arrow functions make them simple and short enough that we can define them inline instead.

```js
_.eq(3, 3);
// true
_.add(10, 1);
// 11
_.map([1, 2, 3], function(n) {
  return _.multiply(n, 10);
});
// [10, 20, 30]
_.reduce([1, 2, 3], _.add);
// 6

// becomes

3 === 3
10 + 1
[1, 2, 3].map(n => n * 10);
[1, 2, 3].reduce((total, n) => total + n);
```

## 7\. Paths

Many of Lodash's functions take paths as strings or arrays. We can use arrow functions to create more reusable paths instead.

```js
var object = { 'a': [{ 'b': { 'c': 3 } }, 4] };

_.at(object, ['a[0].b.c', 'a[1]']);
// [3, 4]
_.at(['a', 'b', 'c'], 0, 2);
// ['a', 'c']

// becomes

[
  obj => obj.a[0].b.c,
  obj => obj.a[1]
].map(path => path(object));

[
  arr => arr[0],
  arr => arr[2]
].map(path => path(['a', 'b', 'c']));
```

Because these paths are "just functions", we can compose them too.

```js
const getFirstPerson = people => people[0];
const getPostCode = person => person.address.postcode;
const getFirstPostCode = people => getPostCode(getFirstPerson(people));
```

We can even make higher order paths that accept parameters.


const getFirstNPeople = n => people => people.slice(0, n);

const getFirst5People = getFirstNPeople(5);
const getFirst5PostCodes = people => getFirst5People(people).map(getPostCode);


## 8\. Pick

The [pick][19] utility allows us to select the properties we want from a target object. We can achieve the same results using destructuring and shorthand object literals.

```js
var object = { 'a': 1, 'b': '2', 'c': 3 };

return _.pick(object, ['a', 'c']);
// { a: 1, c: 3 }

// becomes

const { a, c } = { a: 1, b: 2, c: 3 };

return { a, c };
```

## 9\. Constant, Identity, Noop

Lodash provides some utilities for creating simple functions with a specific behaviour.

```js
_.constant({ 'a': 1 })();
// { a: 1 }
_.identity({ user: 'fred' });
// { user: 'fred' }
_.noop();
// undefined
```

We can define all of these functions inline using arrows.

```js
const constant = x => () => x;
const identity = x => x;
const noop = () => undefined;
```

Or we could rewrite the example above as:

```js
(() => ({ a: 1 }))();
// { a: 1 }
(x => x)({ user: 'fred' });
// { user: 'fred' }
(() => undefined)();
// undefined
```

## 10\. Chaining &amp; Flow

Lodash provides some functions for helping us write chained statements. In many cases the built-in collection methods return an array instance that can be directly chained, but in some cases where the method mutates the collection, this isn't possible.

However, we can define the same transformations as an array of arrow functions.

```js
_([1, 2, 3])
 .tap(function(array) {
   // Mutate input array.
   array.pop();
 })
 .reverse()
 .value();
// [2, 1]

// becomes

const pipeline = [
  array => { array.pop(); return array; },
  array => array.reverse()
];

pipeline.reduce((xs, f) => f(xs), [1, 2, 3]);
```

This way, we don't even have to think about the difference between [tap][20] and [thru][21]. Wrapping this reduction in a utility function makes a great general purpose tool.

```js
const pipe = functions => data => {
  return functions.reduce(
    (value, func) => func(value),
    data
  );
};

const pipeline = pipe([
  x => x * 2,
  x => x / 3,
  x => x > 5,
  b => !b
]);

pipeline(5);
// true
pipeline(20);
// false
```

## Conclusion

Lodash is still a great library and this article only offers a fresh perspective on how the evolved version of JavaScript is allowing us to solve some problems in situations where we would have previously relied on utility modules.

Don't disregard it, but instead—next time you reach for an abstraction—think about whether a simple function would do instead!

[1]: https://www.sitepoint.com/factory-functions-javascript/?utm_source=sitepoint&utm_medium=relatedinline&utm_term=&utm_campaign=relatedauthor
[2]: https://www.sitepoint.com/understanding-asts-building-babel-plugin/?utm_source=sitepoint&utm_medium=relatedinline&utm_term=&utm_campaign=relatedauthor
[3]: https://www.sitepoint.com/author/mbrown
[4]: https://lodash.com/
[5]: https://www.npmjs.com/browse/depended
[6]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/find
[7]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some
[8]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/every
[9]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduceRight
[10]: https://www.sitepoint.com/preparing-ecmascript-6-destructuring-assignment/
[11]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse
[12]: https://lodash.com/docs#rest
[13]: https://lodash.com/docs#spread
[14]: http://www.typescriptlang.org
[15]: http://flowtype.org/
[16]: https://www.sitepoint.com/currying-in-functional-javascript/
[17]: https://github.com/lodash/lodash/wiki/FP-Guide
[18]: http://ramdajs.com
[19]: https://lodash.com/docs#pick
[20]: https://lodash.com/docs#tap
[21]: https://lodash.com/docs#thru
