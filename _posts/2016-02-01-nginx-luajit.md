---
layout: post
title: "How To Install Nginx LuaJit"
image: nginx.jpg
video: false
---

https://github.com/openresty/lua-nginx-module

#### 安装
 - 安装 [LuaJit](http://luajit.org/download.html)
 - 下载 [ngx_devel_kit](https://github.com/simpl/ngx_devel_kit/tags)
 - 下载 [ngx_lua](https://github.com/openresty/lua-nginx-module/tags)
 - 下载 [Nginx](http://nginx.org/en/download.html)

{% highlight shell %}
 wget 'http://nginx.org/download/nginx-1.9.7.tar.gz'
 tar -xzvf nginx-1.9.7.tar.gz
 cd nginx-1.9.7/

 # tell nginx's build system where to find LuaJIT 2.0:
 export LUAJIT_LIB=/path/to/luajit/lib
 export LUAJIT_INC=/path/to/luajit/include/luajit-2.0

 # tell nginx's build system where to find LuaJIT 2.1:
 export LUAJIT_LIB=/path/to/luajit/lib
 export LUAJIT_INC=/path/to/luajit/include/luajit-2.1

 # or tell where to find Lua if using Lua instead:
 #export LUA_LIB=/path/to/lua/lib
 #export LUA_INC=/path/to/lua/include

 # Here we assume Nginx is to be installed under /opt/nginx/.
 ./configure --prefix=/opt/nginx \
         --with-ld-opt="-Wl,-rpath,/path/to/luajit-or-lua/lib" \
         --add-module=/path/to/ngx_devel_kit \
         --add-module=/path/to/lua-nginx-module

 make -j2
 make install
{% endhighlight %}

#### 测试

在nginx.conf中添加下面部分内容

{% highlight conf %}
location /hello {
	default_type 'text/plain';
	content_by_lua 'ngx.say("hello, world")';
}
{% endhighlight %}

##### 验证结果
``` shell
[root@1184c0eeaa0a nginx-1.8.1]# curl 127.0.0.1:8080/hello
hello, world
```
