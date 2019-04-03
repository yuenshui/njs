
# njs 参考文档  
原文地址：[http://nginx.org/en/docs/njs/reference.html](http://nginx.org/en/docs/njs/reference.html)  
[nginx objects]()  
&emsp;&emsp;[HTTP Request](#http-request)  
&emsp;&emsp;[Stream Session](#stream-session)  
[Core](#core)  
&emsp;&emsp;[Object](#object)  
&emsp;&emsp;[String](#string)  
&emsp;&emsp;[JSON](#json)  
&emsp;&emsp;[Crypto](#crypto)  
&emsp;&emsp;[Hash](#hash)  
&emsp;&emsp;[HMAC](#hmac)  
&emsp;&emsp;[Timers](#timers)  
&emsp;&emsp;[File System](#file-system)  
&emsp;&emsp;[File System Flags](#file-system-flags)  

njs提供了扩展nginx功能的对象，方法和属性。

## nginx objects
### HTTP Request

HTTP请求对象仅在`ngx_http_js_module`模块中可用。对象的所有字符串属性都是[字节字符串](#String)。

`r.args{}`  
&emsp;&emsp;请求参数对象，只读  
`r.error(string)`  
&emsp;&emsp;在`error`的级别上将字符串写入`error_log`日志  
`r.finish()`
&emsp;&emsp;完成向客户发送回复  
`r.headersIn{}`  
&emsp;&emsp;传入头对象，只读  
&emsp;&emsp;比如，可以通过`headersIn.foo`或者`headersIn['Foo']`访问HTTP头的`Foo`

`r.headersOut{}`  
&emsp;&emsp;传出头对象，可写。  
&emsp;&emsp;比如, 可以通过`headersOut.foo`或者`headersOut['Foo']`访问HTTP头的`Foo`

`r.httpVersion`  
&emsp;&emsp;`HTTP`版本，只读  
`r.log(string)`  
&emsp;&emsp;将字符串写入`info`级别的`error_log`日志  
`r.internalRedirect(uri)`  
&emsp;&emsp;执行内部重定向到指定的`uri`。如果`uri`以`@`前缀开头，则将其视为命名位置  
`r.method`  
&emsp;&emsp; `HTTP`方法, 只读  
`r.parent`  
&emsp;&emsp; 引用父请求对象  
`r.remoteAddress`  
&emsp;&emsp; 客户端IP地址，只读  
`r.requestBody`  
&emsp;&emsp; 如果客户端请求正文尚未写入临时文件，则返回该请求正文。要确保客户端请求正文在内存中，其大小应受`client_max_body_size`限制，并且应使用`client_body_buffer_size`设置足够的缓冲区大小。该属性仅在`js_content`指令中可用。  
`r.responseBody`  
&emsp;&emsp;拥有[subrequest](#subrequest)响应主体，只读。`r.responseBody`的大小受`subrequest_output_buffer_size`指令的限制。  
`r.return(status[, string])`  
&emsp;&emsp;将具有指定`status`的整个响应发送给客户端  
&emsp;&emsp;可以指定重定向URL（对于代码`301`,`302`,`303`,`307`和`308`）或响应正文文本（对于其他代码）作为第二个参数  
`r.send(string)`  
&emsp;&emsp;将响应主体的一部分发送给客户端  
`r.sendHeader()`  
&emsp;&emsp;将`HTTP`标头发送到客户端  
`r.status`  
&emsp;&emsp;状态，可写
`r.variables{}`  
&emsp;&emsp;nginx变量对象，可写（自`0.2.8`起）  
`r.warn(string)`  
&emsp;&emsp;将`string`写入`warn`级别的日志  
`r.uri`  
&emsp;&emsp;请求中的当前URI，[规范化](http://nginx.org/en/docs/http/ngx_http_core_module.html#location)的，只读  
`r.subrequest(uri[, options[, callback]])`  
&emsp;&emsp;使用给定的`uri`和`options`创建子请求，回调可选。  
&emsp;&emsp;[子请求](http://nginx.org/en/docs/dev/development_guide.html#http_subrequests)与客户端请求共享上行`HTTP`头。可以使用`proxy_set_header`指令发送不同的`HTTP`头。要将全新的`HTTP`头整体发送到代理服务器，可以使用`proxy_pass_request_headers`指令。  
&emsp;&emsp;如果`options`是一个字符串，那么它包含子请求参数字符串。否则，对象key如下：  
&emsp;&emsp;`args`  
&emsp;&emsp;&emsp;&emsp;参数字符串，默认为空字符串  
&emsp;&emsp;`body`  
&emsp;&emsp;&emsp;&emsp;请求的`body`部分，默认情况下使用父请求对象的`body`  
&emsp;&emsp;`method`  
&emsp;&emsp;&emsp;&emsp;`HTTP`方法，默认情况下使用`GET`方法  
&emsp;&emsp;完成回调接收子请求响应对象，其方法和属性与父请求对象相同。

### Stream Session
流会话对象仅在`ngx_stream_js_module`模块中可用。对象的所有字符串属性都是[字节字符串](#String)。

`s.allow()`  
&emsp;&emsp;成功完成阶段处理程序 (0.2.4)  
`s.decline()`  
&emsp;&emsp;完成阶段处理程序并将控制权传递给下一个处理程序 (0.2.4)  
`s.deny()`  
&emsp;&emsp;使用访问错误代码完成阶段处理程序 (0.2.4)  
`s.done([code])`  
&emsp;&emsp;成功完成当前阶段处理程序或使用指定的数字代码完成它 (0.2.4).  
`s.error(string)`  
&emsp;&emsp;以`error`级别将发送的`string`写入错误日志  
`s.log(string)`  
&emsp;&emsp;以`info`级别将发送的`string`写入错误日志  
`s.off(eventName)`  
&emsp;&emsp;取消注册`s.on`方法设置的回调 (0.2.4)  
`s.on(event, callback)`  
&emsp;&emsp;为指定的`event`注册`callback` (0.2.4).  
&emsp;&emsp;`event`可以是下面中的一个:  

&emsp;&emsp;`upload`  
&emsp;&emsp;&emsp;&emsp;来自客户的新数据  
&emsp;&emsp;`download`  
&emsp;&emsp;&emsp;&emsp;给客户的新数据
&emsp;&emsp;&emsp;&emsp;完成回调具有以下原型`callback(data, flags)`，`data`是字符串时，`flags`对象将有一下属性：

&emsp;&emsp;`last`  
&emsp;&emsp;布尔值，如果数据是最后一个缓冲区，则为true。  
`s.remoteAddress`  
&emsp;&emsp;客户端IP地址，只读  
`s.send(data[, options])`  
&emsp;&emsp;将`data`发送到客户端 （0.2.4）。`options`是一个对象，用于覆盖从传入数据块缓冲区派生的`nginx`缓冲区标志。可以使用以下标志覆盖标志：  
&emsp;&emsp;`last`  
&emsp;&emsp;&emsp;&emsp;布尔值，如果缓冲区是最后一个缓冲区，则为true  
&emsp;&emsp;`flush`  
&emsp;&emsp;&emsp;&emsp;布尔值, 如果缓冲区应该具有`flush`标志，则为true  
&emsp;&emsp;每次回调调用，可以多次调用该方法。  
`s.variables{}`  
&emsp;&emsp;`nginx`变量对象，可写 (since 0.2.8)  
`s.warn(string)`  
&emsp;&emsp;以`warn`级别将发送的`string`写入错误日志

## Core
### Object
`Object`构造函数对应于标准JS对象。

`Object.entries(object)`  
&emsp;&emsp;返回`object`的所有可枚举属性`[key，value]`对的数组 (0.2.7).  
`Object.values(object)`  
&emsp;&emsp;返回`object`的所有可枚举属性值的数组 (0.2.7).  

### String
`njs`中有两种类型的字符串：`Unicode`字符串（默认）和字节字符串。

`Unicode`字符串对应于包含`Unicode`字符的`ECMAScript`字符串。

字节字符串包含一系列字节，用于将`Unicode`字符串序列化为外部数据和从外部源反序列化。例如，`toUTF8()`方法使用`UTF8`编码将`Unicode`字符串序列化为字节字符串：
```Javascript
>> '£'.toUTF8().toString('hex')
'c2a3'  /* C2 A3 is the UTF8 representation of 00A3 ('£') code point */
```
`toBytes()`方法将`code point`最多为255的`Unicode`字符串序列化为字节字符串，否则返回`null`：
```Javascript
>> '£'.toBytes().toString('hex')
'a3'  /* a3 is a byte equal to 00A3 ('£') code point  */
```

只有`字节字符串`可以转换为不同的编码。例如，字符串不能直接编码为十六进制:
```Javascrpt
>> 'αβγδ'.toString('base64')
TypeError: argument must be a byte string
    at String.prototype.toString (native)
    at main (native)
```
要将Unicode字符串转换为十六进制，首先应将其转换为字节字符串，然后转换为十六进制:
```Javascript
>> 'αβγδ'.toUTF8().toString('base64')
'zrHOss6zzrQ='
```
`String.bytesFrom(array | string, encoding)`  
&emsp;&emsp;(njs specific) 从包含八位字节的数组或编码字符串创建字节字符串 (0.2.3). 编码可以是`hex`，`base64`和`base64url`
```Javascript
>> String.bytesFrom([0x62, 0x75, 0x66, 0x66, 0x65, 0x72])
'buffer'

>> String.bytesFrom('YnVmZmVy', 'base64')
'buffer'
```
`String.fromCharCode(CharCode1[, ...[, CharCodeN]])`  
&emsp;&emsp;从一个或多个`Unicode` `code points`点返回一个字符串。
```Javascript
>> String.fromCharCode(97, 98, 99, 100)
'abcd'
```
`String.fromCodePoint(codePoint1[, ...[, codePoint2]])`  
&emsp;&emsp;Returns a string from one or more Unicode code points.  
```Javascript
>> String.fromCodePoint(97, 98, 99, 100)
'abcd'
```
`String.prototype.charAt(index)`  
&emsp;&emsp;返回表示指定索引处的一个`Unicode`代码单元的字符串;如果`index`超出范围，则为空字符串。索引可以是大于等于0小于字符串的长度的整数。缺省为0  
`String.prototype.CodePointAt(position)`  
&emsp;&emsp;返回一个数字，表示给定`position`的字符的`code point value`;如果位置没有元素，则为`undefined`。
```Javascript
>> 'ABCD'.codePointAt(3);
68
```
`String.prototype.concat(string1[, ..., stringN])`  
&emsp;&emsp;字符串链接
```Javascript
>> "a".concat("b", "c")
'abc'
```
`String.prototype.endsWith(searchString[, length])`  
&emsp;&emsp;如果`String`以指定`searchString`的字符结尾，则返回`true`，否则返回`false`。可选的`length`参数是字符串的长度。如果省略，则默认值为字符串的长度。
```Javascript
>> 'abc'.endsWith('abc')
true
>> 'abca'.endsWith('abc')
false
```
`String.prototype.fromBytes(start[, end])`  
&emsp;&emsp;(njs specific) 从字节字符串返回一个新的`Unicode`字符串，其中每个字节都替换为相应的`Unicode` `code point`。  
`String.prototype.fromUTF8(start[, end])`  
&emsp;&emsp;(njs specific) 将包含有效UTF8字符串的字节字符串转换为`Unicode`字符串，否则返回`null`。  
`String.prototype.includes(searchString[, position]))`  
&emsp;&emsp;如果在另一个字符串中找到字符串，则返回`true`，否则返回`false`。可选的`position`参数是字符串中开始搜索`searchString`的位置。默认值为0  
```Javascript
>> 'abc'.includes('bc')
true
```
`String.prototype.indexOf(searchString[, fromIndex])`  
&emsp;&emsp;返回`searchString`第一次出现的位置。搜索从`fromIndex`开始。如果未找到该值，则返回`-1`。 `fromIndex`是一个整数，默认值为0.如果`fromIndex`小于`0`或大于`String.prototype.length`，则搜索从索引`0`和`String.prototype.length`之间开始。
```Javascript
>> 'abcdef'.indexOf('de', 2)
3
```
`String.prototype.lastIndexOf(searchString[, fromIndex])`  
&emsp;&emsp;返回`searchString`最后一次出现的位置，从`f`romIndex`向后搜索。如果未找到该值，则返回`-1`。如果`searchString`为空，则返回`fromIndex`。
```Javascript
>> "nginx".lastIndexOf("gi")
1
```
`String.prototype.length`  
&emsp;&emsp;返回字符串的长度
```Javascript
>> 'αβγδ'.length
4
```
`String.prototype.match([regexp])`  
&emsp;&emsp;匹配`regexp`的字符串
```Javascript
>> 'nginx'.match( /ng/i )
'ng'
```
`String.prototype.padEnd(length [, string])`  
&emsp;&emsp;返回指定长度的字符串，其中`string`应用于指定字符串的末尾 (0.2.3).
```Javascript
>> '1234'.padEnd(8, 'abcd')
'1234abcd'
```
`String.prototype.padStart(length [, string])`  
&emsp;&emsp;返回指定长度的字符串，其中`string`应用于指定字符串的开头 (0.2.3).
```Javascript
>> '1234'.padStart(8, 'abcd')
'abcd1234'
```
`String.prototype.repeat(number)`  
&emsp;&emsp;返回具有指定字符串副本数的字符串。
```Javascript
>> 'abc'.repeat(3)
'abcabcabc'
```
`String.prototype.replace([regexp|string[, string|function]])`  
&emsp;&emsp;返回一个新字符串，其中包含由`string`或`function`替换的模式（`String`或`regexp`）的匹配项
```Javascript
>> 'abcdefgh'.replace('d', 1)
'abc1efgh'
```
`String.prototype.search([regexp])`  
&emsp;&emsp;使用`regexp`搜索字符串
```Javascript
>> 'abcdefgh'.search('def')
3
```
`String.prototype.slice(start[, end])`  
&emsp;&emsp;返回一个新字符串，其中包含`start`和`end`之间或从字符串`start`到结尾之间的原始字符串的一部分。
```Javascript
>> 'abcdefghijklmno'.slice(NaN, 5)
'abcde'
```
`String.prototype.split(([string|regexp[, limit]]))`  
&emsp;&emsp;返回`String`与`regexp`的匹配。可选的`limit`参数是一个整数，它指定要查找的拆分数的限制。
```Javascript
>> 'abc'.split('')
[
 'a',
 'b',
 'c'
]
```
`String.prototype.startsWith(searchString[, position])`  
&emsp;&emsp;如果`String`以`searchString`的字符开头，则返回`true`，否则返回`false`。可选的`position`参数是此字符串中开始搜索的位置。默认值为0。
```Javascript
>> 'abc'.startsWith('abc')
true
> 'aabc'.startsWith('abc')
false
```
`String.prototype.substr(start[, length])`  
&emsp;&emsp;返回从`String`的`start`的`length`长度的，或从`start`到结尾的字符串。
```Javascript
>>  'abcdefghijklmno'.substr(3, 5)
'defgh'
```
`String.prototype.substring(start[, end])`  
&emsp;&emsp;返回`String`中`start`和`start`之间或从`start`到结尾的部分
```Javascript
>> 'abcdefghijklmno'.substring(3, 5)
'de'
```
`String.prototype.toBytes(start[, end])`  
&emsp;&emsp;(njs specific) 将`code point`最多为255的`Unicode`字符串序列化为字节字符串，否则返回`null`
`String.prototype.toLowerCase()`  
&emsp;&emsp;转小写，该方法仅支持简单的`Unicode folding`
```Javascript
>> 'ΑΒΓΔ'.toLowerCase()
'αβγδ'
```
`String.prototype.toString([encoding])`  
&emsp;&emsp;如果未指定编码，则返回`ECMAScript`中指定的`Unicode`字符串或字节字符串。

&emsp;&emsp;(njs specific) 如果指定了`encoding`，则将`字节字符串`编码为`hex`，`base64`或`base64url`
```Javascript
>>  'αβγδ'.toUTF8().toString('base64url')
'zrHOss6zzrQ'
```
`String.prototype.toUpperCase()`  
&emsp;&emsp;转换为大写。该方法仅支持简单的`Unicode folding`
```Javascript
>> 'αβγδ'.toUpperCase()
'ΑΒΓΔ'
```
`String.prototype.toUTF8(start[, end])`  
&emsp;&emsp;(njs specific) 使用`UTF8`编码将`Unicode`字符串序列化为`字节字符串`。
```Javascript
>> 'αβγδ'.toUTF8().length
8
>> 'αβγδ'.length
4
```
`String.prototype.trim()`  
&emsp;&emsp;从字符串的两端删除空格。
```Javascript
>> '   abc  '.trim()
'abc'
```
`encodeURI(URI)`  
&emsp;&emsp;通过将某些字符的每个实例替换为表示字符的`UTF-8`编码的一个，两个，三个或四个转义序列来对`URI`进行编码
```Javascript
>> encodeURI('012αβγδ')
'012%CE%B1%CE%B2%CE%B3%CE%B4'
```
`encodeURIComponent(encodedURIString)`  
&emsp;&emsp;通过将某些字符的每个实例替换为表示字符的`UTF-8`编码的一个，两个，三个或四个转义序列来对`URI`进行编码。
```Javascript
>> encodeURIComponent('[@?=')
'%5B%40%3F%3D'
```
`decodeURI(encodedURI)`  
&emsp;&emsp;解码上面编码的URI。
```Javascript
>> decodeURI('012%CE%B1%CE%B2%CE%B3%CE%B4')
'012αβγδ'
```
`decodeURIComponent(decodedURIString)`  
&emsp;&emsp;解码先前编码的URI的编码组件
```Javascript
>> decodeURIComponent('%5B%40%3F%3D')
'[@?='
```

## JSON
JSON对象（ES 5.1）提供了将njs值转换为JSON格式和从JSON格式转换的函数。

`JSON.parse(string[, reviver])`  
&emsp;&emsp;将表示`JSON`数据的`string`转换为`njs`对象(`{...}`)或数组(`[...]`)。可选`reviver`参数是一个函数`function(key, value)`，它将为每个键值对回调，并转换该值。  
`JSON.stringify(value[, replacer] [, space])`  
&emsp;&emsp;将`njs`对象转换回`JSON`。必选参数`value`通常是要转换的`JSON`对象或数组。如果值具有`toJSON()`方法，则会调用该方法进行序列化。可选参数`replacer`是转换结果的函数或数组。可选参数`space`是字符串或数字。如果是数字，则表示结果前的空白数（不超过10）。如果是字符串，则将其用作空格（或前10个字符）。如果省略或为null，则不使用空格。
```Javascript
>> var json = JSON.parse('{"a":1, "b":true}')
>> json.a
1

>> JSON.stringify(json)
'{"a":1,"b":true}'

>> JSON.stringify({ x: [10, undefined, function(){}] })
'{"x":[10,null,null]}'

>> JSON.stringify({"a":1, "toJSON": function() {return "xxx"}})
'"xxx"'

# Example with function replacer

>> function replacer(key, value) {return (typeof value === 'string') ? undefined : value}
>>JSON.stringify({a:1, b:"b", c:true}, replacer)
'{"a":1,"c":true}'
```
## Crypto
Crypto模块提供加密功能支持。`require('crypto')`返回Crypto模块对象。

`crypto.createHash(algorithm)`  
&emsp;&emsp;创建并返回一个`Hash`对象，该对象可用于使用给定算法生成散列摘要。`algorithm`可以是`md5`，`sha1`和`sha256`。  
`crypto.createHmac(algorithm, secret key)`  
&emsp;&emsp;创建并返回使用给定算法和密钥的HMAC对象。`algorithm`可以是`md5`，`sha1`和`sha256`。  
## Hash
`hash.update(data)`  
&emsp;&emsp;使用`data`更新`hash`内容。  
`hash.digest([encoding])`  
&emsp;&emsp;使用`hash.update()`传递的所有数据，计算指纹。`encoding`可以是`hex`，`base64`和`base64url`。如果未提供编码，则返回`字节字符串`。
```Javascript
string is returned.
>> var cr = require('crypto')
undefined

>> cr.createHash('sha1').update('A').update('B').digest('base64url')
'BtlFlCqiamG-GMPiK_GbvKjdK10'
```
## HMAC
`hmac.update(data)`  
&emsp;&emsp;使用`data`更新`HMAC`内容。  
`hmac.digest([encoding])`  
&emsp;&emsp;使用`hmac.update()`传递的所有数据，计算指纹。`encoding`可以是`hex`，`base64`和`base64url`。如果未提供编码，则返回`字节字符串`。
```Javascript
>> var cr = require('crypto')
undefined

>> cr.createHmac('sha1', 'secret.key').update('AB').digest('base64url')
'Oglm93xn23_MkiaEq_e9u8zk374'
```
## Timers
`clearTimeout(timeout)`  
&emsp;&emsp;取消由`setTimeout()`创建的`timeout`对象。  
`setTimeout(function, ms[, arg1, argN])`  
&emsp;&emsp;在指定的毫秒数后调用`function`。可以将一个或多个可选参数传递给`function`。返回`timeout`对象。
```Javascript
function handler(v)
{
    // ...
}

t = setTimeout(handler, 12);

// ...

clearTimeout(t);
```
## File System
文件系统模块提供文件操作。 `require('fs')`返回模块对象。

`appendFileSync(filename, data[, options])`  
&emsp;&emsp;同步将`data`追加到文件`filename`中。如果该文件不存在，则将创建该文件。 `options`参数应该是具有以下属性的对象：   
&emsp;&emsp;`mode`  
&emsp;&emsp;&emsp;&emsp;模式选项， 缺省`0o666`  
&emsp;&emsp;`flag`  
&emsp;&emsp;&emsp;&emsp;文件系统[flag](#File System Flags)，缺省`a`    
`readFileSync(filename[, options])`  
&emsp;&emsp;同步读取文件`filename`的内容。 `options`参数指定字符编码。如果未指定，则返回`字节字符串`。如果指定了`utf8`编码，则返回`Unicode`字符串。否则`options`对象属性如下：  
&emsp;&emsp;`encoding`  
&emsp;&emsp;&emsp;&emsp;指定编码，默认为`utf8`  
&emsp;&emsp;`flag`  
&emsp;&emsp;&emsp;&emsp;文件系统[flag](#File System Flags)，缺省`r`
```Javascript
>> var fs = require('fs')
undefined
>> var file = fs.readFileSync('/file/path.tar.gz')
undefined
>> var gzipped = /^\x1f\x8b/.test(file); gzipped
true
```
`writeFileSync(filename, data[, options])`  
&emsp;&emsp;同步将数据`data`写入文件`filename`。如果该文件不存在，则将创建该文件，如果该文件存在，则将替换该文件。 `options`对象属性如下：  
&emsp;&emsp;`mode`  
&emsp;&emsp;&emsp;&emsp;模式选项， 缺省`0o666`  
&emsp;&emsp;`flag`  
&emsp;&emsp;&emsp;&emsp;文件系统[flag](#File System Flags)，缺省`w`  
```Javascript
>> var fs = require('fs')
undefined
>> var file = fs.writeFileSync('hello.txt', 'Hello world')
undefined
```
## File System Flags
`flag`选项可以接受以下值：

- a — 打开一个文件进行追加。如果文件不存在，则创建该文件
- ax — 与`a`相同但如果文件已存在则失败
- a+ — 打开文件进行阅读和追加。如果该文件不存在，则将创建该文件
- ax+ — 与`a+`相同但如果文件已存在则失败
- as+ — 打开文件以便以同步模式追加。如果该文件不存在，则将创建该文件
- r — 打开一个文件进行阅读。如果文件不存在，则会发生异常
- r+ — 打开文件进行读写。如果文件不存在，则会发生异常
- rs+ — 打开文件以便以同步模式进行读写。强制操作系统绕过本地文件缓存
- w — 打开文件进行写作。如果该文件不存在，则将创建该文件。如果文件存在，则将替换它
- wx — 与`w`相同但如果文件已存在则失败
- w+ — 打开文件进行读写。如果该文件不存在，则将创建该文件。如果文件存在，则将替换它
- wx+ — 与`w+`相同，但如果文件已存在则失败

```Javascript
/**
 * njs 文档翻译
 * njs当前版本 v0.3.0
 * @author Mustang
 * @date 2019-04-03
 * @git https://github.com/yuenshui/njs
 */
```


