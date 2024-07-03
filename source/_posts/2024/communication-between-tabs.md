---
title: 浏览器同域跨标签页通信
date: 2024-07-03 19:31:01
tags:
---

从用户体验上来讲，站点切换页面过程中不建议弹出多个浏览器标签页，这样会分散用户注意力，增加用户切换标签的操作成本。再好的交互规范，也抗不住产品经理或交互设计师的脑洞大开，在同一个站点多标签访问的时候，标签页间的通信不可避免。下面对几种常见方式，做个比较。

<!--more-->

#### 方式一：LocalStorage

因为相同域名下，localStorage 数据是可以共享，而且是单例。所以利用 localStorage 作为中介，可以实现标签间通信。所幸的是 localStorage 在 setItem 的时候，会触发 storage 事件，那么我们只要在通信双方都监听上该事件，就可以愉快的通信的了。

```
// 发送方
function postMsg(info:string) {
  localStorage.setItem('channeld',info)
}
// 接收方
function receiveMsg(callback: (info:string) => void) {
  window.addEventListener('storage', function(event){
    if(event.key ==='channelId') {
      callback(event.newValue)
    }
  })
}
```

优点：简单；接收方离线，消息不丢失，接收方上线后可以决定是否处理消息
缺点：使用大小有限制 5m（也不是什么大问题），stroage 只能放字符串，所以消息存之前需要序列化

#### 方式二：BroadcastChannel

BroadcastChannel 对象，顾名思义是以广播信息的通信，它创建的通信实例（相同参数创建的会被当作是同一个通信通道，所以不同标签下创建的通道可以相互通信），只要是订阅了消息的，都可以接收到。当然上述的 localStorage 可以看做的广播的方式通信的。

```
// 发送接收都需要
const bc = new BroadcastChannel("channelId");

// 发送方
bc.postMessage(info: any);
// 接收方
bc.onmessage(function(event){
  const info = event.data
})
```

优点: 对传输的数据类型没有要求，使用方便
缺点：旧的浏览器不能使用

#### 方式三：postMessage

虽然 postMessage 可以实现跨标签通信，但是它还是利用同一个 tab 下的 window 宿主对象实现的。也就是说，消息发送方需要消息接收方的的宿主对象。比如父窗口打开子窗口：那么父窗口通过 window.open 的返回值，子窗口的 window 对象；子窗口直接通过 opener 就可以获取到父窗口的 window 对象。

```
cWindow = window.open(url)

// 父窗口发送
cWindow.postMessage(info, /* 可选域名参数 */)

// 子窗口接收
window.addEventListener('message', function(event){
    // 有必要可以判断来域名来源 event.origin
    const info = event.data
})
```

优点：数据类型没要求，通信是通过同一个 window 对象实现的，所以对域名没有要求，可以实现跨域通信
缺点：需要找到通信方的 window 对象，比较麻烦，特别的多级打开的窗口。

#### 方式四：SharedWorker API

利用共享的线程实现，类似 BroadcastChannel，它是创建了一个可以跨标签的线程实现的消息传递。创建 SharedWorker 实例时候需要制定 worker 脚本（相同脚本文件参数会创建的是同一个 worker）。

```
// 发送接收都需要
const worker = new SharedWorker('shareWorker.js')

// 发送方
worker.port.possMessage({type:'', info: ''})
// 接收方
worker.port.onmessage(function(event){
  const info = event.data
})

// shareWorker.js
const connections = [];

onconnect = function (event) {
  var port = event.ports[0];
  connections.push(port);

  port.onmessage = function (event) {
    connections.forEach(function (conn) {
      if (conn !== port) {
        conn.postMessage(event.data);
      }
    });
  };

  port.start();
};
```

优点：通信方式更灵活（复杂）
缺点：旧的浏览器不能使用，需要额外 woker 脚本
