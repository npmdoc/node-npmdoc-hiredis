# api documentation for  [hiredis (v0.5.0)](http://github.com/redis/hiredis-node)  [![npm package](https://img.shields.io/npm/v/npmdoc-hiredis.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-hiredis) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-hiredis.svg)](https://travis-ci.org/npmdoc/node-npmdoc-hiredis)
#### Wrapper for reply processing code in hiredis

[![NPM](https://nodei.co/npm/hiredis.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.com/package/hiredis)

[![apidoc](https://npmdoc.github.io/node-npmdoc-hiredis/build/screenCapture.buildCi.browser.apidoc.html.png)](https://npmdoc.github.io/node-npmdoc-hiredis/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-hiredis/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-hiredis/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Jan-Erik Rediger"
    },
    "bugs": {
        "url": "https://github.com/redis/hiredis-node/issues"
    },
    "contributors": [
        {
            "name": "Pieter Noordhuis"
        }
    ],
    "dependencies": {
        "bindings": "^1.2.1",
        "nan": "^2.3.4"
    },
    "description": "Wrapper for reply processing code in hiredis",
    "devDependencies": {},
    "directories": {},
    "dist": {
        "shasum": "db03a98becd7003d13c260043aceecfacdf59b87",
        "tarball": "https://registry.npmjs.org/hiredis/-/hiredis-0.5.0.tgz"
    },
    "engines": {
        "node": ">= 0.10.0"
    },
    "gitHead": "b8f3af63704176a323afed2a0e9bb4811d201bc1",
    "gypfile": true,
    "homepage": "http://github.com/redis/hiredis-node",
    "license": "BSD-3-Clause",
    "main": "hiredis",
    "maintainers": [
        {
            "name": "pietern"
        },
        {
            "name": "badboy_"
        }
    ],
    "name": "hiredis",
    "optionalDependencies": {},
    "repository": {
        "type": "git",
        "url": "git+https://github.com/redis/hiredis-node.git"
    },
    "scripts": {
        "install": "node-gyp rebuild",
        "test": "node test/reader.js && node test/writer.js"
    },
    "version": "0.5.0"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module hiredis](#apidoc.module.hiredis)
1.  [function <span class="apidocSignatureSpan">hiredis.</span>Reader ()](#apidoc.element.hiredis.Reader)
1.  [function <span class="apidocSignatureSpan">hiredis.</span>createConnection (port, host)](#apidoc.element.hiredis.createConnection)
1.  [function <span class="apidocSignatureSpan">hiredis.</span>writeCommand ()](#apidoc.element.hiredis.writeCommand)
1.  object <span class="apidocSignatureSpan">hiredis.</span>Reader.prototype

#### [module hiredis.Reader](#apidoc.module.hiredis.Reader)
1.  [function <span class="apidocSignatureSpan">hiredis.</span>Reader ()](#apidoc.element.hiredis.Reader.Reader)

#### [module hiredis.Reader.prototype](#apidoc.module.hiredis.Reader.prototype)
1.  [function <span class="apidocSignatureSpan">hiredis.Reader.prototype.</span>feed ()](#apidoc.element.hiredis.Reader.prototype.feed)
1.  [function <span class="apidocSignatureSpan">hiredis.Reader.prototype.</span>get ()](#apidoc.element.hiredis.Reader.prototype.get)



# <a name="apidoc.module.hiredis"></a>[module hiredis](#apidoc.module.hiredis)

#### <a name="apidoc.element.hiredis.Reader"></a>[function <span class="apidocSignatureSpan">hiredis.</span>Reader ()](#apidoc.element.hiredis.Reader)
- description and source-code
```javascript
Reader = function () { [native code] }
```
- example usage
```shell
...
The latter has an optional dependency on hiredis-node, so maybe you're
already using it without knowing.

Alternatively, you can use it directly:

'''javascript
var hiredis = require("hiredis"),
    reader = new hiredis.Reader();

// Data comes in
reader.feed("$5\r\nhello\r\n");

// Reply comes out
reader.get() // => "hello"
'''
...
```

#### <a name="apidoc.element.hiredis.createConnection"></a>[function <span class="apidocSignatureSpan">hiredis.</span>createConnection (port, host)](#apidoc.element.hiredis.createConnection)
- description and source-code
```javascript
createConnection = function (port, host) {
    var s = net.createConnection(port || 6379, host);
    var r = new hiredis.Reader();
    var _write = s.write;

    s.write = function() {
        var data = exports.writeCommand.apply(this, arguments);
        return _write.call(s, data);
    }

    s.on("data", function(data) {
        var reply;
        r.feed(data);
        try {
            while((reply = r.get()) !== undefined)
                s.emit("reply", reply);
        } catch(err) {
            r = null;
            s.emit("error", err);
            s.destroy();
        }
    });

    return s;
}
```
- example usage
```shell
...
    size += 5 + bufLen.length + arg.length;
}

return Buffer.concat(parts, size);
}

exports.createConnection = function(port, host) {
var s = net.createConnection(port || 6379, host);
var r = new hiredis.Reader();
var _write = s.write;

s.write = function() {
    var data = exports.writeCommand.apply(this, arguments);
    return _write.call(s, data);
}
...
```

#### <a name="apidoc.element.hiredis.writeCommand"></a>[function <span class="apidocSignatureSpan">hiredis.</span>writeCommand ()](#apidoc.element.hiredis.writeCommand)
- description and source-code
```javascript
writeCommand = function () {
    var args = arguments,
        bufLen = new Buffer(String(args.length), "ascii"),
        parts = [bufStar, bufLen, bufCrlf],
        size = 3 + bufLen.length;

    for (var i = 0; i < args.length; i++) {
        var arg = args[i];
        if (!Buffer.isBuffer(arg))
            arg = new Buffer(String(arg));

        bufLen = new Buffer(String(arg.length), "ascii");
        parts = parts.concat([
            bufDollar, bufLen, bufCrlf,
            arg, bufCrlf
        ]);
        size += 5 + bufLen.length + arg.length;
    }

    return Buffer.concat(parts, size);
}
```
- example usage
```shell
n/a
```



# <a name="apidoc.module.hiredis.Reader"></a>[module hiredis.Reader](#apidoc.module.hiredis.Reader)

#### <a name="apidoc.element.hiredis.Reader.Reader"></a>[function <span class="apidocSignatureSpan">hiredis.</span>Reader ()](#apidoc.element.hiredis.Reader.Reader)
- description and source-code
```javascript
Reader = function () { [native code] }
```
- example usage
```shell
...
The latter has an optional dependency on hiredis-node, so maybe you're
already using it without knowing.

Alternatively, you can use it directly:

'''javascript
var hiredis = require("hiredis"),
    reader = new hiredis.Reader();

// Data comes in
reader.feed("$5\r\nhello\r\n");

// Reply comes out
reader.get() // => "hello"
'''
...
```



# <a name="apidoc.module.hiredis.Reader.prototype"></a>[module hiredis.Reader.prototype](#apidoc.module.hiredis.Reader.prototype)

#### <a name="apidoc.element.hiredis.Reader.prototype.feed"></a>[function <span class="apidocSignatureSpan">hiredis.Reader.prototype.</span>feed ()](#apidoc.element.hiredis.Reader.prototype.feed)
- description and source-code
```javascript
function feed() { [native code] }
```
- example usage
```shell
...
Alternatively, you can use it directly:

'''javascript
var hiredis = require("hiredis"),
    reader = new hiredis.Reader();

// Data comes in
reader.feed("$5\r\nhello\r\n");

// Reply comes out
reader.get() // => "hello"
'''

Instead of returning strings for bulk payloads, it can also return
buffers:
...
```

#### <a name="apidoc.element.hiredis.Reader.prototype.get"></a>[function <span class="apidocSignatureSpan">hiredis.Reader.prototype.</span>get ()](#apidoc.element.hiredis.Reader.prototype.get)
- description and source-code
```javascript
function get() { [native code] }
```
- example usage
```shell
...
var hiredis = require("hiredis"),
    reader = new hiredis.Reader();

// Data comes in
reader.feed("$5\r\nhello\r\n");

// Reply comes out
reader.get() // => "hello"
'''

Instead of returning strings for bulk payloads, it can also return
buffers:

'''javascript
var hiredis = require("hiredis"),
...
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)
