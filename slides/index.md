- title : Fable, the compiler that emits JS you can be proud of!
- description : Introduction to Fable, F# to JS compiler
- author : Alfonso Garcia-Caro
- theme : night
- transition : default


***


### Fable, the compiler that emits JS you can be proud of! 
Bringing together the F# and JS worlds

<div>
  <img style="display: inline-block;vertical-align:middle" src="images/fsharp.png" />
  <span style="white-space: pre">   meets   </span>
  <img style="display: inline-block;vertical-align:middle" src="images/js.png" />
</div>

---


### ABOUT ME

Alfonso Garcia-Caro

- Author of Fable
- Degree in Linguistics
- English / Japanese / Spanish speaker
- .NET (C#/F#) and JS developer
- Developed desktop, mobile and web apps for videogame,
  green energy and education industries


***


### FABLE MAIN FEATURES

- Compiles F# source code to JavaScript
- Powered by the [F# compiler](http://fsharp.github.io/FSharp.Compiler.Service/) and [Babel](http://babeljs.io)
- Compatible with [most of FSharp.Core and some .NET BCL](http://fable-compiler.github.io/docs/compatibility.html)
- Source maps for F# code debugging
- Generates clean JS code
- No runtime, framework agnostic
- Adheres to ES6 standards: modules, iterables...
- Compatible with many JS development tools: Webpack, React, Redux...
- Web, Node, Desktop (Electron) and Mobile (React Native, Fuse) apps
- Extensible through [plugins](http://fable-compiler.github.io/docs/plugins.html)
- Young but growing [ecosystem](https://www.github.com/fable-compiler) ([packages available in npm](https://www.npmjs.com/search?q=fable))


***


### F# MAIN FEATURES

- Static typing with type inference
- Comprehensive core library
- Functional programming also with powerful OOP features
- Great editor support (VS, Xamarin, Ionide, Emacs)

> Dynamic programming also possible with Fable

> [fable-core](https://www.npmjs.com/package/fable-core) is just 20KB gzipped


---


### F# COMPATIBILITY WITH FABLE

- Records, Unions and Tuples
- Structural Equality and Comparison
- Comprehensions (seq, array, list)
- Computation Expressions
- Pattern Matching and Active Patterns
- Object Expressions
- Units of Measure
- Type Providers (must be written for Fable)

Check the [documentation](http://fable-compiler.github.io/docs/compatibility.html) for more info


***


### HELLO WORLD!

    [lang=shell]
    mkdir temp
    cd temp
    npm init --yes
    npm install -g fable-compiler
    npm install --save fable-core
    echo "printfn \"Hello World!\"" > hello.fsx
    fable hello.fsx
    node hello.js


---


### Compiler options

[Tailor compilation](http://fable-compiler.github.io/docs/compiling.html#CLI-options) to your needs:

![CLI options](images/cli.png)

---


### [fableconfig.json](https://github.com/fsprojects/Fable/blob/master/docs/compiling.md#fableconfigjson)

    [lang=json]
    {
        "module": "commonjs",
        "outDir": "out",
        "sourceMaps": true,
        "projFile": "src/Fable.Samples.React.fsproj",
        "scripts": {
            "prebuild": "npm install",
            "postbuild": "node node_modules/webpack/bin/webpack"
        },
        "targets": {
            "debug": {
                "watch": true,
                "symbols": ["DEV_SERVER"],
                "scripts": { "postbuild": "" }
            },
            "production": {
                "scripts": {
                    "postbuild": "node node_modules/webpack/bin/webpack -p"
                }
            }
        }
    }


---


### TESTING

NUnit or Visual Studio tests can be compiled to JS too

    #r "../../../packages/NUnit/lib/nunit.framework.dll"
    #load "util/util.fs"

    open NUnit.Framework

    [<Test>]
    let ``Util.reverse works``() =
        let res = Util.reverse "araN"
        Assert.AreEqual("Nara", res)


---

Compile the tests using NUnit plugin and run them with Mocha

    [lang=text]
    fable samples/node/console/tests.fsx -m commonjs
        --outDir out --plugins build/plugins/Fable.Plugins.NUnit.dll

    node build/tests/node_modules/mocha/bin/mocha
        samples/node/console/out/tests.js

***
- data-transition:concave


### INTERACTING WITH JS

We don't want to just intrude the JS ecosytem,
we want to take advantage of its full potential.

<p class="fragment fade-in">
Because Functional Programming community is <strong>fantastic</strong>
</p>

<p class="fragment fade-in">
and JS community is...
</p>

<p class="fragment fade-in" style="font-size: 6em">
<strong>HUGE</strong>
</p>

<p class="fragment fade-in">
...and amazing too :)
</p>


---
- data-transition:concave


### FOREIGN INTERFACES

Define foreign interfaces easily to get the benefits of static checking and Intellisense

    [<Import("*","string_decoder")>]
    module string_decoder =
        type NodeStringDecoder =
            abstract write: buffer: Buffer -> strings
            abstract detectIncompleteChar: buffer: Buffer -> float

        let StringDecoder: NodeStringDecoder = failwith "JS only"


Use ImportAttribute to [import external JS modules](https://fable-compiler.github.io/docs/interacting.html#Import-attribute)

---
- data-transition:concave
- class:line-height-150


- Native JS, Browser and Node interfaces are included in [fable-core](https://www.npmjs.com/package/fable-core)
- More definitions can be found in npm: [fable-import-xxx](https://www.npmjs.com/search?q=fable-import)
- A TypeScript parser is also available: [ts2fable](https://www.npmjs.com/package/ts2fable)

> Bindings generated by ts2fable often require some manual editting


---
- data-transition:concave


### DYNAMIC PROGRAMMING WITH FABLE

    open Fable.Core

    printfn "Value: %O" jsObj?myProp    // Property access

    jsObj?myProp <- 5                   // Assignment

    let x = jsObj?myMethod(1, 2)        // Application

    let y = createNew jsCons (1, 2)     // Apply `new` keyword

    let data =                          // JS literal object
        createObj [
            "todos" ==> Storage.fetch()
            "newTodo" ==> ""
            "editedTodo" ==> None
            "visibility" ==> "all"
        ]


---
- data-transition:concave


### JS MACROS

Use [Emit](https://fable-compiler.github.io/docs/interacting.html#Emit-attribute) attribute to emit JS code directly

    open Fable.Core

    [<Emit("$0 + $1")>]
    let add (x: int) (y: string): float = failwith "JS only"

    type Test() =
        // Rest arguments
        [<Emit("$0($1...)")>]
        member __.Invoke([<ParamArray>] args: int[]): obj =
            failwith "JS only"

        // Syntax conditioned to optional parameter
        [<Emit("$0[$1]{{=$2}}")>]
        member __.Item
            with get(): float = failwith "JS only"
            and set(v: float): unit = failwith "JS only"


---
- data-transition:concave


### SPECIAL ATTRIBUTES

- [Erased Union Types](https://fable-compiler.github.io/docs/interacting.html#Erase-attribute)
- [String Literal Types](https://fable-compiler.github.io/docs/interacting.html#StringEnum-attribute)
- [Key-Value Lists](https://fable-compiler.github.io/docs/interacting.html#KeyValueList-attribute)
- [Uniqueness Records](https://fable-compiler.github.io/docs/interacting.html#Uniqueness-attribute)

```fsharp
[<Erase>]
type MyErasedType =
    | String of string | Number of int

[<StringEnum>]
type MyStrings =
    | Vertical | Horizontal

[<KeyValueList>]
type MyOptions =
    | Flag1
    | Name of string
    | [<CompiledName("QTY")>] QTY of int
```


***


### MADE WITH FABLE: [IONIDE](http://ionide.io/)

F# Extension for VS Code and Atom

![Ionide logo](images/Ionide.png)


---


![Ionide demo](images/Ionide-demo.gif)

Fable itself has been developed with VS Code + Ionide!


---


### MADE WITH FABLE: [PAYOFF CHARTS](http://www.payoffcharts.com)

![Payoff charts demo](images/PayoffCharts-demo.gif)


---


Source code by @hoonzis available on Github

[github.com/hoonzis/Pricer](https://github.com/hoonzis/Pricer)


---


### MADE WITH FABLE: [THE GAMMA PROJECT](http://rio2016.thegamma.net/)


![Gamma logo](images/Gamma.jpg)

Learn more in [Tomas Petricek's blog](http://tomasp.net/blog/2016/thegamma-olympic-medalists/)


---


![Gamma demo](images/Gamma-demo.gif)


***


### FABLE IN NUMBERS

- Currently in beta: [v0.6.7](https://www.npmjs.com/package/fable-compiler) (soon v1.0.0)
- 6 months since release
- >800 [unit tests](https://github.com/fsprojects/Fable/tree/master/src/tests)
- >800 commits, >500 stars, 37 contributors
- 25 packages in [npm](https://www.npmjs.com/~alfonsogarciacaro): compiler, core lib, bindings, TypeScript parser
- 5000 downloads last month


***


### Let's start coding!

http://fable-compiler.github.io

[CUFP workshop](https://github.com/alfonsogarciacaro/fable-cufp-workshop/tree/master/todomvc-fable-arch)

Don't forget to star the project on [GitHub](https://github.com/fable-compiler/Fable)
and join the [F# Software Foundation](http://foundation.fsharp.org/join)!

<div style="text-align: right">
  <p>Follow us on Twitter!</p>
  <p><a href="https://twitter.com/FableCompiler">@FableCompiler</a> / <a href="https://twitter.com/alfonsogcnunez">@alfonsogcnunez</a></p>
</div>
