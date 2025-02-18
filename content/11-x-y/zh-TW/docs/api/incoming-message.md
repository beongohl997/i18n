## Class: IncomingMessage

> 處理 HTTP/HTTPS 請求的回應。

處理序: [主處理序](../glossary.md#main-process)

`IncomingMessage` implements the [Readable Stream](https://nodejs.org/api/stream.html#stream_readable_streams) interface and is therefore an [EventEmitter][event-emitter].

### 物件事件

#### 事件: 'data'

回傳:

* `chunk` Buffer - A chunk of response body's data.

The `data` event is the usual method of transferring response data into applicative code.

#### 事件: 'end'

Indicates that response body has ended.

#### 事件: 'aborted'

Emitted when a request has been canceled during an ongoing HTTP transaction.

#### 事件: 'error'

回傳:

`error` Error - Typically holds an error string identifying failure root cause.

Emitted when an error was encountered while streaming response data events. For instance, if the server closes the underlying while the response is still streaming, an `error` event will be emitted on the response object and a `close` event will subsequently follow on the request object.

### 物件屬性

An `IncomingMessage` instance has the following readable properties:

#### `response.statusCode`

An `Integer` indicating the HTTP response status code.

#### `response.statusMessage`

A `String` representing the HTTP status message.

#### `response.headers`

A `Record<string, string | string[]>` representing the HTTP response headers. The `headers` object is formatted as follows:

* All header names are lowercased.
* Duplicates of `age`, `authorization`, `content-length`, `content-type`, `etag`, `expires`, `from`, `host`, `if-modified-since`, `if-unmodified-since`, `last-modified`, `location`, `max-forwards`, `proxy-authorization`, `referer`, `retry-after`, `server`, or `user-agent` are discarded.
* `set-cookie` is always an array. Duplicates are added to the array.
* For duplicate `cookie` headers, the values are joined together with '; '.
* For all other headers, the values are joined together with ', '.

#### `response.httpVersion`

A `String` indicating the HTTP protocol version number. Typical values are '1.0' or '1.1'. Additionally `httpVersionMajor` and `httpVersionMinor` are two Integer-valued readable properties that return respectively the HTTP major and minor version numbers.

#### `response.httpVersionMajor`

An `Integer` indicating the HTTP protocol major version number.

#### `response.httpVersionMinor`

An `Integer` indicating the HTTP protocol minor version number.

[event-emitter]: https://nodejs.org/api/events.html#events_class_eventemitter
