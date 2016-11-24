[Source](https://www.reindex.io/blog/you-might-not-need-underscore/ "Permalink to You Might Not Need Underscore")

# You Might Not Need Underscore

### Arrays [#][15]

#### Iterate [#][16]

* Underscore
    
        _.each(array, iteratee)
    

* ES5.1
    
        array.forEach(iteratee)
    

#### Map [#][17]

* Underscore
    
        _.map(array, iteratee)
    

* ES5.1
    
        array.map(iteratee)
    

#### Use a function to accumulate a single value from an array (left-to-right) [#][18]

* Underscore
    
        _.reduce(array, iteratee, memo)
    

* ES5.1
    
        array.reduce(iteratee, memo)
    

#### Use a function to accumulate a single value from an array (right-to-left) [#][19]

* Underscore
    
        _.reduceRight(array, iteratee, memo)
    

* ES5.1
    
        array.reduceRight(iteratee, memo)
    

#### Test whether all elements in an array pass a predicate [#][20]

* Underscore
    
        _.every(array, predicate)
    
* ES5.1
    
        array.every(predicate)
    

#### Test whether some element in an array passes a predicate [#][21]

* Underscore
    
        _.some(array, predicate)
    

* ES5.1
    
        array.some(predicate)
    

#### Find a value in an array [#][22]

* Underscore
    
        _.find(array, predicate)
    

* ES2015
    
        array.find(predicate)
    

#### Get a property from each element in an array [#][23]

* Underscore
    
        _.pluck(array, propertyName)
    

* ES2015
    
        array.map(value => value[propertyName])
    

#### Check if array includes an element [#][24]

* Underscore
    
        _.includes(array, element)
    

* ES2016
    
        array.includes(element)
    

#### Convert an array-like object to array [#][25]

* Underscore
    
        _.toArray(arguments)
    

* ES2015
    
        [...arguments]
    

#### Convert an array of keys and values to an object [#][26]

* Underscore
    
        _.object(array)
    

* ES2015
    
        array.reduce((result, [key, val]) => Object.assign(result, {[key]: val}), {})
    

* Object Rest/Spread (Stage 2)
    
        array.reduce((result, [key, val]) => {...result, [key]: val}, {})
    

#### Create a copy of an array with all falsy values removed [#][27]

* Underscore
    
        _.compact(array)
    

* ES5.1
    
        array.filter(Boolean)
    

* ES2015
    
        array.filter(x => !!x)
    

#### Create a copy of an array with duplicates removed [#][28]

* Underscore
    
        _.uniq(array)
    

* ES2015
    
        [...new Set(array)]
    

#### Find the index of a value in an array [#][29]

* Underscore
    
        _.indexOf(array, value)
    

* ES5.1
    
        array.indexOf(value)
    

#### Find the index in an array by predicate [#][30]

* Underscore
    
        _.findIndex([4, 6, 7, 12], isPrime);
    

* ES2015
    
        [4, 6, 7, 12].findIndex(isPrime);
    

#### Create an array with n numbers, starting from x [#][31]

* Underscore
    
        _.range(x, x + n)
    

* ES2015
    
        Array.from(Array(n), (_, i) => x + i)
    

### Objects [#][32]

#### Names of own enumerable properties as an array [#][33]

* Underscore
    
        _.keys(object)
    

* ES5.1
    
        Object.keys(object)
    

#### Number of keys in an object [#][34]

* Underscore
    
        _.size(object)
    

* ES5.1
    
        Object.keys(object).length
    


#### Names of all enumerable properties as an array [#][35]

* Underscore
    
        _.allKeys(object)
    

* ES2015
    
        ...Reflect.enumerate(object)]
    

#### Values [#][36]

#### Create a new object with the given prototype and properties [#][37]

* Underscore
    
        _.create(proto, properties)
    

* ES2015
    
        Object.assign(Object.create(proto), properties)
    

#### Create a new object from merged own properties [#][38]

* Underscore
    
        _.assign({}, source, { a: false })
    

* ES2015
    
        Object.assign({}, source, { a: false })
    

* Object Rest/Spread (Stage 2)
    
        { ...source, a: false }
    

#### Create a shallow clone of own properties of an object [#][39]

* Underscore
    
        _.extendOwn({}, object)
    
* Object Rest/Spread (Stage 2)
    
        { ...object }
    

#### Check if an object is an array [#][40]

* Underscore
    
        _.isArray(object)
    

* ES5.1
    
        Array.isArray(object)
    

#### Check if an object is a finite Number [#][41]

* Underscore
    
        _.isNumber(object) && _.isFinite(object)
    

* ES2015
    
        Number.isFinite(object)
    

### Functions [#][42]

#### Bind a function to an object [#][43]

```js
    foo(_.bind(function () {
      this.bar();
    }, this));
    
    foo(_.bind(object.fun, object));
```

* ES2015
```js
    foo(() => {
      this.bar();
    });
    
    foo(() => object.fun());
```

### Utility [#][44]

#### Identity function [#][45]

* Underscore
    
        _.identity
    

* ES2015
    
        value => value
    

#### A function that returns a value [#][46]

* Underscore
    
        _.constant(value)
    

* ES2015
    
        () => value
    

#### The empty function [#][47]

* Underscore
    
        _.noop
    

* ES2015
    
        () => {}
    

#### Get the current time in milliseconds since the epoch [#][48]

* Underscore
    
        _.now()
    

* ES5.1
    
        Date.now()
    

#### Template [#][49]

Did you find a bug? Do you have more examples of things that previously might have required an utility library, but not anymore? [Send us a pull request on GitHub!][50]

[Discuss this post on Hacker News.][51]

[1]: https://www.reindex.io/ES_evolution1.png
[2]: http://underscorejs.org/
[3]: https://lodash.com/
[4]: http://ramdajs.com/
[5]: https://www.youtube.com/watch?v=m3svKOdZijA
[6]: http://2014.jsconf.eu/speakers/sebastian-markbage-minimal-api-surface-area-learning-patterns-instead-of-frameworks.html
[7]: https://babeljs.io/
[8]: https://babeljs.io/docs/learn-es2015/
[9]: https://leanpub.com/understandinges6/read
[10]: https://www.npmjs.com/
[11]: https://www.reindex.io#examples
[12]: https://www.reindex.io#what-do-i-need-to-use-them
[13]: http://babeljs.io/docs/setup/
[14]: https://github.com/es-shims/es5-shim
[15]: https://www.reindex.io#arrays
[16]: https://www.reindex.io#iterate
[17]: https://www.reindex.io#map
[18]: https://www.reindex.io#use-a-function-to-accumulate-a-single-value-from-an-array-left-to-right
[19]: https://www.reindex.io#use-a-function-to-accumulate-a-single-value-from-an-array-right-to-left
[20]: https://www.reindex.io#test-whether-all-elements-in-an-array-pass-a-predicate
[21]: https://www.reindex.io#test-whether-some-element-in-an-array-passes-a-predicate
[22]: https://www.reindex.io#find-a-value-in-an-array
[23]: https://www.reindex.io#get-a-property-from-each-element-in-an-array
[24]: https://www.reindex.io#check-if-array-includes-an-element
[25]: https://www.reindex.io#convert-an-array-like-object-to-array
[26]: https://www.reindex.io#convert-an-array-of-keys-and-values-to-an-object
[27]: https://www.reindex.io#create-a-copy-of-an-array-with-all-falsy-values-removed
[28]: https://www.reindex.io#create-a-copy-of-an-array-with-duplicates-removed
[29]: https://www.reindex.io#find-the-index-of-a-value-in-an-array
[30]: https://www.reindex.io#find-the-index-in-an-array-by-predicate
[31]: https://www.reindex.io#create-an-array-with-n-numbers-starting-from-x
[32]: https://www.reindex.io#objects
[33]: https://www.reindex.io#names-of-own-enumerable-properties-as-an-array
[34]: https://www.reindex.io#number-of-keys-in-an-object
[35]: https://www.reindex.io#names-of-all-enumerable-properties-as-an-array
[36]: https://www.reindex.io#values
[37]: https://www.reindex.io#create-a-new-object-with-the-given-prototype-and-properties
[38]: https://www.reindex.io#create-a-new-object-from-merged-own-properties
[39]: https://www.reindex.io#create-a-shallow-clone-of-own-properties-of-an-object
[40]: https://www.reindex.io#check-if-an-object-is-an-array
[41]: https://www.reindex.io#check-if-an-object-is-a-finite-number
[42]: https://www.reindex.io#functions
[43]: https://www.reindex.io#bind-a-function-to-an-object
[44]: https://www.reindex.io#utility
[45]: https://www.reindex.io#identity-function
[46]: https://www.reindex.io#a-function-that-returns-a-value
[47]: https://www.reindex.io#the-empty-function
[48]: https://www.reindex.io#get-the-current-time-in-milliseconds-since-the-epoch
[49]: https://www.reindex.io#template
[50]: https://github.com/reindexio/youmightnotneedunderscore
[51]: https://news.ycombinator.com/item?id=9794773
