<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [CircuitBreaker][1]
    -   [Parameters][2]
    -   [close][3]
    -   [open][4]
    -   [shutdown][5]
    -   [isShutdown][6]
    -   [name][7]
    -   [group][8]
    -   [pendingClose][9]
    -   [closed][10]
    -   [opened][11]
    -   [halfOpen][12]
    -   [status][13]
    -   [stats][14]
    -   [enabled][15]
    -   [warmUp][16]
    -   [volumeThreshold][17]
    -   [fallback][18]
        -   [Parameters][19]
    -   [fire][20]
        -   [Parameters][21]
    -   [call][22]
        -   [Parameters][23]
    -   [clearCache][24]
    -   [healthCheck][25]
        -   [Parameters][26]
    -   [enable][27]
    -   [disable][28]
    -   [isOurError][29]
        -   [Parameters][30]
-   [CircuitBreaker#halfOpen][31]
-   [CircuitBreaker#close][32]
-   [CircuitBreaker#open][33]
-   [CircuitBreaker#shutdown][34]
-   [CircuitBreaker#fire][35]
-   [CircuitBreaker#cacheHit][36]
-   [CircuitBreaker#cacheMiss][37]
-   [CircuitBreaker#reject][38]
-   [CircuitBreaker#timeout][39]
-   [CircuitBreaker#success][40]
-   [CircuitBreaker#semaphoreLocked][41]
-   [CircuitBreaker#healthCheckFailed][42]
-   [CircuitBreaker#fallback][43]
-   [CircuitBreaker#failure][44]
-   [Status][45]
    -   [Parameters][46]
    -   [Examples][47]
    -   [stats][48]
    -   [window][49]
-   [Status#snapshot][50]

## CircuitBreaker

**Extends EventEmitter**

Constructs a [CircuitBreaker][1].

### Parameters

-   `action` **[Function][51]** The action to fire for this [CircuitBreaker][1]
-   `options` **[Object][52]** Options for the [CircuitBreaker][1]
    -   `options.timeout` **[Number][53]** The time in milliseconds that action should
        be allowed to execute before timing out. Timeout can be disabled by setting
        this to `false`. Default 10000 (10 seconds)
    -   `options.maxFailures` **[Number][53]** (Deprecated) The number of times the
        circuit can fail before opening. Default 10.
    -   `options.resetTimeout` **[Number][53]** The time in milliseconds to wait before
        setting the breaker to `halfOpen` state, and trying the action again.
        Default: 30000 (30 seconds)
    -   `options.rollingCountTimeout` **[Number][53]** Sets the duration of the
        statistical rolling window, in milliseconds. This is how long Opossum keeps
        metrics for the circuit breaker to use and for publishing. Default: 10000
    -   `options.rollingCountBuckets` **[Number][53]** Sets the number of buckets the
        rolling statistical window is divided into. So, if
        options.rollingCountTimeout is 10000, and options.rollingCountBuckets is 10,
        then the statistical window will be 1000 1 second snapshots in the
        statistical window. Default: 10
    -   `options.name` **[String][54]** the circuit name to use when reporting stats.
        Default: the name of the function this circuit controls.
    -   `options.rollingPercentilesEnabled` **[boolean][55]** This property indicates
        whether execution latencies should be tracked and calculated as percentiles.
        If they are disabled, all summary statistics (mean, percentiles) are
        returned as -1. Default: false
    -   `options.capacity` **[Number][53]** the number of concurrent requests allowed.
        If the number currently executing function calls is equal to
        options.capacity, further calls to `fire()` are rejected until at least one
        of the current requests completes. Default: `Number.MAX_SAFE_INTEGER`.
    -   `options.errorThresholdPercentage` **[Number][53]** the error percentage at
        which to open the circuit and start short-circuiting requests to fallback.
        Default: 50
    -   `options.enabled` **[boolean][55]** whether this circuit is enabled upon
        construction. Default: true
    -   `options.allowWarmUp` **[boolean][55]** determines whether to allow failures
        without opening the circuit during a brief warmup period (this is the
        `rollingCountTimeout` property). Default: false
        allow before enabling the circuit. This can help in situations where no
        matter what your `errorThresholdPercentage` is, if the first execution
        times out or fails, the circuit immediately opens.
    -   `options.volumeThreshold` **[Number][53]** the minimum number of requests within
        the rolling statistical window that must exist before the circuit breaker
        can open. This is similar to `options.allowWarmUp` in that no matter how many
        failures there are, if the number of requests within the statistical window
        does not exceed this threshold, the circuit will remain closed. Default: 0
    -   `options.errorFilter` **[Function][51]** an optional function that will be
        called when the circuit's function fails (returns a rejected Promise). If
        this function returns truthy, the circuit's failPure statistics will not be
        incremented. This is useful, for example, when you don't want HTTP 404 to
        trip the circuit, but still want to handle it as a failure case.
    -   `options.cache` **[boolean][55]** whether the return value of the first
        successful execution of the circuit's function will be cached. Once a value
        has been cached that value will be returned for every subsequent execution:
        the cache can be cleared using `clearCache`. (The metrics `cacheHit` and
        `cacheMiss` reflect cache activity.) Default: false

### close

Closes the breaker, allowing the action to execute again

Returns **void** 

### open

Opens the breaker. Each time the breaker is fired while the circuit is
opened, a failed Promise is returned, or if any fallback function
has been provided, it is invoked.

If the breaker is already open this call does nothing.

Returns **void** 

### shutdown

Shuts down this circuit breaker. All subsequent calls to the
circuit will fail, returning a rejected promise.

Returns **void** 

### isShutdown

Determines if the circuit has been shutdown.

Type: [Boolean][55]

### name

Gets the name of this circuit

Type: [String][54]

### group

Gets the name of this circuit group

Type: [String][54]

### pendingClose

Gets whether this circuit is in the `pendingClosed` state

Type: [Boolean][55]

### closed

True if the circuit is currently closed. False otherwise.

Type: [Boolean][55]

### opened

True if the circuit is currently opened. False otherwise.

Type: [Boolean][55]

### halfOpen

True if the circuit is currently half opened. False otherwise.

Type: [Boolean][55]

### status

The current [Status][13] of this [CircuitBreaker][1]

Type: [Status][56]

### stats

-   **See: Status#stats
    **

Get the current stats for the circuit.

Type: [Object][52]

### enabled

Gets whether the circuit is enabled or not

Type: [Boolean][55]

### warmUp

Gets whether the circuit is currently in warm up phase

Type: [Boolean][55]

### volumeThreshold

Gets the volume threshold for this circuit

Type: [Boolean][55]

### fallback

Provide a fallback function for this [CircuitBreaker][1]. This
function will be executed when the circuit is `fire`d and fails.
It will always be preceded by a `failure` event, and `breaker.fire` returns
a rejected Promise.

#### Parameters

-   `func` **([Function][51] \| [CircuitBreaker][57])** the fallback function to execute
    when the breaker has opened or when a timeout or error occurs.

Returns **[CircuitBreaker][57]** this

### fire

Execute the action for this circuit. If the action fails or times out, the
returned promise will be rejected. If the action succeeds, the promise will
resolve with the resolved value from action. If a fallback function was
provided, it will be invoked in the event of any failure or timeout.

Any parameters passed to this function will be proxied to the circuit
function.

#### Parameters

-   `args` **...any** 

Returns **[Promise][58]&lt;any>** promise resolves with the circuit function's return
value on success or is rejected on failure of the action. Use isOurError()
to determine if a rejection was a result of the circuit breaker or the
action.

### call

Execute the action for this circuit using `context` as `this`.
If the action fails or times out, the
returned promise will be rejected. If the action succeeds, the promise will
resolve with the resolved value from action. If a fallback function was
provided, it will be invoked in the event of any failure or timeout.

Any parameters in addition to \`context will be passed to the
circuit function.

#### Parameters

-   `context` **any** the `this` context used for function execution
-   `rest` **any** the arguments passed to the action

Returns **[Promise][58]&lt;any>** promise resolves with the circuit function's return
value on success or is rejected on failure of the action.

### clearCache

Clears the cache of this [CircuitBreaker][1]

Returns **void** 

### healthCheck

Provide a health check function to be called periodically. The function
should return a Promise. If the promise is rejected the circuit will open.
This is in addition to the existing circuit behavior as defined by
`options.errorThresholdPercentage` in the constructor. For example, if the
health check function provided here always returns a resolved promise, the
circuit can still trip and open if there are failures exceeding the
configured threshold. The health check function is executed within the
circuit breaker's execution context, so `this` within the function is the
circuit breaker itself.

#### Parameters

-   `func` **[Function][51]** a health check function which returns a promise.
-   `interval` **[Number][53]?** the amount of time between calls to the health
    check function. Default: 5000 (5 seconds)


-   Throws **[TypeError][59]** if `interval` is supplied but not a number

Returns **void** 

### enable

Enables this circuit. If the circuit is the  disabled
state, it will be re-enabled. If not, this is essentially
a noop.

Returns **void** 

### disable

Disables this circuit, causing all calls to the circuit's function
to be executed without circuit or fallback protection.

Returns **void** 

### isOurError

Returns true if the provided error was generated here. It will be false
if the error came from the action itself.

#### Parameters

-   `error` **[Error][60]** The Error to check.

Returns **[Boolean][55]** true if the error was generated here

## CircuitBreaker#halfOpen

Emitted after `options.resetTimeout` has elapsed, allowing for
a single attempt to call the service again. If that attempt is
successful, the circuit will be closed. Otherwise it remains open.

Type: [Number][53]

## CircuitBreaker#close

Emitted when the breaker is reset allowing the action to execute again

## CircuitBreaker#open

Emitted when the breaker opens because the action has
failed more than `options.maxFailures` number of times.

## CircuitBreaker#shutdown

Emitted when the circuit breaker has been shut down.

## CircuitBreaker#fire

Emitted when the circuit breaker action is executed

Type: any

## CircuitBreaker#cacheHit

Emitted when the circuit breaker is using the cache
and finds a value.

## CircuitBreaker#cacheMiss

Emitted when the circuit breaker does not find a value in
the cache, but the cache option is enabled.

## CircuitBreaker#reject

Emitted when the circuit breaker is open and failing fast

Type: [Error][60]

## CircuitBreaker#timeout

Emitted when the circuit breaker action takes longer than
`options.timeout`

Type: [Error][60]

## CircuitBreaker#success

Emitted when the circuit breaker action succeeds

Type: any

## CircuitBreaker#semaphoreLocked

Emitted when the rate limit has been reached and there
are no more locks to be obtained.

Type: [Error][60]

## CircuitBreaker#healthCheckFailed

Emitted with the user-supplied health check function
returns a rejected promise.

Type: [Error][60]

## CircuitBreaker#fallback

Emitted when the circuit breaker executes a fallback function

Type: any

## CircuitBreaker#failure

Emitted when the circuit breaker action fails

Type: [Error][60]

## Status

**Extends EventEmitter**

-   **See: CircuitBreaker#status
    **

Tracks execution status for a given [CircuitBreaker][1].
A Status instance is created for every [CircuitBreaker][1]
and does not typically need to be created by a user.

A Status instance will listen for all events on the [CircuitBreaker][1]
and track them in a rolling statistical window. The window duration is
determined by the `rollingCountTimeout` option provided to the
[CircuitBreaker][1]. The window consists of an array of Objects,
each representing the counts for a [CircuitBreaker][1]'s events.

The array's length is determined by the [CircuitBreaker][1]'s
`rollingCountBuckets` option. The duration of each slice of the window
is determined by dividing the `rollingCountTimeout` by
`rollingCountBuckets`.

### Parameters

-   `options` **[Object][52]** for the status window
    -   `options.rollingCountBuckets` **[Number][53]** number of buckets in the window
    -   `options.rollingCountTimeout` **[Number][53]** the duration of the window
    -   `options.rollingPercentilesEnabled` **[Boolean][55]** whether to calculate
        percentiles

### Examples

```javascript
// Creates a 1 second window consisting of ten time slices,
// each 100ms long.
const circuit = circuitBreaker(fs.readFile,
 { rollingCountBuckets: 10, rollingCountTimeout: 1000});

// get the cumulative statistics for the last second
circuit.status.stats;

// get the array of 10, 1 second time slices for the last second
circuit.status.window;
```

### stats

Get the cumulative stats for the current window

Type: [Object][52]

### window

Gets the stats window as an array of time-sliced objects.

Type: [Array][61]

## Status#snapshot

Emitted at each time-slice. Listeners for this
event will receive a cumulative snapshot of the current status window.

Type: [Object][52]

[1]: #circuitbreaker

[2]: #parameters

[3]: #close

[4]: #open

[5]: #shutdown

[6]: #isshutdown

[7]: #name

[8]: #group

[9]: #pendingclose

[10]: #closed

[11]: #opened

[12]: #halfopen

[13]: #status

[14]: #stats

[15]: #enabled

[16]: #warmup

[17]: #volumethreshold

[18]: #fallback

[19]: #parameters-1

[20]: #fire

[21]: #parameters-2

[22]: #call

[23]: #parameters-3

[24]: #clearcache

[25]: #healthcheck

[26]: #parameters-4

[27]: #enable

[28]: #disable

[29]: #isourerror

[30]: #parameters-5

[31]: #circuitbreakerhalfopen

[32]: #circuitbreakerclose

[33]: #circuitbreakeropen

[34]: #circuitbreakershutdown

[35]: #circuitbreakerfire

[36]: #circuitbreakercachehit

[37]: #circuitbreakercachemiss

[38]: #circuitbreakerreject

[39]: #circuitbreakertimeout

[40]: #circuitbreakersuccess

[41]: #circuitbreakersemaphorelocked

[42]: #circuitbreakerhealthcheckfailed

[43]: #circuitbreakerfallback

[44]: #circuitbreakerfailure

[45]: #status-1

[46]: #parameters-6

[47]: #examples

[48]: #stats-1

[49]: #window

[50]: #statussnapshot

[51]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/function

[52]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object

[53]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number

[54]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String

[55]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean

[56]: #status

[57]: #circuitbreaker

[58]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise

[59]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/TypeError

[60]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error

[61]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array