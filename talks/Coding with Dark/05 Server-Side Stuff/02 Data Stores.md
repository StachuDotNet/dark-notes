# [13] Data Stores


## [1] How Data Storage works with dark
Datastores in Dark are key-value based.
	persistent hash-maps


## [1] Creating a data store
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


## [1] Inserting some records
```
let key = DB::generateKey // (or uuid or something)
DB::set { userId : userId; name: "Paul"; pets: [] } key
```


## [4] Querying
### [30s] all records
- `DB::getAll`
- `DB::getAllWithKeys`
- `(DataStore table) -> Dict`
- 
### [1] multiple records
- by key
	- `DB::getManyWithKeys`
	- `DB::getMany`
	- `getExisting` 
	- `(List keys, DataStore table) -> List`
	from many values in table by keys (ignoring any missing items), return a [value] list of value
- by record field
	- `DB::queryExactFields`
	- `DB::queryExactFieldsWithKey`

### [1] single records
- by key
	- `DB::get`
- by record field
	- `DB::queryOneWithExactField`
	- `DB::queryOneWithExactFieldWithKey`

### [1] by query (experimental SQL compiler)
- allows taking a datastore and a block filter
	- note that this does not check every value in the table but rather is optimized to find data within indexes
- `DB::query Test \value -> value.name != "Paul"`
- `DB::queryWIthKey`
- `DB::queryOne`
- `DB::queryOneWithKey`
- `DB::queryCount` `(datastore table, Block filter)` -> `Int`
	- returns the number of items from table for which filter returns true. Note that this does not check every value in table, but rather opmized to find data with indexes. errors if query is not compatible (yet).

### [30s] aggregations
DB::count


## [30s] Deleting records
- DB::delete (key:Str, table:DataStore) -> Nothing
- delete key from table
- DB::deleteAll (table:DataStore)->Nothing
- delete everything from table


## [30s] Updating records
- DB::set <val:Dict, key:Str, table:DataStore> -> Dict
- upserts val into table, accessible by key


## [1] Creating references between DBs
- this canvas shows the way to create a reference between two datastores; in this case between Dark employees and their pets: https://darklang.com/a/sample-datastore
- Users have a pets field, which is a list of strings. The keys for the pets are added to that list


## [2] Migrations, Locking, and Unlocking
- You can edit the DB's schema (col names and types) until it has data in it, at which point it "locks"
- If you are still in development and don't need the data, creating a REPL and deleting all data in a DB will unlock it (DB::deleteAll)
- Once you have traffic, datastore migrations are manual.
- To change your schema, create a new datastore with the new schema
- Once you are satisfied with your code, run the REPL to test it (if you are just testing, delete the data after.)
- The new datastore should be locked, and you can use another REPL to verify the output looks correct. Set up each reference to the datastore to use the new one datastore within a feature flag.
- when you're ready to change your traffic to use the new datastore, run the migration, and commit each feature flag

ex.

```
DB::getAllWithkeys Blogposts
|> Dict.map \key, value -> DB::set {title: value.title, body: value.body, author: value.author } key BlogPosts_v2
```


## [1] Other
- DB::count
- DB::keys
- DB::schemaFields (table:DataStore) -> List
- fetches all the fieldnames in a table
- DB::schema (table:DataStore) -> List
- returns an Object representing { fieldName: fieldType } in table


## [1] Using external data stores

## thinking in key-value