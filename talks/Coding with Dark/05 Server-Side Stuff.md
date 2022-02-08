[20] Server-Side Stuff
    [5] Workers - CRON jobs and event stuff
        [2] Intro
            "how do I access the data from `emit` in the worker?"
                the data from the `emit` is available in a variable called `event` from within the worker. Its type will be whatever was passed to emit.
            Dark supports doing work asynchronously outside the context of an HTTP Handler using a Worker. Each worker has a queue of messages, which are processed loosely in-order executing the code within the Worker once for each message. Messages are created by calling `emit` from any other code, and can contain arbitrary event data
            Workers can be created from the omnibar or sidebar. similar to http handlers, calling `emit` with a nonexstant Worker name will populate that worker int he 404 sidebar section, allowing it to be created. Creating a Worker from a 404 may result in a delay when executing the first message. When a new worker is created, it immediately processed the first message in the queue, but returns Incomplete because no code has been written yet. This casues the message to get automatically retried in 5 minutes, but until then it may look like o messages are being processed
            workers will automatically process each message. the `event` data passed to `emit` is available in the Worker as a special variable `event`. This can be of any type but is often convenient to use a dict holding many other values.
            Counts
                if there are multiple items in the queue, a live count of queue items will appear at the left
                the last 10 processed events will show as traces that you can use for debugging purposes
        [30s] Traces
            the trace information on Cron will show the most recent 10 times the Cron has run. a Cron never has input data, so the trace will always say No input parameters.
        [30s] Workers + 404s
            workers receive events via the emit expression and will appear in the 404 section if you emit to a non-existent worker.
        [1] Queuing, Cadence Control, and Manual Execution
            queuing and a basic example
            cadence control
            execuution
                manual
                pause/debug
            `emit { messageId: Uuid::generate; createdAt: Date::now } "my-worker"`
            Crons can have intervals of:
                daily, weekly, fortnightly, hourly, every 12hrs, every minute
            crons run automatically once per interval
            To run a Cron on-demand, use the replay button the upper right. Running on-demand does not affect the next scheduled runtime
            Can I set the exact time my cron will run?
                No, not currently. We plan to eventually support this.
            you can pause the queue by hitting the "pause" button in case of operational issues or if you'd like to stop and debug
            Can I pause a Cron to keep it from running?
                No, not currently. we plan to eventually support his
            There's currently no guarantee when within an interval that the cron job will run.
            workers across a canvas execute in parallel, and multiple messages for a worker may be processed in parallel
            a message should be processed within a minute of being emitted. typically, processing begins within a few seconds
            Can I call `emit` from a Worker?
                Yes. This can be useful to do fan-out of work or batch processing of data.
                In fact, you can `emit` to the same Worker that's processing the message
        [30s] Cautions
            Workers do not guarantee exactly-once delivery.
                adding a unique UUID to every message can be useful in keeping track of which messages have been seen by your worker already
            Crons will not alert you of failures unless you write logic to do so
            workers will not alert you of failures unless you write logic to do so
            A message that causes a worker to throw a runtime error will be silently ignored and the worker will process the next message.
                we plan to eventually add more error handling capabilities such as automatic retries and dead-letter-queues
    [13] Data Stores
        [1] How Data Storage works with dark
            Datastores in Dark are key-value based.
                persistent hash-maps
        [1] Creating a data store
            When you create a new datastore, you specify the schema for the record
            Keys
                The key is a unique identifier for each record, and is always of type `String`.
                The key is not visible when looking at the Datastore's schema on the canvas.
                You cannot mark a record field as the key, but you can use the same value for the feld and the key when using Db::set
                Some common key choices:
                    a unique field (like `userId`)
                        If the field is not already a string use `toString`
                    a unique identifier generated programmatically (`DB::generateKey``)
            Values
                In the future, datastores will be defined by type, but for now you manually create the schema.
                Available types are: `String`, `Int`, `Bool`, `Float`, `Password`, `Date`, `UUID`, `Dict`, and `List`s of those
        [1] Inserting some records
        ```
        let key = DB::generateKey // (or uuid or something)
        DB::set { userId : userId; name: "Paul"; pets: [] } key
        ```
        [4] Querying
            [30s] all records
                `DB::getAll`
                `DB::getAllWithKeys`
                `(DataStore table) -> Dict`
            [1] multiple records
                by key
                    `DB::getManyWithKeys`
                    `DB::getMany`
                    `getExisting` 
                    `(List keys, DataStore table) -> List`
                    from many values in table by keys (ignoring any missing items), return a [value] list of value
                by record field
                    `DB::queryExactFields`
                    `DB::queryExactFieldsWithKey`
            [1] single records
                by key
                    `DB::get`
                by record field
                    `DB::queryOneWithExactField`
                    `DB::queryOneWithExactFieldWithKey`
            [1] by query (experimental SQL compiler)
                allows taking a datastore and a block filter
                    note that this does not check every value in the table but rather is optimized to find data within indexes
                `DB::query Test \value -> value.name != "Paul"`
                `DB::queryWIthKey`
                `DB::queryOne`
                `DB::queryOneWithKey`
                `DB::queryCount` `(datastore table, Block filter)` -> `Int`
                    returns the number of items from table for which filter returns true. Note that this does not check every value in table, but rather opmized to find data with indexes. errors if query is not compatible (yet).
            [30s] aggregations
                DB::count
        [30s] Deleting records
            DB::delete (key:Str, table:DataStore) -> Nothing
            delete key from table
            DB::deleteAll (table:DataStore)->Nothing
            delete everything from table
        [30s] Updating records
            DB::set <val:Dict, key:Str, table:DataStore> -> Dict
            upserts val into table, accessible by key
        [1] Creating references between DBs
            this canvas shows the way to create a reference between two datastores; in this case between Dark employees and their pets: https://darklang.com/a/sample-datastore
            Users have a pets field, which is a list of strings. The keys for the pets are added to that list
        [2] Migrations, Locking, and Unlocking
            You can edit the DB's schema (col names and types) until it has data in it, at which point it "locks"
            If you are still in development and don't need the data, creating a REPL and deleting all data in a DB will unlock it (DB::deleteAll)
            Once you have traffic, datastore migrations are manual.
            To change your schema, create a new datastore with the new schema
            Once you are satisfied with your code, run the REPL to test it (if you are just testing, delete the data after.)
            The new datastore should be locked, and you can use another REPL to verify the output looks correct. Set up each reference to the datastore to use the new one datastore within a feature flag.
            when you're ready to change your traffic to use the new datastore, run the migration, and commit each feature flag
            ex.
            ```
            DB::getAllWithkeys Blogposts
            |> Dict.map \key, value -> DB::set {title: value.title, body: value.body, author: value.author } key BlogPosts_v2
            ```
        [1] Other
            DB::count
            DB::keys
            DB::schemaFields (table:DataStore) -> List
            fetches all the fieldnames in a table
            DB::schema (table:DataStore) -> List
            returns an Object representing { fieldName: fieldType } in table
        [1] Using external data stores
        thinking in key-value
    [1] Writing Tests
        At the moment, Dark does not provide an out of the box testing framework. However, it's fairly straightforward to write tests using REPLs and Crons.
        You can write logic to check that the printed messages say exactly what we want them to without having to read every message. In this example, a portion of the message that gets printed when the name is not long enough has been accidentally deleted so a warning is displayed.
        Will dark support testing?
            dark will have integrated unit testing for fns and handlers. We plan to generate tests for you based on types, and to trivially allow you to turn past requests into unit tests.
            It should also be possible to run integration tests that einteract well with dark - we plan to build features that support this use case.
    [1] Secrets
        `Password::hash`
        `Password::check`