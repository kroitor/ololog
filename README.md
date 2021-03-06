# Ololog! <sup>BETA</sup>

- [x] Platform-agnostic logging
- [x] [Colors / styles](https://github.com/xpl/ololog#ansi-styling) for terminals and Chrome DevTools (try [online demo](https://xpl.github.io/ololog/))
- [x] [Displays call locations](https://github.com/xpl/ololog#displaying-call-location)
- [x] [Returns its argument](https://github.com/xpl/ololog#debugging-of-functional-expressions) (for easy debugging of functional expressions)
- [x] [Smart newline / indentation handling](https://github.com/xpl/ololog#smart-newline-handling)
- [x] Formats `Error` instances as [pretty stacktraces with source lines](https://github.com/xpl/ololog#pretty-printing-error-instances)
- [x] [Powerful object printer](https://github.com/xpl/ololog#smart-object-printing)
- [x] [Pluggable architecture](https://github.com/xpl/pipez#pipezbeta)

# Powered by

- [String.ify](https://github.com/xpl/string.ify)
- [ansicolor](https://github.com/xpl/ansicolor)
- [StackTracey](https://github.com/xpl/stacktracey)
- [Pipez](https://github.com/xpl/pipez)

# Importing

```bash
npm install ololog
```
```javascript
log = require ('ololog')
```

# Browser bundle

Exposes global `ololog` and [`ansicolor`](https://github.com/xpl/ansicolor) objects. Installs unsafe [String extensions for ANSI styles](https://github.com/xpl/ansicolor#nice-mode-by-request). Not compressed.

```html
<script src="https://rawgit.com/xpl/ololog/master/build/ololog.browser.js"></script>
<script>
    log = ololog
    log ('something'.red)
</script>
```

# Basic usage

At first, it's similar to `console.log`:

```javascript
log ('foo', 'bar', 'baz') // foo bar baz
```

# Configuration

It exposes a method called `.configure`, which produces a new `log` instance with the new settings applied (not mutating the original one), which can be saved and re-used subsequently:

```javascript
const log = require ('ololog').configure ({ concat: { separator: '' }})
```
```javascript
log ('foo', 'bar', 'baz') // foobarbaz
```

...or you can apply the configuration method _ad-hoc_:

```javascript
log.configure ({ concat: { separator: '' }}) ('foo', 'bar', 'baz') // foobarbaz
```

You can [read more about `configure` here](https://github.com/xpl/pipez#pipezbeta). Configuration engine is implemented as a separate external library, for everyone's use. Contributions are welcome.

# Debugging of functional expressions

Ololog returns its first argument (a feature that `console.log` doesn't have), and it greatly simplifies debugging of functional expressions, as you can simply wrap part of an expression to `log`:

```javascript
array.map (x => log (x) + 1)
```

It is far less ugly than with `console.log`:

```javascript
array.map (x => { console.log (x); return x + 1 })
```

# ANSI styling

![pic](https://cdn.jpg.wtf/futurico/e1/3e/1497133274-e13e9cb97baa90cebbbad55534926fc5.png)

Backed by the [ansicolor](https://github.com/xpl/ansicolor) library, colored output is supported for the terminal environment and for the Chrome DevTools console. On other platforms, ANSI codes are safely stripped from the output, so they don't mess up anything.

Apply styling by calling the `ansicolor` directly:

```javascript
require ('ansicolor').nice // Importing

log (('foo'.dim.red + 'bar'.bgBrightCyan).underline)
```

...or by using the built-in shorthand methods (no need to import `ansicolor`, but we lose the ability to colorize just a part of a string):

```javascript
log.red ('red text')
log.bright.red.underline ('multiple styles combined')
```

[See all the supported styling options here](https://github.com/xpl/ansicolor#supported-styles).

# Smart newline handling

```javascript
log.bright.magenta ('this is something:'.yellow, [ "595da547d9b22f23d8228643", "595da547d9b22f23d822863f", "595da547d9b22f23d8228641" ])
```

![pic](https://cdn.jpg.wtf/futurico/a3/cf/1499313101-a3cf62db303adad169816ce670f43a3b.png)

...and this is how it would look without special caring:

![pic](https://cdn.jpg.wtf/futurico/b1/34/1499313467-b1342c4330146675e9353eddd281006c.png)

Can it handle more arguments?

```javascript
log.bright (
    'this is something:'.yellow, '[ "595da547d9b22f23d8228643",\n  "595da547d9b22f23d822863f",\n  "595da547d9b22f23d8228641"  ]'.cyan,
                                 '[ "595da547d9b22f23d8228643",\n  "595da547d9b22f23d822863f",\n  "595da547d9b22f23d8228641"  ]'.green)
```

![pic](https://cdn.jpg.wtf/futurico/b1/dc/1499313094-b1dc706d06a584059e54106b8376d672.png)

# Smart object printing

All magic is provided by the external [String.ify](https://github.com/xpl/string.ify) library. Read the docs to see all the available configuration options. There are plenty of them! Contributions are welcome.

![GIF animation](http://cdn.jpg.wtf/futurico/13/34/1470446586-13341a275886bd6be2af39e3c24f2f31.gif)

```javascript
let  obj = { abc: 42, defgh: true, qwertyiop: 333, zap: '123457', long: ['foo', 'bar', 'baz', 'qux', 'lol', 'yup'] }
log (obj)
```
```
{       abc:    42,
      defgh:    true,
  qwertyiop:    333,
        zap:   "123457",
       long: [ "foo",
               "bar",
               "baz",
               "qux",
               "lol",
               "yup"  ]  }
```

Disabling fancy formatting:

```javascript
log.noPretty (obj) // or log.configure ({ stringify: { pretty: false } }) (obj)
```
```
{ abc: 42, defgh: true, qwertyiop: 333, zap: "123457", long: ["foo", "bar", "baz", "qux", "lol", "yup"] }
```

Changing max print depth / max array length:

```javascript
log.maxDepth (1).maxArrayLength (100) (...) // or log.configure ({ stringify: { maxDepth: 1, maxArrayLength: 100 } })
```
```javascript
log.unlimited (...) // disables limiting
```

# Pretty printing `Error` instances

This feature is implemented in the [StackTracey](https://github.com/xpl/stacktracey#pretty-printing) library. See it's docs for more (you can configure the path shortening / library calls skipping).

```javascript
const e = new Error ('dummy error') // issued somewhere in a Mocha test callback...

...

log.bright.red (e)
```
```
[EXCEPTION] dummy error
    
        at it                              test.js:104                             const e = new Error ('dummy error')
        at it                              test.js:109                             log.bright.red (e)
        at callFn                          node_modules/mocha/lib/runnable.js:326  var result = fn.call(ctx);                
        at run                             node_modules/mocha/lib/runnable.js:319  callFn(this.fn);                          
        at runTest                         node_modules/mocha/lib/runner.js:422    test.run(fn);                             
        at                                 node_modules/mocha/lib/runner.js:528    self.runTest(function(err) {              
        at next                            node_modules/mocha/lib/runner.js:342    return fn();                              
        at                                 node_modules/mocha/lib/runner.js:352    next(suites.pop());                       
        at next                            node_modules/mocha/lib/runner.js:284    return fn();                              
        at <anonymous>                     node_modules/mocha/lib/runner.js:320    next(0);                                  
        at runCallback                     timers.js:651                                                                     
        at tryOnImmediate                  timers.js:624                                                                     
        at processImmediate [as _immediat  timers.js:596   
```       

# Displaying call location

Have you ever encountered a situation where you need to quickly find in the code the place where the logging is called, but it's not so easy to do? With call location tags it's really easy. And it's enabled by default.

![log message](https://cdn.jpg.wtf/futurico/d6/dd/1493351933-d6dd0c2e633fbb2f886c25c0d8e6f6ad.png)

![call](https://cdn.jpg.wtf/futurico/d6/0c/1493352126-d60cebe41bab9c3d111364ecfc9d2c65.png)

Disabling:

```javascript
log.configure ({ locate: false }) (...)
```

Custom printer:

```javascript
log.configure ({ locate: { print: ({ calleeShort, fileName, line }) => ... } }) (...)
```

Manually setting call location (see the [StackTracey](https://github.com/xpl/stacktracey) library, which serves the purpose):

```javascript
log.configure ({ locate: { where: new StackTracey ().at (2) } }) (...)
```

# Indentation

```javascript
log.configure ({ indent: { level: 3 } }) ('foo\n', 'bar\n', 'baz')  //          foo
                                                                    //          bar
                                                                    //          baz
```

Shorthand method:

```javascript
log.indent (2) ('foo\n', 'bar\n', 'baz')
```

# Timestamping

Disabled by default. To enable:

```javascript
log = log.configure ({ time: true })
```

With indentation:

```javascript
log            ('Lorem ipsum dolor sit amet\nconsectetur adipiscing elit..\n')
log.indent (2) ('Lorem ipsum dolor sit amet\nconsectetur adipiscing elit..\n')
```

![pic](https://cdn.jpg.wtf/futurico/b2/31/1493357342-b2313dd7e25f8606ad7637997ca05fb3.png)

With custom printer:

```javascript
log.configure ({ time: { print: x => (String (x) + ' | ').bright.cyan }}) ('Lorem ipsum dolor sit amet\nconsectetur adipiscing elit..')
```

![pic](https://cdn.jpg.wtf/futurico/93/45/1493357501-9345b20f7edd289e0336bc322f1e68c3.png)

Backdating:

```javascript
log.configure ({ time: { when: new Date ('2017-02-27T12:45:19.951Z') }}) (...)
```

# Specifying additional semantics (errors, warnings, info messages)

You can add `.error` call modifier, which tells Ololog to render with `console.error` instead of `console.log`: 

```javascript
log.error ('this goes to stderr')
```
```javascript
log.bright.red.error ('bright red error!')
```

Under the hood it does the following:

```javascript
log.configure ({ render: { consoleMethod: 'error' } }) ('this goes to stderr')
```

Other `console` methods are supported as well:

```javascript
log.info ('calls console.info')
```
```javascript
log.warn ('calls console.warn')
```

# Limiting max argument length

```javascript
log.configure ({ trim: { max: 5 } }) ('1234567890', 'abcdefgh') // 1234… abcd…
```

# Getting rendered text

The following will execute all stages before the 'render' (screen output) stage, returning its argument:

```javascript
log.before ('render') ({ foo: 42 }) // '{ foo: 42 }'
```

# Custom methods / aspect-oriented code injection

You can add your own shorthand methods, and you can also bind new code to the existing methods, executing it _before_, _after_ or _instead_. See the [pipez](https://github.com/xpl/pipez) library, which provides all the fun.

```javascript
log.methods ({

    indent (level) { return this.configure ({ indent: { level: level }}) }

    get red ()    { return this.configure ({ 'concat+': lines => lines.map (ansicolor.red) }) } // executes it after the 'concat'
    get bright () { return this.configure ({ 'concat+': lines => lines.map (ansicolor.bright) }) }  
})
```

```javascript
log.indent (2).configure ({ time: true }).red.bright ('this is bold red message, indented by 2 and supplied with timestamp')
```

# See also

- [Online demo](https://xpl.github.io/ololog/)
