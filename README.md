# Benny - handy benchmarking tool based on

![CircleCI](https://img.shields.io/circleci/build/github/caderek/redux-multimethod)
![David](https://img.shields.io/david/caderek/redux-multimethod)
![Codecov](https://img.shields.io/codecov/c/github/caderek/redux-multimethod)
![npm bundle size](https://img.shields.io/bundlephobia/minzip/redux-multimethod)
[![GitHub license](https://img.shields.io/github/license/caderek/redux-multimethod)](https://github.com/caderek/redux-multimethod/blob/master/LICENSE)

## Overview

Benny is a wrapper for [benchmark](https://www.npmjs.com/package/benchmark) package.

It provides an improved API that allows you to:

- prepare local setup for each case
- skip or run only selected cases
- save essential results to a file in JSOn format

## Quick example

```js
/* benchmark.js */
const b = require('benny').default

b.suite(
  b.add('Reduce two elements', () => {
    ;[1, 2].reduce((a, b) => a + b)
  }),

  b.add('Reduce five elements', () => {
    ;[1, 2, 3, 4, 5].reduce((a, b) => a + b)
  }),

  b.cycle(),
  b.complete(),
  b.save({ file: 'reduce', version: '1.0.0' }),
  b.run(),
)
```

Execute:

```sh
node benchmark.js
```

Output:

```sh
Reduce two elements:
  147 859 561 ops/s, ±0.82%
Reduce five elements:
  119 060 793 ops/s, ±1.01%
Finished 2 cases, fastest: Reduce two elements
Saved to: benchmark/results/reduce.json
```

File content:

```json
{
  "date": "2019-09-27T00:51:40.230Z",
  "version": "1.0.0",
  "results": [
    {
      "name": "Reduce two elements",
      "ops": 147859561,
      "deviation": 0.82
    },
    {
      "name": "Reduce five elements",
      "ops": 119060793,
      "deviation": 1.01
    }
  ]
}
```

## API

```js
/* benchmark.js */
const { add, complete, cycle, run, save, suite } = require('benny')
// Or use other import methods:
// const benny = require('benny').default
// import { add, complete, cycle, run, save, suite } from 'benny'
// import benny from 'benny'

suite(
  // If the code that you want to benchmark has no setup,
  // you can run it directly:
  add('My first case', () => {
    myFunction()
  }),

  // If the code that you want to benchmark requires setup,
  // you should return it wrapped in function:
  add('My second case', () => {
    // Example setup:
    const testArr = Array.from({ length: 1000 }, (_, index) => index)

    // Benchmarked code wrapped in function:
    return () => myOtherFunction(testArr)
  }),

  // This benchmark will be skipped:
  add.skip('My third case', () => {
    1 + 1
  }),

  // This benchmark will be the only one that runs
  // (unless there are other cases marked by .only)
  add.only('My fourth case', () => {
    Math.max(1, 2, 3, 4, 5)
  }),

  // This will run when each case is benchmarked.
  // You can pass a function that takes an event with current result.
  // By default it pretty-prints case summary
  cycle(),

  // This will run when all cases are benchmarked.
  // You can pass a function that takes an event with all results.
  // By default it prints simple summary.
  complete(),

  // This will save essential results to a file.
  // You can pass an options object.
  // By default saves to benchmark/results/<ISO-DATE-TIME>.json
  save({
    // String or function that produces a string,
    // if function, then result event will be passed as argument:
    file: 'myFileNameWithoutExtension'
    // Destination folder (can be nested), will be created if not exists:
    folder: 'myFolder',
    // Version string - if provided will be added to the default file name
    // and included in the file content
    version: require('package.json').version,
  }),


  // Run the suite
  run()
)
```

## License

Project is under open, non-restrictive [ISC license](LICENSE).
