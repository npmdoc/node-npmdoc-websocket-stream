# api documentation for  [websocket-stream (v4.0.0)](https://github.com/maxogden/websocket-stream#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-websocket-stream.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-websocket-stream) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-websocket-stream.svg)](https://travis-ci.org/npmdoc/node-npmdoc-websocket-stream)
#### Use websockets with the node streams API. Works in browser and node

[![NPM](https://nodei.co/npm/websocket-stream.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/websocket-stream)

[![apidoc](https://npmdoc.github.io/node-npmdoc-websocket-stream/build/screenCapture.buildCi.browser.%252Ftmp%252Fbuild%252Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-websocket-stream/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-websocket-stream/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-websocket-stream/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": "",
    "browser": {
        "./echo-server.js": "./fake-server.js",
        "./index.js": "./stream.js",
        "ws": "./ws-fallback.js"
    },
    "bugs": {
        "url": "https://github.com/maxogden/websocket-stream/issues"
    },
    "dependencies": {
        "duplexify": "^3.2.0",
        "inherits": "^2.0.1",
        "readable-stream": "^2.2.0",
        "safe-buffer": "^5.0.1",
        "ws": "^2.2.3",
        "xtend": "^4.0.0"
    },
    "description": "Use websockets with the node streams API. Works in browser and node",
    "devDependencies": {
        "beefy": "^2.1.1",
        "browserify": "^14.0.0",
        "concat-stream": "^1.4.7",
        "tape": "^4.6.0"
    },
    "directories": {},
    "dist": {
        "shasum": "358386cab1b0070e17b32b437f74f1bc19e13890",
        "tarball": "https://registry.npmjs.org/websocket-stream/-/websocket-stream-4.0.0.tgz"
    },
    "gitHead": "a2c72276413461d68f2b5b2d66a6f72bf16ae0c6",
    "homepage": "https://github.com/maxogden/websocket-stream#readme",
    "keywords": [
        "websocket",
        "websockets",
        "stream",
        "streams",
        "realtime"
    ],
    "license": "BSD-2-Clause",
    "main": "index.js",
    "maintainers": [
        {
            "name": "deanlandolt"
        },
        {
            "name": "jnordberg"
        },
        {
            "name": "mafintosh"
        },
        {
            "name": "matteo.collina"
        },
        {
            "name": "maxogden"
        }
    ],
    "name": "websocket-stream",
    "optionalDependencies": {},
    "repository": {
        "type": "git",
        "url": "git+ssh://git@github.com/maxogden/websocket-stream.git"
    },
    "scripts": {
        "start": "beefy test-client.js",
        "test": "node test.js"
    },
    "version": "4.0.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module websocket-stream](#apidoc.module.websocket-stream)
1.  [function <span class="apidocSignatureSpan"></span>websocket-stream (target, protocols, options)](#apidoc.element.websocket-stream.websocket-stream)
1.  [function <span class="apidocSignatureSpan">websocket-stream.</span>Server (opts, cb)](#apidoc.element.websocket-stream.Server)
1.  [function <span class="apidocSignatureSpan">websocket-stream.</span>createServer (opts, cb)](#apidoc.element.websocket-stream.createServer)
1.  [function <span class="apidocSignatureSpan">websocket-stream.</span>toString ()](#apidoc.element.websocket-stream.toString)



# <a name="apidoc.module.websocket-stream"></a>[module websocket-stream](#apidoc.module.websocket-stream)

#### <a name="apidoc.element.websocket-stream.websocket-stream"></a>[function <span class="apidocSignatureSpan"></span>websocket-stream (target, protocols, options)](#apidoc.element.websocket-stream.websocket-stream)
- description and source-code
```javascript
function WebSocketStream(target, protocols, options) {
  var stream, socket

  var isBrowser = process.title === 'browser'
  var isNative = !!global.WebSocket
  var socketWrite = isBrowser ? socketWriteBrowser : socketWriteNode

  if (protocols && !Array.isArray(protocols) && 'object' === typeof protocols) {
    // accept the "options" Object as the 2nd argument
    options = protocols
    protocols = null

    if (typeof options.protocol === 'string' || Array.isArray(options.protocol)) {
      protocols = options.protocol;
    }
  }

  if (!options) options = {}

  if (options.objectMode === undefined) {
    options.objectMode = !(options.binary === true || options.binary === undefined)
  }

  var proxy = buildProxy(options, socketWrite, socketEnd)

  if (!options.objectMode) {
    proxy._writev = writev
  }

  // browser only: sets the maximum socket buffer size before throttling
  var bufferSize = options.browserBufferSize || 1024 * 512

  // browser only: how long to wait when throttling
  var bufferTimeout = options.browserBufferTimeout || 1000

  // use existing WebSocket object that was passed in
  if (typeof target === 'object') {
    socket = target
  // otherwise make a new one
  } else {
    // special constructor treatment for native websockets in browsers, see
    // https://github.com/maxogden/websocket-stream/issues/82
    if (isNative && isBrowser) {
      socket = new WS(target, protocols)
    } else {
      socket = new WS(target, protocols, options)
    }

    socket.binaryType = 'arraybuffer'
  }

  // was already open when passed in
  if (socket.readyState === WS.OPEN) {
    stream = proxy
  } else {
    stream = duplexify.obj()
    socket.onopen = onopen
  }

  stream.socket = socket

  socket.onclose = onclose
  socket.onerror = onerror
  socket.onmessage = onmessage

  proxy.on('close', destroy)

  var coerceToBuffer = !options.objectMode

  function socketWriteNode(chunk, enc, next) {
    // avoid errors, this never happens unless
    // destroy() is called
    if (socket.readyState !== WS.OPEN) {
      next()
      return
    }

    if (coerceToBuffer && typeof chunk === 'string') {
      chunk = new Buffer(chunk, 'utf8')
    }
    socket.send(chunk, next)
  }

  function socketWriteBrowser(chunk, enc, next) {
    if (socket.bufferedAmount > bufferSize) {
      setTimeout(socketWriteBrowser, bufferTimeout, chunk, enc, next)
      return
    }

    if (coerceToBuffer && typeof chunk === 'string') {
      chunk = new Buffer(chunk, 'utf8')
    }

    try {
      socket.send(chunk)
    } catch(err) {
      return next(err)
    }

    next()
  }

  function socketEnd(done) {
    socket.close()
    done()
  }

  function onopen() {
    stream.setReadable(proxy)
    stream.setWritable(proxy)
    stream.emit('connect')
  }

  function onclose() {
    stream.end()
    stream.destroy()
  }

  function onerror(err) {
    stream.destroy(err)
  }

  function onmessage(event) {
    var data = event.data
    if (data instanceof ArrayBuffer) data = Buffer.from(new Uint8Array(data))
    else data = Buffer.from(data, 'utf8')
    proxy.push(data)
  }

  function destroy() {
    socket.close()
  }

  // this is to be enabled only if objectMode is false
  function writev (chunks, cb) {
    var buffers = new Array(chunks.length)
    for (var i = 0; i < chunks.length; i++) {
      if (typeof chunks[i].chunk === 'string') {
        buffers[i] = Buffer.from(chunks[i], 'utf8')
      } else {
        buffers[i] = chunks[i].chunk
      }
    }

    this._write(Buffer.concat(buffers), 'binary', cb)
  }

  return stream
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.websocket-stream.Server"></a>[function <span class="apidocSignatureSpan">websocket-stream.</span>Server (opts, cb)](#apidoc.element.websocket-stream.Server)
- description and source-code
```javascript
class Server extends WebSocketServer{
  constructor(opts, cb) {
    super(opts)

    var proxied = false
    this.on('newListener', function(event) {
      if (!proxied && event === 'stream') {
        proxied = true
        this.on('connection', function(conn) {
          this.emit('stream', stream(conn, opts))
        })
      }
    })

    if (cb) {
      this.on('stream', cb)
    }
  }
}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.websocket-stream.createServer"></a>[function <span class="apidocSignatureSpan">websocket-stream.</span>createServer (opts, cb)](#apidoc.element.websocket-stream.createServer)
- description and source-code
```javascript
createServer = function (opts, cb) {
  return new Server(opts, cb)
}
```
- example usage
```shell
...

### On the server

Using the ['ws'](http://npmjs.org/ws) module you can make a websocket server and use this module to get websocket streams on the
 server:

'''javascript
var websocket = require('websocket-stream')
var wss = websocket.createServer({server: someHTTPServer}, handle)

function handle(stream) {
  fs.createReadStream('bigdata.json').pipe(stream)
}
'''

We recommend disabling the [per message deflate
...
```

#### <a name="apidoc.element.websocket-stream.toString"></a>[function <span class="apidocSignatureSpan">websocket-stream.</span>toString ()](#apidoc.element.websocket-stream.toString)
- description and source-code
```javascript
toString = function () {
    return toString;
}
```
- example usage
```shell
...
var test = require('tape')
var Buffer = require('safe-buffer').Buffer

test('echo works', function(t) {
  var stream = ws('ws://localhost:8343')
  stream.on('data', function(o) {
    t.ok(Buffer.isBuffer(o), 'is buffer')
    t.equal(o.toString(), 'hello', 'got hello back')
    stream.destroy()
    t.end()
  })
  stream.write(Buffer.from('hello'))
})

test('echo works two times', function(t) {
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
