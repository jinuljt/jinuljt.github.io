---
layout: post
title: "nginx memcached module使用方式"
comments: true
---

看nginx文档的时候发现有一个[memcached模块](http://nginx.org/en/docs/http/ngx_http_memcached_module.html)，感觉应该很有趣，于是研究了一下怎么使用。

nginx模块的表现和我最初的想法有些不同，还是需要服务端程序参与这个缓存的过程。我原先想的是，nginx可以把memcached层屏蔽掉。我觉得可以使用[lua模块](http://wiki.nginx.org/HttpLuaModule)来做到这件事情。有空可以尝试一下。


![架构图](/images/201509/nginx_memcached.png)

**Nginx 配置**
n```
server {
    location / {
	    set            $memcached_key "request_uri";
		memcached_pass 127.0.0.1:11211;
		error_page     404 502 504 = @fallback;
		default_type text/html;
	}

    location @fallback {
	    proxy_pass     http://127.0.0.1:8080;
	}
}

```

**服务端程序**
```
import time

import pylibmc

import web

urls = (
'/(.*)', 'Index'
)
app = web.application(urls, globals())

mc = pylibmc.Client(["127.0.0.1"], binary=True,
behaviors={"tcp_nodelay": True,
"ketama": True})


class Index:
def GET(self, name):
data =  str(time.time())
mc.set(web.ctx['env']['REQUEST_URI'], data, time=5)
return data

if __name__ == "__main__":
app.run()
```
