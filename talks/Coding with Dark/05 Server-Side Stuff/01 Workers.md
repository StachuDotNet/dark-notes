# [5] Workers - CRON jobs and event stuff
## [2] Intro
- "how do I access the data from `emit` in the worker?"
	- the data from the `emit` is available in a variable called `event` from within the worker. Its type will be whatever was passed to emit.
- Dark supports doing work asynchronously outside the context of an HTTP Handler using a Worker. Each worker has a queue of messages, which are processed loosely in-order executing the code within the Worker once for each message. Messages are created by calling `emit` from any other code, and can contain arbitrary event data
- Workers can be created from the omnibar or sidebar. similar to http handlers, calling `emit` with a nonexstant Worker name will populate that worker int he 404 sidebar section, allowing it to be created. Creating a Worker from a 404 may result in a delay when executing the first message. When a new worker is created, it immediately processed the first message in the queue, but returns Incomplete because no code has been written yet. This casues the message to get automatically retried in 5 minutes, but until then it may look like o messages are being processed
- workers will automatically process each message. the `event` data passed to `emit` is available in the Worker as a special variable `event`. This can be of any type but is often convenient to use a dict holding many other values.
- Counts
	- if there are multiple items in the queue, a live count of queue items will appear at the left
	- the last 10 processed events will show as traces that you can use for debugging purposes

## [30s] Traces
the trace information on Cron will show the most recent 10 times the Cron has run. a Cron never has input data, so the trace will always say No input parameters.

## [30s] Workers + 404s
workers receive events via the emit expression and will appear in the 404 section if you emit to a non-existent worker.

## [1] Queuing, Cadence Control, and Manual Execution
- queuing and a basic example
- cadence control
- execuution
	- manual
	- pause/debug
- `emit { messageId: Uuid::generate; createdAt: Date::now } "my-worker"`
- Crons can have intervals of:
	- daily, weekly, fortnightly, hourly, every 12hrs, every minute
- crons run automatically once per interval
- To run a Cron on-demand, use the replay button the upper right. Running on-demand does not affect the next scheduled runtime
- Can I set the exact time my cron will run?
	- No, not currently. We plan to eventually support this.
- you can pause the queue by hitting the "pause" button in case of operational issues or if you'd like to stop and debug
- Can I pause a Cron to keep it from running?
	- No, not currently. we plan to eventually support his
- There's currently no guarantee when within an interval that the cron job will run.
- workers across a canvas execute in parallel, and multiple messages for a worker may be processed in parallel
- a message should be processed within a minute of being emitted. typically, processing begins within a few seconds
- Can I call `emit` from a Worker?
	- Yes. This can be useful to do fan-out of work or batch processing of data.
	- In fact, you can `emit` to the same Worker that's processing the message

## [30s] Cautions
- Workers do not guarantee exactly-once delivery.
	- adding a unique UUID to every message can be useful in keeping track of which messages have been seen by your worker already
- Crons will not alert you of failures unless you write logic to do so
- workers will not alert you of failures unless you write logic to do so
- A message that causes a worker to throw a runtime error will be silently ignored and the worker will process the next message.
	- we plan to eventually add more error handling capabilities such as automatic retries and dead-letter-queues