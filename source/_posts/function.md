---
title: hexo启动失败：line.matchAll is not a function
date: 2021-05-16 15:59:36
tags: 
- hexo
- 踩坑
categories: 
- CS
---

之前是输入 hexo 命令是可以正常启动的，今天试着写第一篇文章发现hexo的命令都用不了，会报下面这个错。

``` bash
(node:28558) ExperimentalWarning: The fs.promises API is experimental
INFO  Validating config
INFO  Start processing
FATAL { err:
   TypeError: line.matchAll is not a function
       at res.value.res.value.split.map.line (/blog/blog/node_modules/hexo-util/lib/highlight.js:128:26)
       at Array.map (<anonymous>)
       at closeTags (/blog/blog/node_modules/hexo-util/lib/highlight.js:126:37)
       at highlight (/blog/blog/node_modules/hexo-util/lib/highlight.js:119:10)
       at highlightUtil (/blog/blog/node_modules/hexo-util/lib/highlight.js:23:16)
       at data.content.dataContent.replace (/blog/blog/node_modules/hexo/lib/plugins/filter/before_post_render/backtick_code_block.js:92:17)
       at String.replace (<anonymous>)
       at Hexo.backtickCodeBlock (/blog/blog/node_modules/hexo/lib/plugins/filter/before_post_render/backtick_code_block.js:19:30)
       at Hexo.tryCatcher (/blog/blog/node_modules/bluebird/js/release/util.js:16:23)
       at Hexo.<anonymous> (/blog/blog/node_modules/bluebird/js/release/method.js:15:34)
       at Promise.each.filter (/blog/blog/node_modules/hexo/lib/extend/filter.js:67:52)
       at tryCatcher (/blog/blog/node_modules/bluebird/js/release/util.js:16:23)
       at Object.gotValue (/blog/blog/node_modules/bluebird/js/release/reduce.js:166:18)
       at Object.gotAccum (/blog/blog/node_modules/bluebird/js/release/reduce.js:155:25)
       at Object.tryCatcher (/blog/blog/node_modules/bluebird/js/release/util.js:16:23)
       at Promise._settlePromiseFromHandler (/blog/blog/node_modules/bluebird/js/release/promise.js:547:31)
       at Promise._settlePromise (/blog/blog/node_modules/bluebird/js/release/promise.js:604:18)
       at Promise._settlePromiseCtx (/blog/blog/node_modules/bluebird/js/release/promise.js:641:10)
       at _drainQueueStep (/blog/blog/node_modules/bluebird/js/release/async.js:97:12)
       at _drainQueue (/blog/blog/node_modules/bluebird/js/release/async.js:86:9)
       at Async._drainQueues (/blog/blog/node_modules/bluebird/js/release/async.js:102:5)
       at Immediate.Async.drainQueues [as _onImmediate] (/blog/blog/node_modules/bluebird/js/release/async.js:15:14) } 'Something\'s wrong. Maybe you can find the solution here: %s' '\u001b[4mhttps://hexo.io/docs/troubleshooting.html\u001b[24m'
```

随便搜了一下没发现解决办法，后来想了下可能是node版本问题。
因为第一次用hexo启动时我在提示下会切换到 v12.4.0 版本，今天重新开了一个命令行后node版本就不对了，我用的又是比较新的hexo版本，系统默认的node版本又比较旧。

可以尝试用nvm切换node版本到v12.4.0，下面是我正常启动的版本信息以供参考。

``` bash
% hexo -v
INFO  Validating config
hexo: 5.4.0
hexo-cli: 4.2.0
os: Darwin 19.6.0 darwin x64
node: 12.4.0
v8: 7.4.288.27-node.18
uv: 1.29.1
zlib: 1.2.11
brotli: 1.0.7
ares: 1.15.0
modules: 72
nghttp2: 1.38.0
napi: 4
llhttp: 1.1.3
http_parser: 2.8.0
openssl: 1.1.1b
cldr: 35.1
icu: 64.2
tz: 2019a
unicode: 12.1
``` 

这实际是一篇测试文章，写的比较简单。