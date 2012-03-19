
# CoffeeNode, an Application Basework

>   "JavaScript is too important to be left to the experts."—*Jeremy
>   Ashkenas, creator of CoffeeScript*



############################################################################################################

# Prelude: What Made me Leave Python

## after 10 Years of Loving it

*   I earned the *Popular Question* badge on StackOverflow.com 3 times:

    *   Is it possible to use **re2** from Python? (2010)

    *   how to install **pycairo for python 3** on Ubuntu 10.04? (2011)

    *   how to do **asynchronous http** requests with epoll and **python 3.1**? (2010)

*   all questions were hard—3373 views in 33 months generated only 10 answers total or 3⅓ answers per.
    That's 3 views per day, but only one answer in 3 days.

*   Realize something?

*   All questions remained essentially unanswered

*   and the last one did it.

*   Good riddance, good bye

*   Helo NodeJS

>    (see [http://stackoverflow.com/questions/2439345](http://stackoverflow.com/questions/2439345),
>    [6232529](http://stackoverflow.com/questions/6232529),
>    and [2489780](http://stackoverflow.com/questions/2489780))


############################################################################################################

# Prelude: What Made me Leave OOP

## after 10 Years of Trying to Love it

>   "The last step forward was object-oriented programming [...] the next
>   step is all overdue: we should've been there by now and we
>   haven't—we're still in the object-oriented stage."—Douglas Crockford, *Web Forward*, keynote to the
>   2008 Frontend Engineering Summit at Yahoo!,
>   [http://www.youtube.com/watch?v=yh7TeoEwNyI](http://www.youtube.com/watch?v=yh7TeoEwNyI)

>   "Object Oriented Programming puts the Nouns first and foremost. Why
>   would you go to such lengths to put one part of speech on a pedestal?
>   Why should one kind of concept take precedence over another? It's not
>   as if OOP has suddenly made verbs less important in the way we
>   actually think. It's a strangely skewed perspective."---Steve Yegge,
>   *Execution in the Kingdom of Nouns*,
>   [http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html](http://steve-yegge.blogspot.com/2006/03/execution-in-kingdom-of-nouns.html)

    RidersGuild.getRiderNotificationSubscriberList().getBroadcaster().run(
      new BroadcastMessage(StableFactory.getNullHorseInstance()));



############################################################################################################

# Approaches to Programming

*   Engineering

*   Architecture

*   Something completely different: 'Computer Science', 'Informatics'

*   Linguistics


############################################################################################################

# Language is Important

*   Computers don't need readable programs—humans do.

Literate Programming can provide tools to make organized, beautiful
source code and documentation, but no amount of CSS can demystify a
badly chosen variable name.

>    "Programs are not just a technical contrivance, they are a medium of
>    intentional communication—with the machine, but more importantly with
>    your development community, and ultimately with yourself."—Douglas
>    Crockford, *Quality*, talk delivered on Yahoo!'s internal Frontend
>    Engineering Summit in 2007,
>    [http://www.youtube.com/watch?v=t9YLtDJZtPY](http://www.youtube.com/watch?v=t9YLtDJZtPY)
>
>    "The simplest thing we can do to enhance the value of our codebase is to
>    make our programs more readable."—Douglas Crockford, *Quality*, talk
>    delivered on Yahoo!'s internal Frontend Engineering Summit in 2007,
>    [http://www.youtube.com/watch?v=t9YLtDJZtPY](http://www.youtube.com/watch?v=t9YLtDJZtPY)


############################################################################################################

# CoffeeNode Data Type Therapy


############################################################################################################

# What is a Data Type?

*   A classification of data

*   based on their underlying storage needs (extension)

*   and intended semantics & use (intention)


############################################################################################################

# How are Data Types Represented?

*   In JavaScript, the `typeof` operator returns a string

*   whereas in Python, types are represented by the classes that
    implement them

*   (the reality is a little more complicated).

*   Representing / identifying types by texts is straightforward,
    simple, accessible and extensible

*   it works for 7 billion people and their pets

*   so it should be good enough for a computer application.

*   Examples:

    -   built-in JavaScript types:

            'text'
            'number'
            'boolean'
            'jsdate'

    -   CoffeeNode extension types:

            'SET/set'
            'HTML/document'

    -   3rd party types:

            'example.com/FOO/bar'


############################################################################################################

# JS Types—Are they Good Enough?

*   Short answer: No.

*   Long answer:

value            JS `typeof`
---------------- -------------
`true`           `boolean`
`->`             `function`
`'text'`         `string`
`undefined`      `undefined`
`42`             `number`
`Infinity`       `number`
`NaN`            `number`
`[]`             `object`
`{}`             `object`
`new Date()`     `object`
`global`         `object`
`new RegExp()`   `object`
`new Error()`    `object`
`arguments`      `object`
`null`           `object`

:  Behavior of the JS `typeof` operator

*   Likewise, the `instanceof` operator is deeply broken:

        typeof 42 == 'number'                   # true
        ( new Number 42 ) instanceof Number     # true
        typeof NaN == 'number'                  # true

        42 instanceof Number                    # false
        NaN instanceof Number                   # false

*   ⚞OMFG⚟


############################################################################################################

# Remedy: The Miller Device (1/2)

*   Fortunately, there is a `toString` method in `Object.protype` that behaves a tad more civilized:

value            `Object::toString.call value`
---------------- -------------------------------
`true`           `[object Boolean]`
`->`             `[object Function]`
`'text'`         `[object String]`
`undefined`      `[object Undefined]`
`42`             `[object Number]`
`Infinity`       `[object Number]`
`NaN`            `[object Number]`
`[]`             `[object Array]`
`{}`             `[object Object]`
`new Date()`     `[object Date]`
`global`         `[object global]`
`new RegExp()`   `[object RegExp]`
`new Error()`    `[object Error]`
`arguments`      `[object Arguments]`
`null`           `[object Null]`

: Return values of `Object::toString`

############################################################################################################

# Remedy: The Miller Device (2/2)

*   We exploit the goodness of the Miller Device to arrive at a sane type system. From
    `COFFEENODE/TYPES.coffee`:

        #---------------------------------------------------------------------------------------------
        js_type_of = ( x ) ->
          return Object.prototype::call x

        #---------------------------------------------------------------------------------------------
        $.isa_list          = ( x ) -> return ( js_type_of x ) == '[object Array]'
        $.isa_boolean       = ( x ) -> return ( js_type_of x ) == '[object Boolean]'
        $.isa_function      = ( x ) -> return ( js_type_of x ) == '[object Function]'
        $.isa_pod           = ( x ) -> return ( js_type_of x ) == '[object Object]'
        $.isa_text          = ( x ) -> return ( js_type_of x ) == '[object String]'
        $.isa_number        = ( x ) -> return ( js_type_of x ) == '[object Number]' and isFinite x
        $.isa_infinity      = ( x ) -> return x == Infinity or x == -Infinity
        $.isa_null          = ( x ) -> return x is null
        #.............................................................................................
        $.isa_jsundefined   = ( x ) -> return x is undefined
        $.isa_jsnotanumber  = ( x ) -> return isNaN x
        $.isa_jsarguments   = ( x ) -> return ( js_type_of x ) == '[object Arguments]'
        $.isa_jsdate        = ( x ) -> return ( js_type_of x ) == '[object Date]'
        $.isa_jsglobal      = ( x ) -> return ( js_type_of x ) == '[object global]'
        $.isa_jsregex       = ( x ) -> return ( js_type_of x ) == '[object RegExp]'
        $.isa_jserror       = ( x ) -> return ( js_type_of x ) == '[object Error]'
        $.isa_jswindow      = ( x ) -> return ( js_type_of x ) == '[object DOMWindow]'
        $.isa_jsctx         = ( x ) -> return ( js_type_of x ) == '[object CanvasRenderingContext2D]'
        $.isa_jsarraybuffer = ( x ) -> return ( js_type_of x ) == '[object ArrayBuffer]'

        #---------------------------------------------------------------------------------------------
        coffeenode_type_by_js_type =
          '[object Array]':                     'list'
          '[object Boolean]':                   'boolean'
          '[object Function]':                  'function'
          '[object Null]':                      'null'
          '[object String]':                    'text'
          '[object Object]':                    'pod'
          #...........................................................................................
          '[object Undefined]':                 'jsundefined'
          '[object Arguments]':                 'jsarguments'
          '[object Date]':                      'jsdate'
          '[object Error]':                     'jserror'
          '[object global]':                    'jsglobal'
          '[object RegExp]':                    'jsregex'
          '[object DOMWindow]':                 'jswindow'
          '[object CanvasRenderingContext2D]':  'jsctx'
          '[object ArrayBuffer]':               'jsarraybuffer'
          #...........................................................................................
          '[object Number]': ( x ) ->
            return 'jsnotanumber' if isNaN x
            return 'jsinfinity'   if x == Infinity or x == -Infinity
            return 'number'

        #---------------------------------------------------------------------------------------------
        $.type_of = ( x ) ->
          """Given any kind of value ``x``, return its type."""
          R = coffeenode_type_by_js_type[ js_type_of x ]
          unless R? then throw new Error "unable to determine type of #{rpr x}"
          return if @isa_function R then R x else R

*   Usage:

        log type_of        1 / 0            # jsinfinity
        log isa_jsinfinity 1 / 0            # true
        log isa_number     1 / 0            # false


############################################################################################################

# Type Hierarchy (and what we can do with it)

## Native JavaScript Types

*   The native JavaScript data types are divided into three groups:

*   The first group contains all the 'universally accepted' data types;
    there are seven of these, all of which (except `function`) are
    directly representable in JSON. I call this set the 'basic' data types:

    *   `list`
    *   `boolean`
    *   `function`
    *   `null`
    *   `text`
    *   `pod`
    *   `number`

*   The second group contains data types that are not nearly as
    universally accepted but are present in all modern JavaScript VMs.
    Some (like `jsnotanumber`) are introduced by CoffeeNode:

    *   `jsundefined`
    *   `jsarguments`
    *   `jsdate`
    *   `jserror`
    *   `jsglobal`
    *   `jsregex`
    *   `jsnotanumber`
    *   `jsinfinity`

*   The third group contains data types that may only be present in some
    environments, depending e.g. you're running code in NodeJS on the
    server or in, say, on SpiderMonkey in Firefox:

    *   `jsglobal`
    *   `jswindow`
    *   `jsctx`
    *   `jsarraybuffer`


############################################################################################################

# Type Hierarchy (and what we can do with it)

## Issues with Native Types

*   A ~~short~~ TL;DR discussion of issues with JS data
    types:

`jsundefined`
  ~ ... it's OK to have a value to represent something that doesn't exist,
    but it's not cool to make one value represent a number of completely unrelated
    code quality issues:

        f = ( x, y ) ->
            bye 'no value given for y' unless y?

        d = { a: 42 }
        f d[ 'a' ]                  # fails because `f` was called with too few arguments
        f d[ 'a' ], d[ 'b' ]        # fails because `d` has no member `b`

`jsnotanumber`
  ~ ... `NaN`, which appears as the result of some bogus operations, such
    as adding a number and a text that does not 'look like' a number;
    this value is crazy to the point it identifies itself as a number.
    Don't touch, move on.

`jsinfinity`
  ~ ... appears as the result of some other bogus operations, such as

    *   `1 / 0` (Wikipedia: "Division by zero must be left undefined in any
        mathematical system that obeys the axioms of a field."),
    *   `Math.max()` (how many apples does the fullest basket contain if there are no baskets?).

`jsarguments`
  ~ ... a strange thing that wants to be a list ever so badly; it is
    sometimes very practical to have, but is missing from competing
    languages and will in a few years get removed from JavaScript, too.

`jsdate`
  ~ ... which is extremely difficult to handle correctly, and full of subtle bugs.

`jserror`
  ~ ... the one data type in this group that looks OK.

`jsregex`
  ~ ... full of strange API decisions, bugs and unfixable shortcomings—see
    [http://blog.stevenlevithan.com](http://blog.stevenlevithan.com).


############################################################################################################

# Type Hierarchy (and what we can do with it)

## Equality? Nope.

*   CoffeeNode tries to make programming in JavaScript easier
    and more fun, and to enhance program correctness.

*   Among the basic things that don't work very well in plain JavaScript
    are equality and containedness with collection data types

*   which means that `[] == []` and `{} == {}` are `false` when they should be `true`

*   and also `[ 456, 893, [ 5, 6, 7, ], ].indexOf [ 5, 6, 7, ]` gives `-1` ('not found') instead of `2`.

*   JavaScript's `===` (CoffeeScript: `==`) 'strict equality operator' is more like a 'object identity
    operator'

############################################################################################################

# Type Hierarchy (and what we can do with it)

## Example Code: `LIST/equals`

*   How can we fix equality and containment?

*   Sadly, there is no way to override the behavior of ``==`` and ``===``.

*   It isn't advisable, either—such a global language change could easily affect
    code acting under the premise that `( [] == [] ) == false`.

*   Don't Monkey-Patch. Ever.

*   So we have to put the functionality into libraries.

*   **Example**: from `COFFEENODE/jstypes/LIST/implementation.coffee` (**NB** this code is from the
    *implementation* submodule of `LIST`—you'd ordinarily call `LIST/main.cnd/equality`, which is a
    wrapper that checks argument number and that `me` is a `list`, and then returns the result of
    calling the below method):

        #---------------------------------------------------------------------------------------------
        $.equals = ( me, probe ) ->

          return false if not TYPES.isa_list probe
          return false if me.length != probe.length
          return true  if me.length == 0

          #...........................................................................................
          for idx in [ 0 ... me.length ]

            #.........................................................................................
            my_element      = me[    idx ]
            your_element    = probe[ idx ]

            return false if ( TYPES.type_of my_element ) != ( TYPES.type_of your_element )
            return false if not Σ.emit "!Δ/call/specific/equals", my_element, your_element

          #...........................................................................................
          return true

*   The magic happens in the Σ library

*   which lives at <https://github.com/loveencounterflow/COFFEENODE-SIGMA>

*   and is a synchronous signal handling library.

*   More later.


############################################################################################################

# Type Hierarchy (and what we can do with it)

## Example Code: `LIST/contains`

*   From `COFFEENODE/jstypes/LIST/implementation.coffee`:

        #---------------------------------------------------------------------------------------------
        $.contains = ( me, probe ) ->

          #...........................................................................................
          # If ``List.indexOf`` reports a hit, we definitely know it is so and return ``true``:

          return true if ( me.indexOf probe ) > -1

          #...........................................................................................
          # We know the miss is authoritative if ``probe`` can be equality-tested with JavaScript's
          # ``===`` operator:

          type_of_probe = TYPES.type_of probe
          return false if TYPES.simple_equality_types[ type_of_probe ]

          #...........................................................................................
          for value in me
            type_of_value = TYPES.type_of value

            #.........................................................................................
            # We can trivially skip ahead if
            # * the type of the value and the type of the probe differ
            # * or if the value type is on the 'simple equality' list:

            continue if type_of_value isnt type_of_probe
            continue if TYPES.simple_equality_types[ type_of_value ]

            #.........................................................................................
            return true if Σ.emit "!Δ/call/specific/equals", probe, value

          #...........................................................................................
          return false



############################################################################################################

# Types and Kinds

## Types are a Closed Set

*   To repeat: *Each* piece of data has a type.

*   In JavaScript (and, therefore, CoffeeScript) this data type can only ever
    be one of

    *   `list`
    *   `boolean`
    *   `function`
    *   `null`
    *   `text`
    *   `pod`
    *   `number`
    *   `jsarguments`
    *   `jsdate`
    *   `jserror`
    *   `jsglobal`
    *   `jsregex`
    *   `jsnotanumber`
    *   `jsinfinity`

*   Additionally, though, JavaScript may have a number of host object data types:

    *   `jsglobal`
    *   `jswindow`
    *   `jsctx`

*   (plus some things we omitted here, like an image on an HTML page).

*   You would have to write some C extension to your JavaScript VM to
    add a type

*   but whatever VM you use, the available types form a closed set at run time.
    And most added objects don't bother for typing; they just 'look like' ordinary
    plain objects, so you would have to inspect attributes to classify them.


############################################################################################################

# Types and Kinds

## Kinds are an Open Set

*   But JavaScript is very flexible, so you can build arbitrary objects.
    From `COFFEENODE/SET`:

        #---------------------------------------------------------------------------------------------
        $.new = ( x = null ) ->
          """Given any number of elements, return a set containing these elements."""

          #...........................................................................................
          elements = {}
          R        =
            '~isa':                   'SET/set'
            'elements':               elements

          #...........................................................................................
          if x?
            type = TYPES.type_of x
            unless TYPES.is_facetted x then bye "need a collection, got a #{type}"

            #.........................................................................................
            switch type
              #.......................................................................................
              when 'list', 'text'
                for element in x
                  elements[ HASH.as_hash_key element ] = 1
              #.......................................................................................
              when 'pod'
                for name, value of x
                  elements[ HASH.as_hash_key value ] = 1
              #.......................................................................................
              else
                return Σ.emit '!Δ/call/specific/as_set', x

          #...........................................................................................
          return R

*   Using this code in a `*.cnd` module (the `new_set` vocable courtesy of the LSD system that augments
    `*.cnd` files):

        log new_set 'helo world'

    gives you

        {   ~isa:     'SET/set'
            elements:
              h:      1
              e:      1
              l:      1
              o:      1
              ' ':    1
              w:      1
              r:      1
              d:      1
              }

*   which is basically a POD that gives its 'kind' by specifying a name under the key `~isa`

*   and contains a sub-POD `elements` that keeps the records.


############################################################################################################

# Wait—`*.cnd` Files???

## They're just CoffeeScript

*   `cnd` is short for 'CoffeeNode'.

*   `*.cnd` files implement a kind of 'augmented' CoffeeScript

*   where the grammar is left untouched

*   but vocabulary gets injected prior to execution.

*   You run them with

        cnd run route/to/your/script.cnd

*   It all happens in `COFFEENODE/ENGINE/wrap-0.0.3.coffee`, where the `cnd` extension gets registered:

        #-----------------------------------------------------------------------------------------
        require.extensions[ '.cnd' ] = ( module, route ) ->
          source  = njs_fs.readFileSync route, 'utf-8'
          #.......................................................................................
          content = Σ.emit 'COFFEENODE/engine/wrap/0.0.3',
            'module':   module
            'route':    route
            'source':   source
          #.......................................................................................
          module._compile content, route

*   The transformation of the source code is quite primitive: we simply iterate over all the identifiers in
    the source, and where words are found that belong to the received vocabulary, we either add a signal
    emitter or a `require` statement. This:

        names = []
        for word in words_of 'Alpha Beta Gamma'
          push names, word

        log names
        log reverse copy names

    gets transposed into this:

        #---------------------------------------------------------------------------------------
        # Minimal standard vocabulary
        $           = exports ? @
        bye         = ( P... ) -> throw new Error P...
        log         = ( require 'COFFEENODE/ENGINE/get-log-method' )()
        echo        = console.log

        #---------------------------------------------------------------------------------------"
        # Δ methods and standard libraries

        Λ = require 'COFFEENODE/Λ/implementation'
        words_of = Λ.get_emitter 'words_of'
        push = Λ.get_emitter 'push'
        reverse = Λ.get_emitter 'reverse'
        copy = Λ.get_emitter 'copy'

        names = []
        for word in words_of 'Alpha Beta Gamma'
          push names, word

        log names
        log reverse copy names

*   Since the additional vocabulary is injected at the top of the script, you can always use your own
    semantics—so if you define your own `push` function, you will get your own function, not the CoffeeNode
    version.


############################################################################################################

# Wait—`*.cnd` Files???

## The Vocabulary

*   Currently there are three groups of words that qualify for automatic inclusion in `*.cnd` files:

    *   Specific Δ Methods: they are dispatched to the type library that is associated with the type
        of the first argument in the call (a.k.a. the 'prinicipal noun').

    *   Generic Δ Methods: they are dispatched to the library that is identified in a registry (more later).

    *   Libraries included in the `COFFEENODE/library` folder.

*   Naming conventions:

    *   Δ methods should be fully spelled-out verbs or verb-ish expressions: `push`, `reverse`, `sort`,
        `copy`, `clear`, `compose`, `names_of`, `validate_isa_text`. There are a few accepted abbreviations,
        such as `rpr` for the 'representation' of a value, `idx` for 'index', `chr` for 'character'.

    *   CoffeeNode library names are spelled in SCREAMING CAPS using the Latin and the Greek alphabet.


############################################################################################################

# Types and Kinds

## Having Fun with Sets

*   A real advantage over using Plain Old JavaScript Objects directly is the type-safety of CoffeeNode sets:

        log new_set [ 98765.432, null, true, 'abc', ]

    gives

        {   ~isa:                 'SET/set',
            elements:
              '���n/98765.432':   1
              '���x/null':        1
              '���x/true':        1
              'abc':              1
              }

*   OK, that's ugly.

*   The `���` result from `\ufdef`—a so-called Unicode surrogate code point which can
    never go single in a valid Unicode string. Well, here it does so, bending the rules without
    breaking them. Welcome to the Matrix.

*   JavaScript only ever uses texts for POD keys, but using this sigil we can (with very high certainty)
    ensure that our special strings don't interfere with any other strings.

*   The `SET/contains` method (which coincides with `SET.has`) is *very* simple:

        #-----------------------------------------------------------------------------------------
        $.contains = $.has = ( me, probe ) ->
          return me[ 'elements' ][ HASH.as_hash_key probe ] isnt undefined

*   I just discover i should've really written `return POD.contains me[ 'elements' ], probe`


############################################################################################################

# Types and Kinds

## Kinds are Kind To You

*   So what happens if you throw unsuitable data at `new_set`?—Let's try:

*   `new_set new_set 'abc'` gives you

        Error: as_set is applicable to a set, but not implemented in COFFEENODE/jstypes/SET

    This tells you that although a set is a suitable argument to `as_set`, it currently isn't implemented
    (and maybe shouldn't—still pondering this question).

*   How the hell does the system *know* this?

*   `log new_set 42` gives you

        Error: 'as_set' is not applicable to a number;
        'as_set' requires { +facetted },
        but type 'number' is tagged with { basic, ecma, json, number, numeric }
        and doesn't match the method description.

*   this tells you that in CoffeeNode, data types & kinds are *tagged*.

*   Do you know of any other language / environment that tags data types? I think it's elementary!


############################################################################################################

# Types and Kinds

##   Tagged Data Types? Are you Kidding?

*   No, really.

*   A list is characterized as

    *   `list`—each type is tagged with its own name; important for what follows.

    *   `mutable`–you can modify the object. Frozen lists (tuples) miss this tag.

    *   `facetted`—meaning you can do the `x[ foo ]` thing.

    *   `indexed`—meaning the `foo` in `x[ foo ]` must be a number.

    *   `dense`—a sparse list would be missing this tag.

    *   `ordered`—unlike a POD, which misses this tag.

    *   `repetitive`—values may be repeated.

    *   `ecma`—this dignifies everything that is in ECMA's book.

    *   `json`—this data type can be fed to `JSON.stringify` / `as_json`

    *   `basic`—this pertains to the six data types earlier shown in the first group of JS native data
        types.




############################################################################################################

# Types and Kinds

##   Matching types and methods

*   A peek into `COFFEENODE/Λ/registry/jstypes`:

        #-----------------------------------------------------------------------------------------
        module.exports = ( require '../_read-registry-jstypes' )
          'boolean':        'ecma json basic'
          'function':       'callable ecma basic'
          'list':           'mutable indexed facetted dense ordered repetitive ecma json basic'
          'null':           'single-valued ecma json basic'
          'number':         'numeric ecma json basic'
          'pod':            'mutable facetted ecma json basic'
          'text':           'indexed facetted dense ordered ecma json basic'
          'jsarguments':    'mutable indexed facetted dense ordered repetitive ecma'
          'jsdate':         'mutable ecma'
          'jserror':        'ecma'
          'jsglobal':       'mutable facetted ecma'
          'jsinfinity':     'single-valued ecma'
          'jsnotanumber':   'ecma'
          'jsregex':        'ecma'
          'jsundefined':    'undefined ecma'

![](../resources/coffeenode-jstype-features.png)

*   A peek into `COFFEENODE/Λ/registry/specific-methods`:

        #-----------------------------------------------------------------------------------------
        module.exports = ( require '../_read-registry-specific-Λ-methods' )
          'add':                        '?facetted ?numeric'
          'first-value-of':             '+ordered'
          'names-of':                   '-indexed +facetted'
          'idxs-of':                    '+indexed'
          'normalize':                  '*'
          'words-of':                   '?text'

![](../resources/coffeenode-specific-Δ-methods.png)

*   When a 'type-specific Δ method' is called for, we check whether the description of the type of the first
    argument 'matches' the description of the method:

    *   `?` specifies an **optional tag**—at least one of the optional tags must appear in the type's tags;

    *   `+` specifies a **mandatory tag**—all mandatory tags must appear in the type's tags;

    *   `-` specifies a **forbidden tag**—none of the forbidden tags may appear in the type's tags;

    *   `*` specifies **match any type**—this may not be combined with other tags.

*   The matching process is not fully general, as more complicated patterns are not possible. You cannot
    currently say *match either ( a and b ) or else ( c and d )*. It appears to suffice though.

*   Because each type is tagged with its own name, you always have the choice to match types and methods
    simply by naming the types that the method applies to.

*   This is what happens with `words_of`. One could argue that this method is *much too peculiar to earn
    the distinction of being globally available* (which is not quite true anyway), but i strongly feel
    that the vast majority of methods in the `jstypes` library *should* attain that status.


############################################################################################################

# Types and Kinds

##   Keeping Peace, Avoiding Conflicts

*   OK so earlier we said that *even if we could fix the JS `===` operator, we shouldn't do that*: **don't
    change global semantics**.

*   Now what happens if one part of your application relies on `new_set 42` to fail miserably, while the
    other half of your brain is just rejoicing about having implemented `NUMBER/as_set`?

*   Because of the way CoffeeNode works, that very much *looks* like a global change, no?

*   And you would be right.

*   But there's help: enlightened reincarnated programmers that spent decades in DLL Hell in their
    earlier lives have enabled NodeJS modules to specify exactly which versions of dependencies
    a given module needs—meaning you can have several versions of the *same* module servicing the
    *same* application.

*   This is great news. I can sleep again. 未來光明。

*   Until i start scratching my head over how to exactly implement that.

*   Some day.

*   BTW we're sort of prepared for that—each CoffeeNode library module is (planned to become) an installable
    entry in the npm module registry and on GitHub.com.


############################################################################################################

# Method Call Styles

*   There are several ways to call library methods:

    *   Easiest, slowest: use Δ methods. This will do signal emission and argument
        validation behind the scenes; you only have to care for the type library responsible for the
        prinicipal noun to support the verb:

            push names, user_name

    *   Type-safest, a little faster: call library methods. This will still do validation, but this time
        the code is annotated: 'hey i believe i'm having a list here, so please check that and push
        a value'. Yo get type validation with no extra syntax!

            LIST.push names, user_name

    *   Less type-safe, yet a little faster: use library implementation methods:

            LIST._.push names, user_name

    *   Most problematic, fastest: use native JS object methods. In this particular case, the `push` method
        is without major flaws, although it acts a bit strange in case the pushee should happen to be
        `undefined`:

            names.push user_name

*   In JavaScript and other modern languages, things like collection
    manipulation etc. are built into the language, together with an API that
    manages access to these facilities. One could argue that these should
    *not* be concerns for the language proper, but a matter of libraries. In
    fact, i argue that even *the very syntax* of the language itself can be
    moved into a library.


############################################################################################################

#   How Σ works

*   A synchronous signal handling library

*   API:

    *   You create a signal by calling `Σ.emit`; first argument is the signal name, other arguments as
        required for the purpose:

            x = Σ.emit 'signal-name', 42, 108

    *   You catch a signal by passing a signal matcher and a listener function to `Σ.listen`; listeners
        take the signal as first argument and other arguments as required for the purpose:

            Σ.listen 'signal-matcher', ( signal, foo, bar ) ->
                ...
                Σ.update signal, 'new '

    *   When you look into the signal, you'll find the first optional argument got treated specially—it
        becomes `signal[ 'value' ]`:

            { '~isa': 'Σ/signal'
              name:                 'signal-name'
              arguments:            [ 42, 108 ]
              value:                42
              count:                1
              'is-mandatory':       false
              'listener-locators':  [ 'file:///route/to/script.cnd#39,3' ],
              data: null }



    *   You can rename a signal; this will modify signal[ 'name' ]

            Σ.listen 'signal-matcher', ( signal, arguments... ) ->
                ...
                Σ.update signal, 'new '


############################################################################################################

#   Beyond `*.cnd`

*   We've seen that we can do *lot* with `*.cnd` modules.

*   It's almost a new language.

*   Almost.

*   Except we have no handle to manipulate the non-verbal / syntactical behaviors—all the `x + y`,
    `x[ 3 ] = 42`, `a = [ 8, 7, 6, ]`, `f()` stuff is beyond our reach.

*   Also, we cannot extend the syntax of the language to add stuff.

*   I'd really like to explore what we can do if we had a flexible system where we can easily define
    new syntax—both to produce more formulaic, terse domain-specific languages (DSLs) and more eloquent
    idioms that approach natural language.

*   That has been done before—for the first time in history by Grace Hopper (1906–1992) who invented
    the programming language FLOW-MATIC between 1953 and 1959. A sample (slightly edited)::

              ....................................................................................
         (0)  input  inventory  file-a price        file-b;
              output priced-inv file-c unpriced-inv file-d;
              hsp d.

              ....................................................................................
         (1)  compare product-no (a) with product-no (b);
              if greater  go to operation 10;
              if equal    go to operation 5;
              otherwise   go to operation 2.

              ....................................................................................
         (2)  transfer a to d.
         (3)  write-item d.
         (4)  jump to operation 8.

              ....................................................................................
         (5)  transfer a to c.
         (6)  move unit-price (b) to unit-price (c).
         (7)  write-item c.
         (8)  read-item a;
              if end of data go to operation 14.
         (9)  jump to operation 1.

              ....................................................................................
        (10)  read item b;
              if end of data go to operation 12.
        (11)  jump to operation 1.

              ....................................................................................
        (12)  set operation 9 to go to operation 2.
        (13)  jump to operation 2.

              ....................................................................................
        (14)  test product-no (b) against zzzzzzzzzzzz;
              if equal  go to operation 16;
              otherwise go to operation 15.
        (15)  rewind b.

              ....................................................................................
        (16)  close-out files c; d.
        (17)  stop. (end)








############################################################################################################

#   Automatic Δ methods



############################################################################################################

#   Automatic Δ methods


############################################################################################################

#   Automatic Δ methods

new_$type
as_$type
isa_$type
validate_isa_$type


############################################################################################################

# XXXXXXXXXX

"Good architecture is necessary to give programs enough structure to be
able to grow large without collapsing into a puddle of
confusion."—Douglas Crockford, *Quality*, talk delivered on Yahoo!'s
internal Frontend Engineering Summit in 2007,
[http://www.youtube.com/watch?v=t9YLtDJZtPY](http://www.youtube.com/watch?v=t9YLtDJZtPY)

"you should use standard, easily recognizable idioms in preference to
overly clever techniques that purport to offer better
performance."—*Joshua Bloch, “Effective Java Second Edition”, pg. 242*


"All languages are equally powerful in the sense of being Turing
equivalent, but that’s not the sense of the word programmers care about.
(No one wants to program a Turing machine.) The kind of power
programmers care about may not be formally definable, but one way to
explain it would be to say that it refers to features you could only get
in the less powerful language by writing an interpreter for the more
powerful language in it. If language A has an operator for removing
spaces from strings and language B doesn’t, that probably doesn’t make A
more powerful, because you can probably write a subroutine to do it in
B. But if A supports, say, recursion, and B doesn’t, that’s not likely
to be something you can fix by writing library functions."—Paul Graham,
*Beating the Averages*




# Screaming Caps Libraries

Qualifications:

*   Stateless libraries are just collections of methods. They may be
    configured, but runtime configuration changes are not guaranteed to
    work and are generally frowned upon.

*   MULTIMIX compatibility—use `$ = module.exports` to define what is
    visible to the outside; always use `@` (i.e. `this`) to call library
    methods from other library methods.

## The Tower of Babylon

*   FlowMatic
*   CoffeeNode
*   IcedCoffeeScript
*   CoffeeScript
*   JavaScript
*   [bytecode]
*   C(++)
*   assembler
*   machine code

# The Oops of OOP

# The Vocabulary

## JavaScript

! != !== % %= & && &= \* */*= + ++ += , ----= ... ? ... : ... / /\* //
/= < << <<= <= = == === \> \>= \>\> \>\>= \>\>\> \>\>\>= \^ \^= Array
ArrayBuffer Boolean break case catch class const continue Date debugger
decodeURI decodeURIComponent default delete do do...while each else
encodeURI encodeURIComponent enum Error eval EvalError export extends
false finally Float32Array Float64Array for Function function get if
implements import in Infinity instanceof Int16Array Int32Array Int8Array
interface isFinite isNaN Iterator JSON label let Math NaN new null
Number Object of package parseFloat parseInt private protected public
RangeError ReferenceError RegExp return set static StopIteration String
super switch SyntaxError this throw true try TypeError typeof
Uint16Array Uint32Array Uint8Array Uint8ClampedArray unary + unary
*undefined uneval URIError var void while with x.name x[ 'name' ] yield
| |= || \~

## CoffeeScript

*\> ? .. ... \#\#\# ( x for x in y when x \> 0 ) [ 0 ... 10 ] while loop
until for of in @ is isnt class :: extends switch when else then """ '''
///...///

## CoffeeNode

### Specific Λ Methods

as-list absolute add all-of any-of as-html chrs-of clear contains
contains-only-digits contains-only-numbers copy delete-value drop-prefix
drop-suffix ends-with equals extend facets-of fill filter find
find-first first-idx-of first-value-of flatten flush flush-left
flush-right get glean-lines-of has idxs-of insert interleave intersect
invert is-empty is-even is-odd is-zero join join-comma join-commaspace
join-fields join-lines join-space join-tight keys-of last-idx-of
last-value-of length-of lines-of as-upper-case as-lower-case matches
merge names-of negate normalize partition pluck pop pull push
remove-doublets remove-key remove-slice remove-value remove-values
repeat replace reset rest-of reverse rpr rpr-all shuffle select set
shallow-copy slice smallest-idx-of sort split starts-with subtract trim
union-of update values-of words-of arity-of

### Generic Λ Methods

after as-hash-key as-json assemble bye call compose echo freeze
from-hash-key from-json get-descriptor infinity is-blank-text is-frozen
is-mutable is-nonempty-text is-nonnegative-integer is-positive-number
is-property-name-of is-shallow-frozen is-text-without-whitespace is-word
isa isa-boolean isa-function isa-integer isa-jsarguments isa-jsctx
isa-jsdate isa-jserror isa-jsglobal isa-jsinfinity isa-jsnotanumber
isa-jsregex isa-jsundefined isa-jswindow isa-list isa-null isa-number
isa-pod isa-text isnt-empty isnt-undefined
looks-like-a-property-descriptor make-revocable max min once
set-property shallow-freeze simple-equality-types type-of validate
validate-arity validate-is-empty validate-is-frozen validate-is-indexed
validate-is-integer validate-is-mutable validate-is-nonempty-list
validate-is-nonempty-text validate-is-nonnegative-integer
validate-is-nonnegative-number validate-is-nonzero-number
validate-is-positive-integer validate-is-positive-number
validate-is-text-without-whitespace validate-is-word validate-isa
validate-isa-boolean validate-isa-function validate-isa-jsarguments
validate-isa-jsarraybuffer validate-isa-jsctx validate-isa-jsdate
validate-isa-jserror validate-isa-jsglobal validate-isa-jsinfinity
validate-isa-jsnotanumber validate-isa-jsregex validate-isa-jsundefined
validate-isa-jswindow validate-isa-list validate-isa-null
validate-isa-number validate-isa-pod validate-isa-text
validate-isa-text-or-anyregex validate-isa-text-or-jsregex
validate-isa-text-or-regex validate-isnt-empty validate-isnt-undefined
wait-for-keyboard xray

### Nouns

BITSNPIECES COFFEESCRIPT DIFF ENGINE FLOWMATIC FS HASH HTML JQUERY
MACROS MULTIMIX PANDOC QUANTITY REGEX ROUTE SET TRM TYPES VALIDATE Δ Λ Σ

JSARGUMENTS JSDATE JSERROR JSGLOBAL JSINFINITY JSNOTANUMBER JSREGEX
JSUNDEFINED

BOOLEAN FUNCTION LIST NULL NUMBER POD TEXT
