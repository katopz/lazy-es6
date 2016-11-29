
[Source](https://thomashunter.name/blog/the-long-road-to-asyncawait-in-javascript/ "Permalink to The long road to Async/Await in JavaScript")

# The long road to Async/Await in JavaScript

This is a comparison of different methods for performing asynchronous control flow in JavaScript, specifically&nbsp;**Callbacks**, **Promises**, **Generators** / **Yields** (ES6), and **Async** / **Await** (ES7).&nbsp;To follow along be sure you understand how the [JavaScript Event Loop][1] works and what it means&nbsp;when code is executed synchronously in the current stack, or shoved into the queue to be executed asynchronously in the future.

In the following contrived examples, `publishLevel()` is our main application code (perhaps something we'd see in a **Controller**), whereas&nbsp;`getUser()`, `canCreate()`, and `saveLevel()` are functions&nbsp;nested deeper in our application (perhaps in our **Model**s).

**Compatibility:&nbsp;**Keep in mind that anything marked as **ES6** will require Node.js &gt;= 0.12 with the `\--harmony` flag enabled, or any version of io.js &gt;= 1.0. [Browser support for ES6][2] is anything but spectacular and you'll likely need&nbsp;to transpile. Anything marked as **ES7** will definitely require a transpile for either Browser or Node.js.

## Stage 0: Synchronous&nbsp;Code

This of course is not Asynchronous code&nbsp;but&nbsp;it does show us&nbsp;eloquent syntax. If you're used to writing applications in, say, PHP, all of your code looks like this. This is the cleanest&nbsp;way one can write code and have it execute sequentially.

In the publishLevel() function we execute a function, get the result, pass it to the next function, run a branch, all in the current stack, all using minimal syntax.
    
    
    var level_result = publishLevel(12, {data: true});
    console.log(level_result);
    
    function publishLevel(user_id, level_data) {
      var user = getUser(user_id);
      var can_create = canCreate(user);
    
      if (!can_create) {
        return null;
      }
    
      var level = saveLevel(user, level_data);
    
      return level;
    }
    
    function getUser(user_id) {
      return {
        id: user_id,
        nickname: 'tlhunter'
      };
    }
    
    function canCreate(user) {
      return user.id === 12;
    }
    
    function saveLevel(user, data) {
      return {
        id: 100,
        owner: user.nickname,
        data: data
      };
    }

## Stage 1: Callbacks

This is the defacto approach&nbsp;used in&nbsp;Node.js applications where sequential&nbsp;asynchronous operations need to happen. As more function calls need to happen your code starts to nest even deeper. This phenomenon is affectionately known as callback hell.

What happens if you want to add another operation in the middle? You've got to re-nest all subsequent tasks within the new operation. Over time&nbsp;your code begins&nbsp;to flare outward, and those git-blame's start to lie.
    
    
    publishLevel(12, {data: true}, function(level_result) {
      console.log(level_result);
    });
    
    function publishLevel(user_id, level_data, cb) {
      getUser(user_id, function(user) {
        canCreate(user, function(can_create) {
          if (!can_create) {
            return cb(null);
          }
          saveLevel(user, level_data, function(level) {
            cb(level);
          });
        });
      });
    }
    
    function getUser(user_id, cb) {
      setTimeout(function() {
        cb({
          id: user_id,
          nickname: 'tlhunter'
        });
      }, 100);
    }
    
    function canCreate(user, cb) {
      setTimeout(function() {
        cb(user.id === 12);
      }, 100);
    }
    
    function saveLevel(user, data, cb) {
      setTimeout(function() {
        cb({
          id: 100,
          owner: user.nickname,
          data: data
        });
      }, 100);
    }

## Stage 2: Promises (ES6&nbsp;or&nbsp;ES5 + Polyfill)

This is the approach used by a large codebase I've been working with lately. While we don't get Promises until ES6, ES5 can use them thanks to polyfills such as [Bluebird][3].

The benefit of using Promises is&nbsp;we don't need to continually nest functions, and adding new work in the middle is as simple as adding a few extra lines.

In my opinion the syntax is a bit ugly. Chaining .then() calls from previous executions and calling&nbsp;the next bit of asynchronous work within the same function just doesn't feel right.
    
    
    publishLevel(12, {data: true}).then(function(level_result) {
      console.log(level_result);
    });
    
    function publishLevel(user_id, level_data, cb) {
      var user = null;
      return getUser(user_id).then(function(_user) {
        user = _user;
        return canCreate(_user);
      }).then(function(can_create) {
        if (!can_create) {
          return null;
        }
    
        return saveLevel(user, level_data);
      });
    }
    
    function getUser(user_id) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: user_id,
            nickname: 'tlhunter'
          });
        }, 100);
      });
    }
    
    function canCreate(user) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve(user.id === 12);
        }, 100);
      });
    }
    
    function saveLevel(user, data) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: 100,
            owner: user.nickname,
            data: data
          });
        }, 100);
      });
    }

Promises everywhere is certainly more fun than Promises somewhere. If you're planning on heavily using Promises with Node.js, check out&nbsp;[mz,][4]&nbsp;which will Promisify the Node.js API.

## Stage 3: Generators/Yields (ES6)

ES6 gives us [Generator][5] functions which we can **yield**. When a function yields it is temporarily paused while the caller gets to do something with the yielded value. These were designed with&nbsp;doing iteration-based tasks in mind, yielding simple values, but here we're going to yield Promises!

This is the first time&nbsp;we see code able to get executed in a different stack&nbsp;yet exist within the same function scope. Of course this new paradigm requires a new syntax. Generator functions have a&nbsp;*****&nbsp;in their declaration, and we make use of the&nbsp;new&nbsp;**yield** keyword.

These&nbsp;_can_ be used for doing control flow, but it's really intended for iteration work, as you'll see in this next example where I run a whole bunch of ugly code to manually keep the generator alive:
    
    
    var generator = publishLevel(12, {data: true});
    
    generator.next().value.then(function(user) {
       return generator.next(user).value.then(function(can_create) {
         return generator.next(can_create).value.then(function(level_result) {
           console.log(level_result);
         });
       });
     });
    
    function * publishLevel(user_id, level_data) {
      var user = yield getUser(user_id);
      var can_create = yield canCreate(user);
    
      if (!can_create) {
        return null;
      }
    
      var level = yield saveLevel(user, level_data);
    
      return level;
    }
    
    function getUser(user_id) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: user_id,
            nickname: 'tlhunter'
          });
        }, 100);
      });
    }
    
    function canCreate(user) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve(user.id === 12);
        }, 100);
      });
    }
    
    function saveLevel(user, data) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: 100,
            owner: user.nickname,
            data: data
          });
        }, 100);
      });
    }

Notice this intimate knowledge we need to know about the function we're calling! We take the result, pass it into another `generator.next()` call as an argument (this becomes the result of the yielded assignment).

Objects returned by generators have a `.next()` method, with a `.done` and `.value` attribute. If done is true, then the generators work is finished&nbsp;(the final return), however if done is false then there's more work to happen&nbsp;(the preceding yield's).&nbsp;The calling function is given the intermediate yield values, and has to know to continue the execution of the generator. While this is great for doing iteration work, it's tedious&nbsp;from the perspective of doing asynchronous control flow.

## Stage 4: Async/Await (ES7)

Async / Await is amazing, the mecca of working with asynchronous code in JavaScript. Personally I think it's a shame we got Generators in ES6 instead of this. The solution is so eloquent that it will forever change the way we write JavaScript.

Internally it works with Promises. When the promise returned by an await&nbsp;resolves the code in the function will continue executing, and the resolved value will be provided.
    
    
    publishLevel(12, {data: true}).then(function(level_data) {
      console.log(level_data);
    });
    
    async function publishLevel(user_id, level_data) {
      var user = await getUser(user_id);
      var can_create = await canCreate(user);
    
      if (!can_create) {
        return null;
      }
    
      var level = await saveLevel(user, level_data);
    
      return level;
    }
    
    function getUser(user_id) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: user_id,
            nickname: 'tlhunter'
          });
        }, 100);
      });
    }
    
    function canCreate(user) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve(user.id === 12);
        }, 100);
      });
    }
    
    function saveLevel(user, data) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: 100,
            owner: user.nickname,
            data: data
          });
        }, 100);
      });
    }

You can transpile this down to ES5 today using Babel:
    
    
    $ npm install babel-runtime
    $ babel --stage=1 --optional runtime input.es7.js &gt; output.es5.js

What does this code look like after transpilation?
    
    
    'use strict';
    
    var _regeneratorRuntime = require('babel-runtime/regenerator')['default'];
    
    var _Promise = require('babel-runtime/core-js/promise')['default'];
    
    publishLevel(12, { data: true }).then(function (level_data) {
      console.log(level_data);
    });
    
    function publishLevel(user_id, level_data) {
      var user, can_create, level;
      return _regeneratorRuntime.async(function publishLevel$(context$1$0) {
        while (1) switch (context$1$0.prev = context$1$0.next) {
          case 0:
            context$1$0.next = 2;
            return _regeneratorRuntime.awrap(getUser(user_id));
    
          case 2:
            user = context$1$0.sent;
            context$1$0.next = 5;
            return _regeneratorRuntime.awrap(canCreate(user));
    
          case 5:
            can_create = context$1$0.sent;
    
            if (can_create) {
              context$1$0.next = 8;
              break;
            }
    
            return context$1$0.abrupt('return', null);
    
          case 8:
            context$1$0.next = 10;
            return _regeneratorRuntime.awrap(saveLevel(user, level_data));
    
          case 10:
            level = context$1$0.sent;
            return context$1$0.abrupt('return', level);
    
          case 12:
          case 'end':
            return context$1$0.stop();
        }
      }, null, this);
    }
    
    function getUser(user_id) {
      return new _Promise(function (resolve) {
        setTimeout(function () {
          resolve({
            id: user_id,
            nickname: 'tlhunter'
          });
        }, 100);
      });
    }
    
    function canCreate(user) {
      return new _Promise(function (resolve) {
        setTimeout(function () {
          resolve(user.id === 12);
        }, 100);
      });
    }
    
    function saveLevel(user, data) {
      return new _Promise(function (resolve) {
        setTimeout(function () {
          resolve({
            id: 100,
            owner: user.nickname,
            data: data
          });
        }, 100);
      });
    }

I'm not sure how performant this code is. Certainly once JavaScript engines natively support Async / Await&nbsp;it'll be fast, but the output from Babel I'm not too sure.

## Stage 3.5: Generators/Yields&nbsp;+ co (ES6)

I put this one out of order so you'd first see how awesome Async / Await is, and what the [co][6] module&nbsp;attempts to emulate.

Async / Await is great&nbsp;but we can't use it today without complex&nbsp;transpilations! Generators are neat, but they require manual executions of `.next()`! Luckily there's a sweet library called **co** which can sort of provide us the best of both worlds (transpile-free if you're running modern Node). It'll run `.next()` for us, and if we return Promises, when they resolve the generator will continue. Finally once the final return is called, that value is the final resolved value of the co-wrapped generator.
    
    
    var co = require('co');
    
    publishLevel(12, {data: true}).then(function(level_data) {
      console.log(level_data);
    });
    
    function publishLevel(user_id, level_data) {
      return co(function * publishLevel() {
        var user = yield getUser(user_id);
        var can_create = yield canCreate(user);
    
        if (!can_create) {
          return null;
        }
    
        var level = yield saveLevel(user, level_data);
    
        return level;
      });
    }
    
    function getUser(user_id) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: user_id,
            nickname: 'tlhunter'
          });
        }, 100);
      });
    }
    
    function canCreate(user) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve(user.id === 12);
        }, 100);
      });
    }
    
    function saveLevel(user, data) {
      return new Promise(function(resolve) {
        setTimeout(function() {
          resolve({
            id: 100,
            owner: user.nickname,
            data: data
          });
        }, 100);
      });
    }

The solution isn't as eloquent as the Async/Await version. We need an additional function level and a library. But overall it's a nice alternative to nested callbacks or verbose Promises.

## tl;dr

* **Stage 1: Callbacks** shows us how nested callbacks give us control over Asynchronous code 
    * Pro's: ES5, Simple to understand, Node.js API works this way
    * Con's: Messy refactors, code nesting levels jump around
* **Stage 2: Promises** gives us the power of callbacks but keeps code from getting out of hand 
    * Pro's: ES5&nbsp;with polyfill, nesting is under control
    * Con's: Verbose syntax
* **Stage 3: Generators/Yields** describes generators and how they can get a bit messy 
    * Pro's:&nbsp;Works great with iterators, parts of functions can be executed in future
    * Con's: ES6 or transpile, painful to manually manage execution
* **Stage 3.5: Generators/Yields + co** is a great solution you can use today 
    * Pro's: The advantage of using Generators without manually managing yields
    * Con's: ES6 or transpile
* **Stage 4: Async/Await** is an amazing solution you can use tomorrow 
    * Pro's: Eloquent syntax
    * Con's: ES7 currently requires transpile regardless of environment

Pick your poison!

[1]: https://thomashunter.name/blog/the-javascript-event-loop-presentation/
[2]: https://kangax.github.io/compat-table/es6/
[3]: https://github.com/petkaantonov/bluebird
[4]: https://github.com/normalize/mz
[5]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_Generators
[6]: https://github.com/tj/co
[7]: https://secure.gravatar.com/avatar/9171705459b4dfd18c8badc3cf50acf9?s=80&amp;d=retro&amp;r=x
[8]: https://twitter.com/tlhunter
[9]: https://thomashunter.name/blog/author/tlhunter/

  
