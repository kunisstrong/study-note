```
let websocket = null
// 判断当前浏览器是否支持websocket，主要此处要更换为自己的地址
if('WebSocket' in window){
	websocket = new  WebSocket("ws://localhost:80/p2p")
}else {
	console.log('not support websocket')
}

// 连接发生错误的回调方法
websocket.onerror = () => {
	console.log('服务器通信失败')
}

// 连接成功建立的回调方法
websocket.onopen = () => {
	console.log('与服务器已建立连接')
}

// 接收到消息的回调方法
websocket.onmessage = (event) => {
	console.log(event.data)
}

// 连接关闭的回调方法
websocket.onclose = () => {
	console.log('websocket连接已关闭')
}

// 监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会跑出异常
window.onbeforeunload = () => {
	websocket.close()
}
```

