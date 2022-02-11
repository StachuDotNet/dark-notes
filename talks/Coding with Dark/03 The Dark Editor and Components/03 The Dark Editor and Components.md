# [10] The Dark Editor and Components
## [2] Handlers and Editor Overview/Tour
- When building in Dark, you are able to create five major backend components, called Handlers
- API Endpoints (REST)
- Background workers (with built-in event/queue system)
- Scheduled jobs/cron
- Persistent Datastores (key-value / hash-map)
	- all datastores and are key-value store. a persistent has-map/dictionary. When looking at a datastore you'll see all handlers that calll the datastore
- REPLs (internal tools)
	 - triggerd only by the develper and frequently relies on the play button
- These components live on your canvase. They are created from the seidebar or by hitting ctrl k to bring up the omnibox
- All of these components support core Dark functionality
	- Trace data and live values
	- Implicit returns and pipelines
- These components DO NOT require you to set up any additional infrastructure or deployment processes. As soon as you create them, they're available and update as you write code. (deployless)
- Dark code is meant to be written in these hanndlers. We always recommend writing code within these components, and then extracting that code into a function using `ctrl \\` as needed

## [1] The Ombibox and command palette
- includes common refactoring tools
- If you're looking to do something that is not immediately available, chances are it's in the command palette, accessed by hitting `ctrl-\\`
- extract a function or variable for reuse
- create a type
- wrap the current expression in a let
- insert a let-expression above this one
- wrap an expression if an if, if-then or if-then

## [2] How to pipe
- to start a pipeline, use `|>` at the end of of the expression you are piping
- once you are in a pipeline, hitting return at the end of the expression will continue the pipe
- if you need to pipe a specific susbset of an expression, you can select it and then hit shift+enter

## [2] Traces and Play/Replay
- functions that do not have side effects (like `Int::add`) run automatically
- functions w/ side effects have play buttons. Press the play button to execute the fn for the selected trace
	- a replay icon will show up
	- the replay icon indicates the function has been executed already. pressing it executes the code for the trace again
- Live values
- you can seemingly click to replay only subsections of the code?
- whenever a handler has been executed fully at least once, we have a _trace_
- the result of the _trace_ is shown below the handler definition
- http handlers
	- send requests to dark _before_ writing code. postman, front-end, curl, whatever
	- ( go into this for a bit.)
	- then find the request int he 404s area

## [1] Unlimited Undo/Redo
- Dark supports unlimited undo/redo in a single element. Undo with ctrl+z and redo with ctrl+shift+z

## [30s] Copy/Paste
- You can copy/paste selections, which is often used for refactoring.
- It may be helpful to note that copy/paste only works in Dark between handlers at this time. Copying JSON from an external source will paste into your handlers in Dark, but if you write code int hte Dark langaige in your text editor of choice, that code will not paste. We hope to improve this experience in the future

## [2m] Package Manager
- Deep dive into one package (slack? twilio?)
- Limitations, long-term intentions w/r/t packages
- Later on when we cover how to actually _work_ with Dark, we'll cover how to use and contribute to the package repo

## [30s] Deleted
- If you delete a handler, that code is saved for you in the Deleted section of your canvas.
- Everything you delete is sorted by type, and will remain in that section until you remove it.

## [30s] Keyboard mappings
- Canvas mode
	- CTRL+K, Enter => Open Omnibox / Search
	- Left, right, up, down => scroll canvas left, right, up, down
	- ctrl+a/e => scroll left/right one page
	- ctrl+b/f, pgup/pgdown => scroll canvas one page up/down
- Code-editing
	- (ctrl+\) or (ctrl+s, alt+x) => open command/refactoring palette
	- tab, shift/tab => move to next/pervious blank
	- ctrl+x/c/v => clipboard cut, copy, paste
	- shift+enter -> start pipe using selected code