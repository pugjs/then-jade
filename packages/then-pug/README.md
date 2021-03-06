# then-pug

**WARNING**: This is a work-in-progress port of `then-jade` to `pug`. Expect it to break at any time until this warning is removed. The documentation is not up to date.


Jade template engine with async/streaming support via promises and generators.

The Jade template engine is a templating engine with a 2 stage process :
 * stage 1: synchronous compilation of a template, leading to a rendering function
 * stage 2: synchronous rendering of compiled template using user defined `locals`

`then-jade` makes sure that the rendering stage of the process (stage 2) becomes asynchronous: 
 * Generators and Promises can be used in the user defined `locals` and the rendering will be progressive, waiting asynchronously for the resolution of the promises
 * The rendered output is made available as a ReadableStream with back-pressure implemented, so the rendering will only be done as fast as the downstream WritableStream can handle it

It is not the goal of `then_jade` to modify the compilation phase of the process (stage 1). This means for example that extends & includes will continue to be loaded synchronously by jade.

Note: `jade` was frozen and deprecated in version 1.11.0 because of trademark issues. It was replaced by `pug` which has a new code architecture. `then-jade` is currently frozen and creating a `then-pug` module is only a very low priority task for the maintainers.

[![Build Status](https://travis-ci.org/pugjs/then-jade.png?branch=master)](https://travis-ci.org/pugjs/then-jade)
[![Dependency Status](https://gemnasium.com/jadejs/then-jade.png)](https://gemnasium.com/pugjs/then-jade)
[![NPM version](https://img.shields.io/npm/v/then-jade.svg)](https://www.npmjs.com/package/then-jade)

## Installation

    npm install then-jade

## API

Before all examples, you will need:

```js
var thenJade = require('then-jade');
```

### compile(str, options)

Compiles a string containing a jade template into a rendering function.

```js
var fn = thenJade.compile(str, options);
```

The rendering function can be called as `fn(locals, callback)` where `locals` are the user defined variables or generator/promises that can be used in the template. `callback` is a node-style `callback(err, res)` that will be called with the rendered template once the rendering is finished or with an error if something went wrong.

For `options`, see the documentation for `compileStreaming`


### compileStreaming(str, options)

Compiles a string containing a jade template into a rendering function.

```js
var fn = thenJade.compileStreaming(str, options);
```

The rendering function can be called as `fn(locals)` where `locals` are the user defined variables or generator/promises that can be used in the template. The result of calling this function is a ReadableStream that will stream the rendered template.

### render(str, options, callback)

* Render the given `str` of jade.

If the function is called with an optional node-style `callback`, the callback
will be called with the result when the rendering is finished.

Otherwise, the function will return a Promise that will be fulfilled when
the rendering is finished.

Options:
  - `filename` filename required for `include` / `extends`

### renderStreaming(str, options)

Render the given `str` of jade

`options`:
  - `filename` filename required for `include` / `extends`

`options` are used as `locals` at rendering time.

The result is a ReadableStream wrapping the asynchronous rendering of the template.
This stream can be piped into a WritableStream.

### renderFile(path, options, callback)

Render the given `path` file containing a jade.
The compilation of the file is synchronous and uses an internal in-memory cache
to avoid re-compiling the same file twice.
The rendering is asynchronous.

If the function is called with an optional node-style `callback`, the callback
will be called with the result when the rendering is finished.

Otherwise, the function will return a Promise that will be fulfilled when
the rendering is finished.

`options` are used as `locals` at rendering time.

### renderFileStreaming(path, options) 

Render the given `path` file containing a jade.
The compilation of the file is synchronous and uses an internal in-memory cache
to avoid re-compiling the same file twice.
The rendering is asynchronous.

The result is a ReadableStream wrapping the asynchronous rendering of the template.
This stream can be piped into a WritableStream.

`options` are used as `locals` at rendering time.

## License

  MIT
