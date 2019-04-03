# njs脚本语言
njs是JavaScript语言的一个子集，允许扩展nginx功能。njs是根据[ECMAScript 5.1](http://www.ecma-international.org/ecma-262/5.1/)（严格模式）创建的，带有一些[ECMAScript 6](http://www.ecma-international.org/ecma-262/6.0/)及更高版本的扩展。规范仍在不断推进。

- [下载和安装](https://github.com/yuenshui/njs/blob/master/install.md)
- [Changes](http://nginx.org/en/docs/njs/changes.html)
- [参考文档](https://github.com/yuenshui/njs/blob/master/Reference.md)
- [Examples](http://nginx.org/en/docs/njs/examples.html)
- [Compatibility](http://nginx.org/en/docs/njs/compatibility.html)
- [Command-line interface](http://nginx.org/en/docs/njs/cli.html)

- [ngx_http_js_module](http://nginx.org/en/docs/http/ngx_http_js_module.html)
- [ngx_stream_js_module](http://nginx.org/en/docs/stream/ngx_stream_js_module.html)

## Use cases
- 在请求到达上游服务器之前，`njs`中的复杂访问控制和安全性检查
- 操作`HTTP`头
- 编写灵活的异步内容处理程序和过滤器
有关更多`njs`案例，请参阅[示例](http://nginx.org/en/docs/njs/examples.html)和[博客](https://www.nginx.com/blog/tag/nginx-javascript-module/?_ga=2.178954210.795733934.1554170026-615231066.1554170026)文章。


## Basic HTTP Example
在nginx中使用njs:

- [安装](http://nginx.org/en/docs/njs/install.html)`njs`脚本语言

- 创建一个`njs`脚本文件，例如`hello_world.js`。有关`njs`属性和方法的列表，请参见[参考](https://github.com/yuenshui/njs/blob/master/Reference.md)。
```Javascript
function hello(r) {
    r.return(200, "Hello world!");
}
```
- 在`nginx.conf`文件中，启用`ngx_http_js_module`模块并使用`hello_world.js`脚本文件指定`js_include`指令：
```NginxConfig
load_module modules/ngx_http_js_module.so;

events {}

http {
    js_include hello_world.js;

    server {
        listen 8000;

        location / {
            js_content hello;
        }
    }
}
```
还有一个独立的[命令行](http://nginx.org/en/docs/njs/cli.html)实用程序，可以独立于nginx用于njs开发和调试。


```Javascript
/**
 * njs 官网首页
 * 原网址：http://nginx.org/en/docs/njs/
 * @author Mustang
 * @date 2019-04-03
 * @git https://github.com/yuenshui/njs
 */
```