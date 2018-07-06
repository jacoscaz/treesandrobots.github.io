---
title: Looping over async functions in ES6
description: Exploring a few alternatives for implementing memory-stable control loops in modern ES6 JavaScript
---

Over the last few weeks I've been spending some time exploring the performance limits of using modern server-side JavaScript to write control loops.

With this post, my primary goal is to acquire a general understanding of how long things take in the JavaScript world and how fast I can loop upon an `async` function in **Node.js 10.x**.

## Premise #1: async functions

The `async` and `await` keywords brought to JavaScript by ES6 allow programmers to offload the tedious task of wrapping their code into concatenations of  `.then()` and `.catch()` to the JavaScript compiler / runtime. However, no matter how synchronous their bodies might look, `async`  functions are effectively a frontend to `Promise`-based programming. More on this on [MDN's page about asynchronous functions][mdn-async-functions].

## Premise #2: measuring time intervals

I used Node.js' wonderful `process.hrtime()`  to measure how long things take. More about it on [the official documentation][nodejs-process]. The following function converts time diffs obtained using `hrtime()` to integers of elapsed nanoseconds:

```js
function timeDiffToNanoseconds(diff) {
  return (diff[0] * 1e9) + diff[1];
}
```

## Premise #3: monitoring the latency of the event loop

JavaScript's concurrency model is based on a **single-threaded event loop**. An excellent explanation of what this means can be found on the [MDN's page about concurrency in JavaScript][mdn-event-loop]. Keeping an eye on the latency of such loop is often advisable, particularly so when one is working on performance. The following snippet of code prints the average latency of the event loop to `stdout` roughly every second.

```js
let sum = 0;
let curr = 0;
let prev = process.hrtime();
let ticks = 0;

function samplingLoop() {
  curr = process.hrtime();
  ticks += 1;
  sum += timeDiffToNanoseconds(process.hrtime(prev));
  prev = curr;
  setImmediate(samplingLoop);
}

function outputLoop() {
  console.log(`Event loop latency: ${Math.round(sum / ticks)}`);
  sum = 0;
  ticks = 0;
  setTimeout(outputLoop, 1000);
}

samplingLoop();
outputLoop();
```

In my local environment, a Node.js process running this snippet alone shows an average event loop latency of roughly 2700 nanoseconds, which gives us around 370k loop ticks per second.

## Baseline: synchronous task

In order to obtain a reference level of performance against which to compare further results, I decided to start with measuring how fast a simple synchronous task can run.

```js
function task() {
  return Math.random() + Date.now();
}

const iterations = 1e7;
const before = process.hrtime();

for (let i = 0; i < iterations; i += 1) {
  task();
}

const elapsed = timeDiffToNanoseconds(process.hrtime(before));

console.log(`Avg. task execution time: ${Math.round(elapsed / iterations)}`);
```

Using a late 2013 MacBook Pro with a 2.4GHz Intel Core i5, the average iteration time varied between tens of nanoseconds to tens of *thousands* of nanoseconds, depending on the number of iterations.

| Iterations | Average iteration time (ns) |
| ---------- | --------------------------- |
| 1          | 70k                         |
| 10         | 10k                         |
| 100        | 900                         |
| 1000       | 600                         |
| 10000      | 350                         |
| 100000     | 160                         |
| 1000000    | 95                          |
| 10000000   | 85                          |

Although this is something I would like to confirm and further explore in the future, my best guess is that the logarithmic nature of this correlation is due to dynamic optimizations - such as JIT compilation and instruction caching - taking place as the number of iterations builds up.

At this point I stopped for a second and thought to myself: I'm measuring friggin' _nanoseconds_. How cool is that?

As I am working on control loops, generally supposed to go through as many iterations as possible, I decided to proceed in my tests using 10000000 iterations (ten million). Furthermore, I decided for a reference average iteration time of 100  nanoseconds. In an ideal world where no overhead is required to keep executing a task over and over, this would result in around 10000000 (ten million, again) iterations per second.

## Loop #1: simple and disruptive

Given the asynchronous nature of promises and ES6's [tail call optimization][tail-call-optimization-post], I initially tested the following loop:

```js
async function task() {
  return Math.random() + Date.now();
}

// As per premise #1, this could also be written as
// 
// function task() {
//   return Promise.resolve(Math.random() + Date.now());
// }

function loop(iterations) {
  return new Promise((resolve, reject) => {
    function _loop(i) {
      if (i > 0) {
        task()
          .then(() => _loop(i -= 1))
          .catch(reject);
      } else {
        resolve();
      }
    }
    _loop(iterations);
  });
}

const iterations = 1e7;
const before = process.hrtime();
loop(iterations).then(() => {
  const elapsed = timeDiffToNanoseconds(process.hrtime(before));
  console.log(`Avg. execution time: ${Math.round(elapsed / iterations)}`);
});
```

This first implementation yelded an average time of 250 nanoseconds per iteration, circa 4 million iterations per second. A good start! Plus, even though recursive in nature, this code proved to be surprisingly memory-stable. That, I believe, is due to tail call optimization having been enabled by the use of independent, one-off Promise(s) throughout the recursion. 

Much to my dismay, however, I quickly found out that the above only works as expected if the task requires multiple ticks of the JavaScript event loop to resolve. The reason why this can become an issue is that many asynchronous functions are written in ways that - as far as their relationship with the event loop is concerned - are equivalent to the following:

```js
function task() {
  return Promise.resolve();
}
```

The problem here is that `Promise.resolve()` behaves in a manner that is similar to `process.nextTick()`, resulting in `.then()` callbacks being executed in __the same tick of the event loop in which the task itself is executed__. Therefore, adding the `loop()` function itself as a callback to `task().then()` results in an infinite recursion that, although devoid of memory leaks, effectively prevents the event loop from ever moving forward.

As an example of a task that does not result in such a disruptive behaviour, the following one uses `setImmediate()` to spread its execution across multiple ticks of the event loop:

```js
function wait(delay) {
  return new Promise((resolve) => {
	  setImmediate(resolve);
  });
}

async function task() {           // tick 1
  await wait(100);                // tick 1
  console.log('Hello!');          // tick 2
  await wait(100);                // tick 2
  console.log('Hello again!');    // tick 3
}
```

If you find yourself struggling with the difference between `setImmediate()` and `process.nextTick()` and/or asking your deity why would anyone in their right mind use the name `nextTick` for a method that schedules something to occur during the _current_ tick, I suggest having a look at [this really nice series of posts][nice-posts-on-timers] and at [the official documentation on timers][nodejs-timers].

## Loop #2: the event loop is the only loop

In my next test, I decided to use the JavaScript event loop itself as my task runner by using `setImmediate()` to schedule each new iteration on the next tick.

```js
async function task() {
  return Math.random() + Date.now();
}

function loop(iterations) {
  return new Promise((resolve, reject) => {
    function _loop(i) {
      if (i > 0) {
        task()
          .then(() => {
            setImmediate(() => {
              _loop(i -= 1);
            });
          })
          .catch(reject);
      } else {
        resolve();
      }
    }
    _loop(iterations);
  });
}

const iterations = 1e7;
const before = process.hrtime();
loop(iterations).then(() => {
  const elapsed = timeDiffToNanoseconds(process.hrtime(before));
  console.log(`Avg. execution time: ${Math.round(elapsed / iterations)}`);
});
```

Although this new implementation eliminated the disruptive behaviour of loop #1, it also limited the number of iterations per second to the number of ticks per second of the JavaScript event loop. The average iteration time increased to roughly 3500 nanoseconds - an order of magnitude higher than loop #1. Coherently, the latency of the event loop also went up an order of magnitude, matching the average iteration time. This result surprised me. I was not expecting `setImmediate() ` to increase the average iteration time by a whole order of magnitude.

## Loop #3: a hybrid approach

Although an order of magnitude is usually a great deal, the vast majority of use cases are far enough from the microseconds scale at which I was operating that they would be perfectly served by using loop #2. Furthermore, a control loop is usually related to a number of I/O operations that are guaranteed to scatter execution across multiple ticks of the JavaScript event loop.

However, as I wanted to see how far I could get, I decided to combine loop #1 and loop #2 into a hybrid loop. This new loop uses the potentially blocking behaviour of loop #1 up to a configurable number of executions, using loop #2's approach to let the event loop move onto the next tick every time such a limit is reached.

```js
function loop(iterations) {
  return new Promise((resolve, reject) => {
    function _loop(i) {
      if (i > 0) {
        if (i % 10 === 0) {
          setImmediate(() => {
            _loop(i - 1);
          });
        } else {
          task()
            .then(() => _loop(i - 1))
            .catch(reject);
        }
      } else {
        resolve();
      }
    }
    _loop(iterations);
  });
}
```

Happy with where I was heading, I decided to spend some time testing and refining this idea into something easy to integrate in my projects and (hopefully!) worth sharing. This ultimately resulted in `loopyLoop`, a Node.js package that provides a simple `EventEmitter`-based API for looping over `async` functions. `loopyloop` is available on [GitHub][loopyloop-github] and [NPM][loopyloop-npm].

```js
const LoopyLoop = require('loopyloop');

async function task() {
  return Math.random() + Date.now();
}

const iterations = 1e7;
let i = iterations;

const before = process.hrtime();
const loop = new LoopyLoop(async () => {
  if (i > 0) {
    await task();
    i -= 1;
  } else {
    loop.stop();
  }
})
  .on('stopped', () => {
    const elapsed = timeDiffToNanoseconds(process.hrtime(before));
    console.log(`Avg. execution time: ${Math.round(elapsed / iterations)}`);
  })
  .start();
```

`loopyloop`-powered loop #3 yelded an average iteration time of 750 nanoseconds, around 1.3 million iterations per second, putting it at a bit more than half the speed of loop #1 without the disrupting behaviour of the latter. Surprinsingly, its hybrid approach still managed to hit only one order of magnitude below the reference figure produced by the vastly simpler synchronous code.

## Conclusion: the potential is there

My conclusion is that modern server-side JavaScript using ES6 features can potentially run fast enough to handle basic control loops. With these numbers I feel comfortable in moving to the next step in my path: evaluating I/O performance on a few hardware platforms.

## Addendum: full code

Here's the full, copy-paste-ready code for your enjoyment.

```js
(async () => {

  // ===================================================
  // ====================== UTILS ======================
  // ===================================================

  function timeDiffToNanoseconds(diff) {
    return (diff[0] * 1e9) + diff[1];
  }

  // ===================================================
  // ================ EVENT LOOP LATENCY ===============
  // ===================================================

  const stopSampling = (() => {
    let sum = 0;
    let curr = 0;
    let prev = process.hrtime();
    let ticks = 0;
    let sampleImmediate;
    let outputTimeout;
    function samplingLoop() {
      curr = process.hrtime();
      ticks += 1;
      sum += timeDiffToNanoseconds(process.hrtime(prev));
      prev = curr;
      sampleImmediate = setImmediate(samplingLoop);
    }
    function outputLoop() {
      console.log(`Event loop latency: ${Math.round(sum / ticks)}`);
      sum = 0;
      ticks = 0;
      outputTimeout = setTimeout(outputLoop, 1000);
    }
    function stop() {
      clearTimeout(outputTimeout);
      clearImmediate(sampleImmediate);
    }
    samplingLoop();
    outputLoop();
    return stop;
  })();

  // ===================================================
  // ================ TASK AND SETTINGS ================
  // ===================================================

  const iterations = 1e7;

  function task() {
    return Math.random() + Date.now();
  }

  async function asyncTask() {
    return Math.random() + Date.now();
  }

  // ===================================================
  // ===================== BASELINE ====================
  // ===================================================

  async function testBaseline() {
    const before = process.hrtime();
    for (let i = 0; i < iterations; i += 1) {
      task();
    }
    const elapsed = timeDiffToNanoseconds(process.hrtime(before));
    return Math.round(elapsed / iterations);
  }

  // ===================================================
  // ====================== LOOP #1 ====================
  // ===================================================

  async function testLoop1() {
    function loop(iterations) {
      return new Promise((resolve, reject) => {
        function _loop(i) {
          if (i > 0) {
            asyncTask()
              .then(() => _loop(i -= 1))
              .catch(reject);
          } else {
            resolve();
          }
        }
        _loop(iterations);
      });
    }
    const before = process.hrtime();
    await loop(iterations);
    const elapsed = timeDiffToNanoseconds(process.hrtime(before));
    return Math.round(elapsed / iterations);
  }

  // ===================================================
  // ====================== LOOP #2 ====================
  // ===================================================

  async function testLoop2() {
    function loop(iterations) {
      return new Promise((resolve, reject) => {
        function _loop(i) {
          if (i > 0) {
            asyncTask()
              .then(() => {
                setImmediate(() => {
                  _loop(i -= 1);
                });
              })
              .catch(reject);
          } else {
            resolve();
          }
        }
        _loop(iterations);
      });
    }
    const before = process.hrtime();
    await loop(iterations);
    const elapsed = timeDiffToNanoseconds(process.hrtime(before));
    return Math.round(elapsed / iterations);
  }

  // ===================================================
  // ====================== LOOP #3 ====================
  // ===================================================

  const LoopyLoop = require('loopyloop');

  function testLoop3() {
    return new Promise((resolve, reject) => {
      let i = iterations;
      const before = process.hrtime();
      const loop = new LoopyLoop(async () => {
        if (i > 0) {
          await task();
          i -= 1;
        } else {
          loop.stop();
        }
      })
        .on('error', reject)
        .on('stopped', () => {
          const elapsed = timeDiffToNanoseconds(process.hrtime(before));
          resolve(Math.round(elapsed / iterations));
        })
        .start();
    });
  }

  // ===================================================
  // ===================== RUN TESTS ===================
  // ===================================================

  console.log(`Testing baseline...`);
  console.log(`BASELINE: ${await testBaseline()} nanoseconds per iteration`);
  console.log(`Testing loop #1...`);
  console.log(`LOOP #1: ${await testLoop1()} nanoseconds per iteration`);
  console.log(`Testing loop #2...`);
  console.log(`LOOP #2: ${await testLoop2()} nanoseconds per iteration`);
  console.log(`Testing loop #3...`);
  console.log(`LOOP #3: ${await testLoop3()} nanoseconds per iteration`);
	stopSampling();
})()
  .catch((err) => {
    setImmediate(() => {
      throw err;
    });
  });
```

[nodejs-timers]: https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/
[nodejs-process]: https://nodejs.org/api/process.html
[loopyloop-github]: https://github.com/jacoscaz/node-loopyloop
[loopyloop-npm]: https://www.npmjs.com/package/loopyloop
[mdn-event-loop]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
[mdn-async-functions]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
[nice-posts-on-timers]: https://jsblog.insiderattack.net/promises-next-ticks-and-immediates-nodejs-event-loop-part-3-9226cbe7a6aa
[tail-call-optimization-post]: https://medium.com/@dai_shi/tail-call-optimization-tco-in-node-v6-e2492c9d5b7c

