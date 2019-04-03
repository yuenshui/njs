Example Configuration  
Directives  
&emsp;&emsp;js_content  
&emsp;&emsp;js_include  
&emsp;&emsp;js_path  
&emsp;&emsp;js_set  
Request Argument  

`ngx_http_js_module`模块用于在nginx中实现位置和变量处理程序。

默认情况下不构建此模块。可在[此处下载和安装](https://github.com/yuenshui/njs/blob/master/install.md)说明。

## Example Configuration
```NginxConfig
load_module modules/ngx_http_js_module.so;
...

http {
    js_include http.js;

    js_set $foo     foo;
    js_set $summary summary;

    server {
        listen 8000;

        location / {
            add_header X-Foo $foo;
            js_content baz;
        }

        location = /summary {
            return 200 $summary;
        }

        location = /hello {
            js_content hello;
        }
    }
}
```
`http.js`文件:
```Javascript
function foo(r) {
    r.log("hello from foo() handler");
    return "foo";
}

function summary(r) {
    var a, s, h;

    s = "JS summary\n\n";

    s += "Method: " + r.method + "\n";
    s += "HTTP version: " + r.httpVersion + "\n";
    s += "Host: " + r.headersIn.host + "\n";
    s += "Remote Address: " + r.remoteAddress + "\n";
    s += "URI: " + r.uri + "\n";

    s += "Headers:\n";
    for (h in r.headersIn) {
        s += "  header '" + h + "' is '" + r.headersIn[h] + "'\n";
    }

    s += "Args:\n";
    for (a in r.args) {
        s += "  arg '" + a + "' is '" + r.args[a] + "'\n";
    }

    return s;
}

function baz(r) {
    r.status = 200;
    r.headersOut.foo = 1234;
    r.headersOut['Content-Type'] = "text/plain; charset=utf-8";
    r.headersOut['Content-Length'] = 15;
    r.sendHeader();
    r.send("nginx");
    r.send("java");
    r.send("script");

    r.finish();
}

function hello(r) {
    r.return(200, "Hello world!");
}
```
## 指令
```
语法:	js_content 函数;  
缺省:	—  
上下文:	location, limit_except  
将njs函数设置为位置内容处理程序。

语法:	js_include 文件;
缺省:	—
上下文:	http

指定在njs中实现位置和变量处理程序的文件。

语法:	js_path 路径;
缺省:	—
上下文:	http
0.3.0版本可用
为njs模块设置其他路径

语法:	js_set $variable 函数;
缺省:	—
上下文:	http
为指定的变量设置njs函数。
```
## 请求参数
每个`HTTP`的`njs`处理程序都接收一个参数，一个请求对象。