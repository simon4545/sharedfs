---
title: 非常巧妙的Node.js 数量可定的并行化代码
---

下面的代码 巧妙的使用Array(5)创建了有5个空元素的数组。再对这个数组进行遍历。
由于遍历的实现函数使用了async异步方法，方法体内的
``` js
var text = await fetch(url).then(res => res.text())
```
又会阻塞for循环的遍历行为，所以会有5个异步上下文在完成fetch行为后继续从cursor中找到下一个要处理的任务，实现了只有5路并发的多线程效果。

``` js
const items = [
  'https://httpbin.org/bytes/2',
  'https://httpbin.org/bytes/2',
  'https://httpbin.org/bytes/2',
  'https://httpbin.org/bytes/2',
  'https://httpbin.org/bytes/2',
  'https://httpbin.org/bytes/2',
  'https://httpbin.org/bytes/2',
  'https://httpbin.org/bytes/2'
]

// get a cursor that keeps track of what items have already been processed.
let cursor = items.entries();

// create 5 for loops that each run off the same cursor which keeps track of location
Array(5).fill().forEach(async (item,idx) => {
    for (let [index, url] of cursor){
        console.log('getting url is ', index, url,idx)
        // run your async task instead of this next line
        var text = await fetch(url).then(res => res.text())
        console.log('text is', text.slice(0, 20))
    }
})
console.log("asdf")
```