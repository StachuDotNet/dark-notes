note: this draft is very early-days, and was drafted fully before I officially joined Dark as an employee
I suppose these were more 'stolen notes' than anything meant for instruction, but I'd like to keep them handy.

Building Dark
    Technology used by Dark
        VS Code and Dev Containers
        Postgresql
        ReScript
            We currently use ReScript using the OCaml format (*.ml files) for the client.
            string interpolation: {|j|some string with x: $x and also $|j}
            record syntax is funny
            ```
            { field1 = 56
            ; field2 = true
            ; field3 = "asdf"
            }
            ```
            Lambdas: `list |> List.map ~f:(fun elem -> elem + 2)`
            dictionaries are slightly challenging to use in ReScript (?)
            named params
                `Option.withDefault ~default:5 (Some 5)`
                let myFunction (regularParameter: int) ~(namedParam: string) = 
            optional params
                `let myFunction ?(optionalParam = 3) (regularParam: int): int = `
            Modules
                ReScript has a complex module system, which takes some time to grasp. Modules can have parameters, have inheritance, and each other these features uses a complicated, difficult to grasp syntax.
                We only barely use modules in the Dark codebase, so here's what you need to know:
                    all files are automatically modules. Note that in the backend, modules need to have their directory names included, but not in the client.
                    using a module is simple
                        `open MyModule` (* all function and types are available *)
                        `module M = MyModule2` (* access members as if the module was called M *)
                    create a module:
                    ```module MyModule = struct
                      type t = int
                      let myFunction x = x + 2
                    end```
        Docker
        F#
        OCaml
    How things work now
        Glossary
            Toplevel
            structural toplevel
            function space
            canvas
            caret
            cursor
            AST
            live value
            input value
            trace
            field
            structured editor
        High-level repository break-down
        Let's trace a request
        A Tour of the Editor
            the editor is in `client/`. We refer to it is as the client or the editor, but not the frontend.
            It started life as an Elm application, and still uses the Elm architecture
            the fluid editor
                whenever you type code, you are using the 'fluid' editor. This is a structured editor that is designed to feel like text when typing
                a structured editor means that you cannot write invalid code. the editor edits the AST of the pgoram
            analysis
            autocomplete
            Elm Architecture (link out somewhere)
        A Tour of the Backend
            Libexecution and the editor
                `LibExecution` is the "execution engine" of Dark. The same code is compiled to native code to respond to HTTP handlers, and is also compiled to WASM to run in the editor.
                The play button on functions and on handlers executes the code on the server, returning updates to the trace of those functions. In all other cases, the editor runs code in the JS version, filling in the results of the functions it doesn't have acess to from the traces
            A "trace" is a combination of an `event` (referred to in Dark as an "input value" and in the code as a `StoredEvent`), and the arguments and results of functions called during a trace
            The Standard Library:
                split between `fsharp-backend/src/LibExecution/StdLib` (for fns available on the client _and_ backend) and `fsharp-???`
            Serialization
                Dark programs are directly serialized in our DB, and loaded for any requests that come in. Each change in the editor creates an Op for that toplevel (DB, handler, function, etc.). That is appended to the list of previous ops for that handler, and serialized into the DB in an efficient binary format.
                The ops contain the entire handler or function, which is much slower than it could be (part of the reason undo is so slow)
                We cache/denormalize the current code for each handler, which makes requests fasts
                one downside of this is that we have to be very careful what changes we make the Dark AST defintiion. There is a doc in the dark repo discussing this in more detail
        Client/backend communication
            the client sends ASTs to the backend to save and to run the programs in the cloud. The client also fetches expressions from the backend to display and edit them. It does this over JSON.
            The F# backend has automatic JSON serializers and deserializers, using automatic serializsers of types in `api.fs`. The client has hand-written serializers in `client/serc/core/Encoders` and Client/src/code/Decoder.ml`. The OCaml compiler will prompt you to add new encoders, but not decoders, and is generally a simple process.
        The journey of an HTTP request
            google load balancer
            nginx sidecar container
            Dark BwdServer, namely `runDarkHandler`
            if it's a 404, the event is stored in the `stored_events_v2` table and sent to the client via Stroller (a sort of reverse proxy, written in Rust)
            if a page is found, the request is turned into an "input value," which is turned into a set of variables and passed into `Execution.runHttp` which calls `Interpreter.eval`, first running the code through the Dark middleware in `LibMiddleware`
            `AST.eval` runs the Dark code, saving parts of the trace as it goes. input values, function args, and return values are saved in Posgres tables `stored_events_v2`, `function_arguments` and `function_results_v2`
            A trace is pushed to Pusher, which forwards it to the editor, where it appears as a dot on the cavcas
            when a user clicks on the trace, the trace is loaded from the server. a web worker named `Fetcher` fetches the trace in the background, decodes it, and sends the value to the editor. On the server-side it is fetched from the `ApiServer`
        Editor vs. Production (how code runs)
            Code runs in 2 places in Dark, in the Editor and in Production. In production, we have a Kubernetes cluster of interpreters with HTTP servers which are connected to a database, connected to the internet via Google Cloud infra. that run Dark programs
            When requests are made in production, we save their inputs and intermediate values (combined, these form a "trace," discussed below). Those are sent to the client
            The Dark interpreter is also compiled to JS and is available in the browser in the client. The traces are sent to the JS-compiled interpreter, which uses their results to fill in for functions which can't be run ont he client (such as DB fns)
        when a change is made, typically an `AddOp` modification is made. That modification is returned by many of the fns that edit programs,a nd it's processed in `Main.ml`
        The path of an edit (front-end stuff)
            there are 2 (or more?) kinds of structured editors:
            (originally, everything was in Forms. Fluid was only introduced later)
                Fluid (for Code)
                    this is the code journey when you type a character
                        event is processed by `FluidKeyboard.ml`, creating a `FluidMsg`
                        `Fluid.ml` recognizes the `FluidMsg`, calling `updateKey`
                        `updateKey` looks at the current caret position, and the "tokens" before and after the caret, to figure out what'st  happening
                        `updateKey` makes a transformation based on whatever it decided
                        the AST for that code is transformed
                        `FluidAutocomplete` may be regenerated, if needed
                        the browser's animatino event fires, causing a re-render. The AST is "tokenized," essentially pretty-printing it as HTML, which then renders
                        an API call is made to send the change to the server (detailed below)
                Forms (for DBs, handlers, fn params, and other medata)
                    this is the code journey when you type a character
                        the event is processed by `KeyPress.ml`
                        the contents of `m.complete.value` are updated (this is where the value in the forms box is stored)
                        the `Autocomplete` values are regenerated
                        after pressing enter, or clicking away, the change is made
                        an API call is made to send the change to the server (detailed below)
                this change is accepted by `api.ml` in the backend, where it is decoded, applied to the program, and then saved into the database. Saving the program involves a special binary serialization format, in `Serialization_format.ml`
                After being saved, it is sent to `Stroller`, a "sidecar" process in Rust that sends it to Pusher.com, the websockets vendor we use. This is sent to other clients which then update their programs. It is also sent to the original editor, who ignores it.
                    Question: why do you use a "websockets vender" rather than using a library or rolling it yourself? what does pusher.com provide? (resilience?)
        ASTs
            An "AST" is an "Abstract Syntax Tree." The simple explanation is that it's a set of "classes" and "objects" representing programs. 
            In Dark it's defined in `FluidExpression.ml`
                `type expr = | EInteger of ....`
                `type pattern = | FFPString of ...`
                each expression has an `id` that is used to uniquely refer to the expression. this is used when editing programs, and to relate live values from the analysis engine to the display in the editor.
            these definitions are in ReScript
        Toplevels
            "toplevel" is the generic name for a par of a Dark program, either an handler (HTTP, Cron, Worker), a function, a type, or a database
            structural toplevels are toplevels which are part of the structure of your pgoram: handlers and DBs. These live on the canvas
            Other toplevels are non-structural and they don't live on the canvas or really anywhere. We have started to affectionately refer to the display for these as the "function space")
        Traces and Live values
            when a request is made to a prod server, the inputs (typically a HTTP rqeuest) are saved. We also save intermediate results of fns which are called during the request. Together, these comprise a Trace. Traces are shown in the editor and users can choose between them.
    How to make changes, and what changes to make
        General notes on making PRs/contributions
            Dark uses a fork model for constributions. Go to the Dark repo in your browser and click `fork` to add a fork
                Then change your local repo to use the fork:
                `git remote rm origin`
                `git remote add origin https://github.com/stachudotnet/dark.git`
            Issues
                `good first bugs` and `good early bugs`
            code checklist
                never change an existing dark standard library function. Make a new one (with a new versino) and deprecate the old one. It is however safe to fix the type signature on an existing function, or to change its docstring.
                don't change existing serialized types, as that breaks the serializer. Serialized types are in Serialization_format.ml and have `[[@deriving ... bin_io]]` next to their type definitions
                the code redering step (FuildEditorView.toHtml) is extremely performance sensitive, and it's important that we don't add any steps that checks the entire AST on each token. Doing passes of the AST at the start (not in the loop) is fine
                format the code (is this done automatically now?)
            writing a successful PR message
                explain the problem you're solving. If this is explained elsewhere, link to it
                explain how your solution addresses the problem
                highlight any choices you've made in the impl.
                make clear what the product and user-facing changes are, esp. if it could break anything for existing users
                if the change is in the editor, include a before/after screenshot or gif
        Ongoing tasks
            Writing tests
            Porting to F#
        Adding new features
        Adding tests
            Adding your first test
                find function to test
                    see what functions exist:
                    (copy this stuff, put in .sh, and just tell people to look there. no one should have to copy this stuff.
                adding the test
                    unit tests for Dark functions are in `fsharp-backend/Tests/testfiles/*.tests", typically named after the module we're in. See testfiles/README>md to see the format
                    example: `Float.multiply_v0 26.0 0.6 = 13.0`
        Non-technical contributions
    Where to ask for help
    Devlog
        history of dark
        where dark's going
    Unorganized
        If you're interested in helping out, please do! the easiest place is to start by [porting standard library functions]; there are more than 200 which need to be ported! It's really easy: simply uncomment the function and fix the small things that pop up. THere's even a [contribution tutorial in the docs]. We also have instructions on how to [get started on our codebase], and a lot of [issues on GitHub]; also please don't be a stranger in the [Dark Slack]
        Dark is a programming language, structured editor, and infrastructure - all in one - whose goal is to make it 100x eaiser to build backend services.
        webservers
            There are two conceptual webservers used in Dark. The API server handles the editor for Dark users, logging people in, fetching traces, editing code, etc. The other server, which I'm calling the BWD server (for builtwithdark.com), serves HTTP requests for Dark programs to "grand-users" (our users' users).
            These two servers are split into two projects.
        Tablecloth
            a few years ago I released [a standard library for OCaml and ReScript called Tablecloth.] The idea is that it's a thin layer on top of the built-in standard libraries that have a common API. Although the idea was that we could re-use code in OCaml and ReScript, that proved challenging even with Tablecloth, so now the main use is to lower the cognitifive load: we can just use one set of functions in different languages, never having to pause to think what it's called in this language, or if the signature is different.
            To smooth the transition to F#, I implemented [Tablecloth in F#] too. Now, tablecloth represents a library which supports 3 different MLs and is also extremely similar to the Elm standard library (on which it's based).
            I intend to push that upstream to the [Tablecloth repo] soon, as we'll release a new version of Tablecloth _any day now_.
        Fuzzers
            Fuzzers create random inputs and test if certain properties hold up. I've been testing properties like:
            does calling this function return exactly the same result in both the OCaml and F# implementsions?
            do serialization functinos work for all cases?
            if I call the new F# versino of pretty-printer, is the text identical? Do I get code that can be read by the old OCaml deserializer? (even if the text is not identicial)
            is the user data stored in the DB understood to have the same meaning in both impls?
            is the hash of a particular Dark value identical for both impls (necesarry to match traces to their callers)?
        OCaml interop
            Dark code is stored in our Postgres database. For speed, it's stored using a fast binary serialization using an [OCaml library built by Jane Street], that has no compatible library in F#. As such, the simplest and most reliable way forward in the short term was to directly interface to the OCaml deseriazation code.
            The process to do this is a little involved. The functions are exposed from OCaml to C, using the [OCaml FFI]. F# can call [C Functions using the dotnet FFI] which is called P/Invoke. Of course, between the [intermediate layer] in C that nreads the bite from rone runtime and passes it safely to the other...
        A new lower IR
            In compiler terms, an Internal Representation is a set of types or classes representing the program. A compiler typically has multiple IRs representing different stages of compilation; it's common to have an AST to represent the program pretty exactly, then a high-level IR, medium-level, and low-level IR that each have fewer high-level constructions and more low-level.
            In the old representations of Dark, we only had one: the AST. Now we have two: [ProgramTypes] and [RuntimeTypes]. The major difference is that RuntimeTypes only has information that's useful to run the code. In particular, the different ways to call functions (pipes, bin-ops, lambdas, and regular fn calls) are all reduced to one type (called [Apply]).