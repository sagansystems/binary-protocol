# binary-protocol

[![Build Status](https://travis-ci.org/oleksiyk/binary-protocol.png)](https://travis-ci.org/oleksiyk/binary-protocol)

binary-protocol is a library for parsing and creating arbitrary byte buffers. It was primary created for Apache Kafka protocol.
You can build your own type definitions based on the following methods:

* Int8
* UInt8
* Int16LE
* UInt16LE
* Int16BE
* UInt16BE
* Int32LE
* Int32BE
* UInt32LE
* UInt32BE
* FloatLE
* FloatBE
* DoubleLE
* DoubleB

Int64 support (using long.js):
* Int64BE
* Int64LE

Read or write raw bytes:
* raw

Loops (arrays):
* loop


### Install
```
$ npm install bin-protocol
```

### Reader examples

```javascript
var protocol = require('bin-protocol');

var reader = new protocol.Reader(new Buffer([0, 1, 2, 3]));

reader
    .Int8('num1')
    .Int8('num2')
    .Int8('num3')
    .Int8('num4');

console.log(reader.result); // => { num1: 0, num2: 1, num3: 2, num4: 3 }
```

```javascript
var protocol = require('bin-protocol');

var reader = new protocol.Reader(new Buffer([0, 1, 2, 3]));

reader
    .Int8('num1')
    .Int8('num2')
    .Int8('num3')
    .Int8('num4');

console.log(reader.result); // => { num1: 0, num2: 1, num3: 2, num4: 3 }
```

```javascript
var protocol = require('bin-protocol');

protocol.define('char', {
    read: function () {
        this.Int8('char');
        return String.fromCharCode(this.context.char);
    }
});

protocol.define('array', {
    read: function () {
        this
            .Int8('length')
            .loop('items', this.char, this.context.length);
        return this.context.items;
    }
});

var reader = new protocol.Reader(new Buffer([5, 97, 98, 99, 100, 101]));

reader
    .array('chars');

console.log(reader.result); // => { chars: [ 'a', 'b', 'c', 'd', 'e' ] }
```

### Writer examples
```javascript
var writer = new protocol.Writer();

writer
    .Int8(1)
    .Int8(2)
    .Int8(3);

var buffer = writer.result();

console.log(buffer); // => <Buffer 01 02 03>
```

```javascript
protocol.define('array', {
    write: function (values) {
        this
            .Int8(values.length)
            .loop(values, this.Int8);
    }
});

var writer = new protocol.Writer();

writer.array([2, 3, 4]);

console.log(writer.result()); // => <Buffer 03 02 03 04>
```

See tests for more.

# License (MIT)

Copyright (c) 2015
 Oleksiy Krivoshey.

Permission is hereby granted, free of charge, to any person
obtaining a copy of this software and associated documentation
files (the "Software"), to deal in the Software without
restriction, including without limitation the rights to use,
copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following
conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES
OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY,
WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
