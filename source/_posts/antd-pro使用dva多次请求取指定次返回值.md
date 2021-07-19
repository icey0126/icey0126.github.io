---
title: antd pro使用dva多次请求取指定次返回值
date: 2021-07-19 21:55:37
tags: 
- dva
- 踩坑
categories: 
- CS
- 前端
---

记录一下上周遇到的问题和解决方法。

antd pro官方推荐了dva进行数据流管理，因为官网有教程就不细说原理了，主要讲一下遇到的问题。

使用dva进行状态管理时出现了一个需求，需要使用同一个接口的不同query参数的返回值作为不同页面的展示数据。

但当某个需要query参数过滤的页面使用useEffect配合dispatch修改数据后，一下子出现了两次请求。当然，这个现象很合理。

一次是没加query参数的请求，一次是加上的。但是我没办法控制页面到底获取的是哪次请求获得的数据，实际上看下来好像拿到的都是第一次没加参数的数据，这当然不是我想要的。

本来有个做法是在获取请求并处理数据的hook这一层做一个判断，当有参数时使用第一种方式取值，没有时使用第二种。但是hook中useEffect的依赖项又出现了dispatch后一定会改变的值，导致了数据无限刷新。所以这个方法不行。

后来在同事的帮助下使用了lodash中的debounce做了一个去抖。

>debounce: 当调用动作n毫秒后，才会执行该动作，若在这n毫秒内又调用此动作则将重新计算执行时间。

实际在请求这个数据时，第一次请求是初始定义的没有参数的请求，第二次则是在使用了dispatch修改了请求参数导致的再请求，因为两次的间隔很短，而我们只需要第二次的请求结果，所以可以直接取消第一次的请求。

大致做法是使用下面这个函数替换掉原来的dispatch部分。

```bash
const debouncePatch = debounce(
    ({params,dispatch}) =>
        dispatch({type: "fetchData", payload: {params}}),
    200,
)

//原本的写法
    dispatch({
        type: "fetchData",
        payload: {params},
    })

//替换的写法
    debouncePatch({
        dispatch,
        params,
    })
```






