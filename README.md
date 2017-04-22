# npmdoc-websocket-stream

#### api documentation for  [websocket-stream (v4.0.0)](https://github.com/maxogden/websocket-stream#readme)  [![npm package](https://img.shields.io/npm/v/npmdoc-websocket-stream.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-websocket-stream) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-websocket-stream.svg)](https://travis-ci.org/npmdoc/node-npmdoc-websocket-stream)

#### Use websockets with the node streams API. Works in browser and node

[![NPM](https://nodei.co/npm/websocket-stream.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/websocket-stream)

- [https://npmdoc.github.io/node-npmdoc-websocket-stream/build/apidoc.html](https://npmdoc.github.io/node-npmdoc-websocket-stream/build/apidoc.html)

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
    "version": "4.0.0",
    "bin": {}
}
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
