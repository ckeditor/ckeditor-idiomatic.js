# Code style guide for [CKEditor](https://github.com/ckeditor/ckeditor-dev)

**This document is based on [Idiomatic.JS](https://github.com/rwldrn/idiomatic.js).** However, it contains many clarifications and rules specific for projects maintained by [CKSource](http://cksource.com/) like [CKEditor](https://github.com/ckeditor/ckeditor-dev).


## Mottos

> ### All code in any code-base should look like a single person typed it, no matter how many people contributed.

> ### "Arguments over style are pointless. There should be a style guide, and you should follow it"
>_Rebecca_ _Murphey_

&nbsp;

> ### "Part of being a good steward to a successful project is realizing that writing code for yourself is a Bad Idea™. If thousands of people are using your code, then write your code for maximum clarity, not your personal preference of how to get clever within the spec."
>_Idan_ _Gazit_


## Table of Contents

* [Whitespace](#whitespace)
* [Beautiful Syntax](#spacing)
* [Type Checking (Courtesy jQuery Core Style Guidelines)](#type)
* [Conditional Evaluation](#cond)
* [Practical Style](#practical)
* [Naming](#naming)
* [Misc](#misc)
* [Native & Host Objects](#native)
* [Comments](#comments)
* [One Language Code](#language)



------------------------------------------------


## Code style guide


1. <a name="whitespace">Whitespace</a>
  - Always use tabs. Never use spaces for indentation (for both - code and comments).
  - Tab is 4 spaces.
  - Do not leave trailing spaces (**note:** empty lines should not contain any spaces).
  - Always use LF line endings. Never use CRLF.
  - If your editor supports it, always work with the "show invisibles" setting turned on. The benefits of this practice are:
      - Enforced consistency
      - Eliminating end of line whitespace
      - Eliminating blank line whitespace
      - Commits and diffs that are easier to read

  Related settings for Sublime users:

        {
            // Defaults:
            "tab_size": 4,
            "translate_tabs_to_spaces": false,

            // Additional settings:
            "default_line_ending": "unix",
            "trim_trailing_white_space_on_save": true
        }


2. <a name="spacing">Beautiful Syntax</a>

    A. Parens, Braces, Linebreaks

    ```javascript

    // 2.A.1.1
    // Use whitespace to promote readability

    if ( condition ) {
        // statements
    }

    if ( true ) {
      // statements
    } else {
      // statements
    }

    if ( true ) {
        // statements
    }
    // Comments regarding what "else" means and does.
    else {
        // statements
    }

    while ( condition ) {
        // statements
    }

    for ( var i = 0; i < 100; i++ ) {
        // statements
    }

    try {
        // statements
    } catch ( e ) {
        // statements
    }
    ```


    B. Assignments, Declarations, Functions (Named, Expression, Constructor)

    ```javascript

    // 2.B.1.1
    // Variables
    var foo = 'bar',
        num = 1,
        undef;

    // Literal notations:
    var array = [],
        object = {};


    // 2.B.1.2
    // Using only one `var` per scope (function) promotes readability
    // and keeps your declaration list free of clutter (also saves a few keystrokes).

        // Comment on this.
    var foo = '',
        bar = '',
        // Comment on this.
        quux;

    // 2.B.1.3
    // Try to group `var` statements. Preferably in the beginning
    // of their respective scope (function), but also don't go crazy with this.
    // Avoid declaring new variable every few lines of code.
    // In case of long functions make sure to declare all common variables
    // (like loop counters, `el`, `range`, etc.) in the beginning of scope.
    // Same goes for const and let from ECMAScript 6.

    // Bad.
    function foo() {
        // 2 statements

        var bar = '';

        // 2 statements

        var foo = '';

        // 50 statements

        var bom = '';

        // some statements
    }

    // Good
    function foo() {
        var bar = '',
            foo = '';

        // 50 statements

        var bom = '';   // Assuming that bom isn't needed before, there's no sense
                        // in grouping it with the first group.

        // some statements
    }


    // 2.B.2.1
    // Named Function Declaration
    function foo( arg1, argN ) {

    }

    // Usage.
    foo( arg1, argN );


    // 2.B.2.2
    // Named Function Declaration.
    function square( number ) {
        return number * number;
    }

    // Usage.
    square( 10 );

    // Really contrived continuation passing style.
    function square( number, callback ) {
        callback( number * number );
    }

    square( 10, function( square ) {
        // callback statements
    } );


    // 2.B.2.3
    // Function Expression
    var square = function( number ) {
        // Return something valuable and relevant.
        return number * number;
    };


    // 2.B.2.4
    // Constructor Declaration
    function FooBar( options ) {
        this.options = options;
    }

    // Usage.
    var fooBar = new FooBar( { a: 'alpha' } );

    fooBar.options; // -> { a: 'alpha' }

    ```


    C. Quotes

    Always use single quotes in JavaScript, but double quotes in HTML.


3. <a name="type">Type Checking</a>

    A. Actual Types

    String:

        typeof variable == 'string'

    Number:

        typeof variable == 'number'

    Boolean:

        typeof variable == 'boolean'

    Object:

        typeof variable == 'object'

    Array:

        // In CKEditor:
        CKEDITOR.tools.isArray( arrayLikeObject )
        // In other cases:
        Array.isArray( arrayLikeObject )
        (wherever possible)

    Node:

        elem.nodeType == 1

    null:

        variable === null

    null or undefined:

        variable == null

    undefined:

        Global Variables:

            typeof variable == 'undefined'
            // Or:
            variable === undefined

        Properties:

            object.prop === undefined
            'prop' in object
            !object.prop // If you know what you do.


    B. Coerced Types

    Use `+`, `!!` and `+ ''` for type coercion whenever possible. However, your code
    should not look like assembly or CoffeeScript so do not overuse tricks.

    Here are some common cases along with coercions:


    ```javascript

    // 3.B.2.1

    var number = 1,
        string = '1',
        bool = false;

    number;
    // 1

    number + '';
    // '1'

    string;
    // '1'

    +string;
    // 1

    +string++;
    // 1

    string;
    // 2

    bool;
    // false

    +bool;
    // 0

    bool + '';
    // 'false'
    ```


    ```javascript
    // 3.B.2.2

    var number = 1,
        string = '1',
        bool = true;

    string === number;
    // false

    string === number + '';
    // true

    +string === number;
    // true

    bool === number;
    // false

    +bool === number;
    // true

    bool === string;
    // false

    bool === !!string;
    // true
    ```

    ```javascript
    // 3.B.2.3

    var array = [ 'a', 'b', 'c' ];

    !!~array.indexOf( 'a' );
    // true

    !!~array.indexOf( 'b' );
    // true

    !!~array.indexOf( 'c' );
    // true

    !!~array.indexOf( 'd' );
    // false

    // Note that the above should be considered "unnecessarily clever"
    // Prefer the obvious approach of comparing the returned value of
    // indexOf, like:

    if ( array.indexOf( 'a' ) >= 0 ) {
      // ...
    }
    ```

    ```javascript
    // 3.B.2.4

    var num = 2.5;

    parseInt( num, 10 );

    // Is the same as...

    ~~num;

    num >> 0;

    num >>> 0;

    // All result in 2.


    // Keep in mind however, that negative numbers will be treated differently...

    var neg = -2.5;

    parseInt( neg, 10 );

    // Is the same as...

    ~~neg;

    neg >> 0;

    // All result in -2.
    // However...

    neg >>> 0;

    // Will result in 4294967294.

    ```


4. <a name="cond">Conditional Evaluation</a>

    ```javascript

    // 4.1.1
    // When only evaluating that an array has length,
    // instead of this:
    if ( array.length > 0 ) ...

    // ...evaluate truthiness, like this:
    if ( array.length ) ...


    // 4.1.2
    // When only evaluating that an array is empty,
    // instead of this:
    if ( array.length === 0 ) ...

    // ...evaluate truthiness, like this:
    if ( !array.length ) ...


    // 4.1.3
    // When only evaluating that a string is not empty,
    // instead of this:
    if ( string !== '' ) ...

    // ...evaluate truthiness, like this:
    if ( string ) ...


    // 4.1.4
    // When only evaluating that a string _is_ empty,
    // instead of this:
    if ( string === '' ) ...

    // ...evaluate falsyness, like this:
    if ( !string ) ...


    // 4.1.5
    // When only evaluating that a reference is true,
    // instead of this:
    if ( foo === true ) ...

    // ...evaluate like you mean it, take advantage of built in capabilities:
    if ( foo ) ...


    // 4.1.6
    // When evaluating that a reference is false,
    // instead of this:
    if ( foo === false ) ...

    // ...use negation to coerce a true evaluation"
    if ( !foo ) ...

    // ...Be careful, this will also match: 0, '', null, undefined, NaN
    // If you _MUST_ test for a boolean false, then use"
    if ( foo === false ) ...


    // 4.1.7
    // When only evaluating a ref that might be null or undefined,
    // but NOT false, '' or 0, instead of this:
    if ( foo === null || foo === undefined ) ...

    // ...take advantage of == type coercion, like this:
    if ( foo == null ) ...

    // Remember, using == will match a `null` to BOTH `null` and `undefined`
    // but not `false`, '' or 0
    null == undefined

    ```

    ALWAYS evaluate for the best, most accurate result - the above is a guideline, not a dogma.

    ```javascript

    // 4.2.1
    // Type coercion and evaluation notes

    // Use === only if you have to - for example when you're dealing
    // with user input or you need to distinguish between null and undefined.
    // You can safely use == in most cases, especially
    // when function's input is documented.
    // It will be caller's fault if he passes wrong arguments.


    // 4.2.2
    // Booleans, Truthies & Falsies

    // Booleans:
    true, false

    // Truthy:
    "foo", 1

    // Falsy:
    "", 0, null, undefined, NaN, void 0

    ```


5. <a name="practical">Practical Style</a>

    ```javascript

    // 5.1.1
    // A Practical Module

    ( function() {
        var Module = ( function() {
            var data = "secret";

            return {
                // This is some boolean property.
                bool: true,
                // Some string value.
                string: 'a string',
                // An array property.
                array: [ 1, 2, 3, 4 ],
                // An object property.
                object: {
                    lang: 'en-Us'
                },

                getData: function() {
                    // Get the current value of `data`.
                    return data;
                },

                setData: function( value ) {
                    // Set the value of `data` and return it.
                    return ( data = value );
                }
            };
        } )();

        // Other things might happen here.

        // Expose our module to the global object.
        CKEDITOR.module = Module;
    } )();

    ```

    ```javascript

    // 5.2.1
    // A Practical Constructor

    ( function() {
        function Ctor( foo ) {
            this.foo = foo;

            return this;
        }

        CKEDITOR.tools.extend( Ctor.prototype, {
            getFoo: function() {
                return this.foo;
            };

            setFoo: function( val ) {
                return ( this.foo = val );
            };
        } );

        // Expose our constructor to the global object.
        CKEDITOR.ctor = Ctor;
    } )();

    ```


6. <a name="naming">Naming</a>


    A. You are not a human code compiler/compressor, so don't try to be one.

    The following code is an example of egregious naming:

    ```javascript

    // 6.A.1.1
    // Example of code with poor names

    function q(s) {
        return document.querySelectorAll(s);
    }
    var i,a=[],els=q("#foo");
    for(i=0;i<els.length;i++){a.push(els[i]);}
    ```

    Here's the same piece of logic, but with kinder, more thoughtful naming (and a readable structure):

    ```javascript

    // 6.A.2.1
    // Example of code with improved names

    function query( selector ) {
      return document.querySelectorAll( selector );
    }

    var id = 0,
        elements = [],
        matches = query( '#foo' ),
        length = matches.length;

    for ( ; id < length; id++ )
        elements.push( matches[ id ] );

    ```

    A few additional naming pointers:

    ```javascript

    // 6.A.3.1
    // Naming strings

    `dog` is a string


    // 6.A.3.2
    // Naming arrays

    `dogs` is an array of `dog` strings


    // 6.A.3.3
    // Naming functions, objects, instances, etc

    camelCase; function and var declarations


    // 6.A.3.4
    // Naming constructors, prototypes, etc.

    // constructor function
    PascalCase;
    // but camel case when making public:
    CKEDITOR.sth.camelCase;


    // 6.A.3.6
    // From the Google Closure Library Style Guide

    functionNamesLikeThis;
    variableNamesLikeThis;
    ConstructorNamesLikeThis;
    EnumNamesLikeThis;
    methodNamesLikeThis;
    SYMBOLIC_CONSTANTS_LIKE_THIS;

    ```

    B. Faces of `this`

    Keep `this` "local" to the class. This means &ndash; do not bind e.g. a function in `selection.js` file to editor instance and do not create a function which should be ran in editor context. This way you'll avoid confusion what `this` refers to.

    ```javascript

    // 6.B.1
    function Selection() {
        var editor = this.editor;

        // Bad:
        editor.on( 'setData', someCallback, editor );

        // Still bad:
        editor.on( 'setData', CKEDITOR.tools.bind( someCallback, editor ) );

        // Good:
        editor.on( 'setData', someCallback, this );

        // Good:
        this.getEditor().on( 'setData', function( evt ) {
            evt.editor.doSomething();
        } );

        // Good too:
        var that = this;
        editor.on( 'setData', function( evt ) {
            editor.setCurrentSelection( that );
        } );
    }

    function someCallback() {
        // Should refers to Selection, not Editor.
        this;
    }

    ```

    C. Use `thisArg`

    Several prototype methods of ES 5.1 built-ins come with a special `thisArg` signature, which should be used whenever possible. CKEditor follows this practice too, so for example [`eventTarget.on`](http://docs.ckeditor.com/#!/api/CKEDITOR.event-method-on) also accepts context as an argument.

    ```javascript

    // 6.C.1

    var obj = { f: 'foo', b: 'bar', q: 'qux' };

    Object.keys( obj ).forEach( function( key ) {

      // |this| now refers to `obj`

      console.log( this[ key ] );

    }, obj ); // <-- the last arg is `thisArg`

    // Prints...

    // 'foo'
    // 'bar'
    // 'qux'

    ```

    `thisArg` can be used with `Array.prototype.every`, `Array.prototype.forEach`, `Array.prototype.some`, `Array.prototype.map`, `Array.prototype.filter`.

7. <a name="misc">Misc</a>

    A. Early returns promote code readability with negligible performance difference

    ```javascript

    // 7.A.1.1
    // Bad:
    function returnLate( foo ) {
        var ret;

        if ( foo ) {
            ret = 'foo';
        } else {
            ret = 'quux';
        }
        return ret;
    }

    // Good:
    function returnEarly( foo ) {
        if ( foo ) {
            return 'foo';
        }
        return 'quux';
    }

    // Bad:
    function doSthComplex( foo, bar ) {
        if ( foo ) {
            // do something

            if ( bar ) {
                // do something
            } else {
                // do something else
            }
        }
    }

    // Good:
    function doSthComplex( foo, bar ) {
        if ( !foo )
            return;

        // do something

        if ( !bar ) {
            // do something else
            return;
        }

        // do something
    }

    ```

    B. (CKEditor only) Objects minify poorly, closures very well

    Avoid excessing usage of objects, when functional programming may be used as well. Properties
    and method names unlike private variables and function names cannot be shortened by minifier.

    You can play with [online Google Closure Compiler](http://closure-compiler.appspot.com/home) to
    test different approaches. Make sure to check the gzipped size which matters the most.


8. <a name="native">Native & Host Objects</a>

    The basic principle here is:

    ### Don't do stupid shit and everything will be ok.

    To reinforce this concept, please watch the following presentation:

    #### “Everything is Permitted: Extending Built-ins” by Andrew Dupont (JSConf2011, Portland, Oregon)

    <iframe src="http://blip.tv/play/g_Mngr6LegI.html" width="480" height="346" frameborder="0" allowfullscreen></iframe><embed type="application/x-shockwave-flash" src="http://a.blip.tv/api.swf#g_Mngr6LegI" style="display:none"></embed>

    http://blip.tv/jsconf/jsconf2011-andrew-dupont-everything-is-permitted-extending-built-ins-5211542

    **But** CKEditor is a widget - an application inside someone's system &ndash; so... do not touch anything except
    `CKEDITOR.*` namespace.


9. <a name="comments">Comments</a>
  - Single line above the code that is subject (except for long expressions like conditions).
  - Comment is a sentence - start it with capital leter, end with period.
  - Use multiline comments only for API documentation.
  - Check the [JSDuck documentation guide](http://dev.ckeditor.com/wiki/CodeDocumentation).
  - You can use JSDuck documentation style for private functions, but then use single line comments.
  - Justify end line comments with tabs:

            if ( some != really.complex &&      // Comment line 1.
                condition || with &&            // Comment line 2.
                ( multiple == !lines ) )        // Comment line 3.

            // Of course it's better to avoid this spaghetti at all.


----------


<a rel="license" href="http://creativecommons.org/licenses/by/3.0/deed.en_US"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by/3.0/80x15.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">Principles of Writing Consistent, Idiomatic JavaScript</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/rwldrn/idiomatic.js" property="cc:attributionName" rel="cc:attributionURL">Rick Waldron and Contributors</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/3.0/deed.en_US">Creative Commons Attribution 3.0 Unported License</a>.<br />Based on a work at <a xmlns:dct="http://purl.org/dc/terms/" href="https://github.com/rwldrn/idiomatic.js" rel="dct:source">github.com/rwldrn/idiomatic.js</a>.
