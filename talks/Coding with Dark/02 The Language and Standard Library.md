[27] The Language and Standard Library
    [1] Meta
        Dark (will be) a statically-typed functional/imperitive hybrid, based loosely on ML. It's a high level language w/ immutable values, GC, and support for generics / polymorphic types. it's somewhat similar to ocaml/elm, and is influenced by various other languages
        the type system is most similar to Elm/Haskell/ReasonML/OCaml/Rust/F#. values are immutable (except refs, but those don't exist yet?)
            based on expressions!
    [3] Flow Control
        [1min] `let`, expressions, blocks, scope, and implicit returns
            "implicit returns"
                The value of an expression is the last value in that expression.
                A handler or function will return the result of the last expression within it.
        [30s] `if`/`else` expression
            currently supports truthiness, but will fix this
            they don't short-circuit (yet)
            ifs not allowed w/o matching else - will be eventually relaxed
        [30s] `match` expressions
            the match expression is used to destructure complex types, such as the Option type and the Result type. It can also be used similarly to switch statements in other langauges (?).
                By default, functions that return Otpion or Result go to the Error Rail. once you remove the function from the Error Rail, use match for destructuring
        [30s] `|>` pipelining
        [30s] imperitive programming isn't supported currently, but planned
            dark does not yet support statements without the extra let. when you hit 'enter' at the end of a line that has a return value, we assume you wan tto make a new expression. since that would be the last expression 9and returned) we will automatically add the let _ = to the expression for you
            statements, refs, mutable data structures, etc.
            dark does not have a `for` loop. It has `List::map`. This allows you to do something to a collection of objects in a list
    [15] Core Types and their Modules
        (intro slide noting core types)
        [30s] Common fns and operators
            `equals` and `notEquals`, `==` and `!=`
            `toString`
            `::isNull`
        [30s] `Bool::`
            `::and`, `&&`
            `::or`, `||`
            `::xor`
        [2.5] `Date::`
            [30s] Creating `Date`s
                `::now()`
                `::today()`
                `::parse`
                `::fromSeconds`
            [30s] Creating `Date`s from Other `Date`s (mutating `Date`s)
                `::add`
                `::subtract`
                `::atStartOfDay`
            [30s] Comparisons
                `::<`, `::lessThan`
                `::>`, `::greaterThan`
                `::<=`, `::lessThanOrEqualTo`
                `::>=`, `::greaterThanOrEqualTo`
            [30s] Stringifying
                `::toString`
                `::toStringIso8601BasicDate`
                `::toStringIso8601BasicDateTime`
                `::toSeconds`
            [30s] Extracting components of a `Date`
                `::minute`
                `::second`
                `::hour`
                `::weekDay`
                `::day`
                `::month`
                `::year`
        [30s] `Crypto::`
            `::md5`
            `::sha1hmac`
            `::sha256`
            `::sha256hmac`
            `::sha384`
        [2] Numbers (`Int`, `Float`) and `Math`
            Some operators
                `%` (modulo)
                `+`, `-`, `*`, `/`
                `<`, `<=`, `>`, `>=`, `==`, `!=`
            Int::
                Comparisons
                    `::greaterThan`, `::greaterThanOrEqualTo`, `::lessThan`, `::lessThanOrEqualTo`
                    `::max`, `::min`
                Operations
                    `::clamp`
                    `::absoluteValue`
                    `::add`, `::divide`, `::subtract`, `::multiply`
                    `::sqrt`
                    `::remainder(int value, int divisor)`
                    `::sum`
                    `::mod`
                    `::power`
                `::toFloat`
                `::random(int start, int end) -> Result`
            `Float::`
                Operations
                    `::add`, `::subtract`, `::multiply`, `::divide`
                    `::max`, `::min`
                    `::power`
                    `::sum`
                    `::sqrt`
                    `::absoluteValue`
                    `::negate`
                Rounding
                    `::roundTowardsZero`
                    `::round`
                    `::roundUp`
                    `::roundDown`
                    `::floor`
                    `::truncate`
                    `::ceiling`
                    `::clamp`
                Comparisons
                    `::greaterThan`
                    `::greaterThanOrEqualTo`
                    `::lessThan`
                    `::lessThanOrEqualTo`
            `Math::`
                Geometrical Constants
                    `::pi()`
                    `::tau`
                Angle/Radian Conversions
                    `::radians`
                    `::degrees`
                Common Geometric Functions
                    `::sin`, `::cos`, `::tan`
                    `::sinh`, `::cosh`, `::tanh`
                    `::acos`, `::asin`, `::atan`, `::atan2`
        [2] `String::`
            [15s] Create from Nothing
                `::newLine()`
                `::random`
            [15s] Comparisons
                `::contains`
                `::endsWith`, `::startsWith`
                `::isEmpty`
            [30s] Transformations
                `++`, `::append`
                `::first`, `::last`
                `::trim`, `::trimEnd`, `::trimStart`
                `::toLowerCase`, `::toUpperCase`
                `::join`
                `::padEnd`, `::padStart`
                `::reverse`
                `::replaceAll`
                `::slugify`
                `::htmlEscape`
                `::dropFirst`, `::dropLast`
                `::slice`
                `::prepend`
            [1] Conversions
                `::length`
                `::split`
                `::toList`
                (of characters)
                `::fromList`
                `::toBytes`, `::toFloat`, `::toInt`, `::toUUID`
                `::base64Encode`, `::base64Decode`
                `::fromChar`
            `::foreach`
            `::digest`
        [3] `List::`
            [30s] Create from scratch
                `::range`
                `::empty()`
                `::repeat`
                `::singleton`
            [30s] Select 0-1
                `::findFirst`
                `::last`
                `::member`
                `::getAt`
                `::head`
                `::randomElement`
            [1] Create from existing
                `::append`
                `::filter`
                `::map`, `::map2`, `::map2shortest`
                `::sort`, `::sortBy`, `::sortByComparater`
                `::reverse`
                `::tail`
                `::take`
                `::uniqueBy`
                `::takeWhile`
                `::filterMap`
            [30s] Comparisons and Checks
                `::length`
                `::isEmpty`
            [30s] To/From other types
                `::zip`, `::unzip`
                `::zipShortest`
            `::flatten`
            `::drop`
            `::dropWhile`
            `::fold`
            `::indexedMap`
            `::interleave`
            `::interpose`
            `::push`, `::pushBack`
        [2] `Dict::`
            Create from scratch
                `::empty()`
                `::singleton`
            Create from Existing
                `::set`
                `::merge`
                `::filter`
                `::filterMap`
                `::remove`
            `::get`
            `::member`
            Comparisons
                `::isEmpty`
            Conversions - Going To/From Other Types
                `::toJson`
                `::fromList`
                `::fromListOverwritingDuplicates`
                `::toList`
                `::keys`
                `::values`
            `::size`
            `::maps`
            ?
        [1] `Option::`
            `::map`
            `::map2`
            `::withDefault`
            `::andThen`
        [2] `Result::`
            `::map`
            `::map2`
            `::mapError`
            `::fromOption`, `::toOption`
            `::andThen`
            `::withDefault`
        [1] Records
        [15s] `UUID::generate()`
        [30s] `JSON::`
            `::parse` 
            `(Str json) -> Result`
        [30s] `Bytes::`
            ::base64Encode
            ::hexEncode
            ::length
        [30s] TODOs
            sets
            units
            tuples
    [2] User-Defined Types
        dark currently has limited support for user-defined types. currently we support inline definition of records but do not support defining record types explicitly.
        record types are actually implemented under the hood, and we intend to use them to support typed datastores, API contracts, and static types.
        (future) user defined enums
        all types are and will be versioned
        types (will be) defined outside the context of the canvas
    [2] Creating functions
        simple
        lambdas - anonymous functions
            `List::map [5, 10, 11] \var -> var + 2`
        don't support functional lang concepts such as partial application and functions as first-class values
        functions don't live on the canvas, but a bit ethereal
        built-in fns are well-versioned: we frequently deprecate old functions and add updates. when we deprecate old versions, your code does NOT change and you keep using the old ones. we intend to support automated refactoring and updating in the future.
    [30s] Incompletes
        a developer may write programs where some parts are incomplete as they build out the code. structures containing incompletes are themselves incomplete. Incompletes are never returned to end users, cand cannot be stored in a datastore. Returning an incompletefrom an HTTP handler will result in a 500 error
    `emit`
    [1] Secrets
        some values are senstive, for example passwords and credit card numbers
        currently dark supports the `Password` type which is never saved directly or sent to the editor
        in the future, Dark will allow you to specify types of senstive values, preventing them from being stored in logs, and allowing a team to limit who has access to these values in the Dark.
    [3] Error Rail
        functions that return Result or Option are handled by the error rail by default
        the error rail allows you to keep writing code along the "happy path" without stopping.
        When you call a function which might not succeed, you can keep going, rather than handling the error cases immediately
        lines/fns that return to the error rail have signifiers on the right
            | = no happy or error cases hit (before any run)
            (/) = error
            (o) = success
        in the case of a success, code will keep running
        in the case of a failure, the execution will end (similiar to throwing an uncaught exception)
        taking fns off the error rail
            when you are ready to handle error cases, you remove them from the rail by using the editor command take-function-off-rail (open command palette) ON THE FUNCTION NAME. This will unwrap the values