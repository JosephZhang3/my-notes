
demo项目git地址

首先，我们点击 Connect 按钮

![](./websocket-console-1.png)

> 向上的绿色箭头表示 client 发送给 server 的消息
> 
> 向下的红色箭头表示 server 回传给 client 的消息

如上图，进行心跳检测并连接成功之后，client 会订阅一个名字叫做 /topic/greetings 的 topic ，这个主题可以理解成消息目的地，它对应 server 端配置的消息代理中的目的地前缀 desitination prefixes

![](./websocket-console-3.png)

接下来，我们输入 name 张江浩，然后点击 Send 按钮，控制台输出如下图

![](./websocket-console-2.png)

```json
{"content":"Yeap,how about you ? 张江浩."}
```
上面标记强调的 json 是 server 回传给 client 的，它是 utf-8 编码，包含36个英文字母/英文符号（36个字节）以及3个汉字（9个字节），所以内容长度是45个字节。

还应该注意到，每隔25秒，server 会自动传送一个h（heartbeat）心跳检测数据包给 client ，以确定 client 是否依然存活。心跳检测包的内容只有一个字母 h 

client 给 server 上传数据包后，server 端的心跳检测基准时间点会重置成给 client 端回传数据包成功的时间点（基于回传完成的时刻，隔25秒再进行心跳检测） 

