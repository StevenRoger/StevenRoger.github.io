---
title: websocket初探
description: websocket协议的学习及demo程序测试
categories:
- C++
tags:
- C++
---

黑夜啊，一无所有，请给我一点灵感！

麻蛋，我又不能做湿人！

websocket是什么，我就不介绍了，能看到这篇文章的，都是会用Google的人。

本来好好的一个东西，Linux上很多好用的SDK，偏偏本人最近接到的项目却是windows上的，还是C++的，还要支持快速开发，你看看libwebsockets、websocket++，我都用不了啦！

自己手动写吧！

对于协议这种，自己手动写，我也是小白，所以，你懂的咯。 
websocket的原理，很多资料的，基本了解之后，就知道我们第一步要做的事情就是找一个支持websocket的浏览器作为客户端，什么firefox、chrome都是可以的。 
第二步，老老实实写个简单的server端，用socket监听一个可用的端口。就像这样吧：
```cpp
    WSADATA wsaData;    
    if (WSAStartup(MAKEWORD(2,2), &wsaData) != NO_ERROR)// Initialize Winsock
        printf("Error at WSAStartup()\n");
    //create the SOCKET
    listenfd = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);   

    struct sockaddr_in server = { 0 };
    server.sin_family = AF_INET;    
    server.sin_addr.s_addr = inet_addr(_host);
    server.sin_port = htons((u_short)atoi(_port));

    // Bind the socket.
    if (bind( listenfd, (SOCKADDR*)&server, sizeof(server)) == SOCKET_ERROR) 
    {
        printf("Error on server bind.\n");
        return;
    }       
    if (listen(listenfd, SOMAXCONN ) == SOCKET_ERROR)
    {
        printf("Error listening on socket.\n");
        return;
    }
    printf("Server is listening on socket... %s:%u\n", inet_ntoa(server.sin_addr), ntohs(server.sin_port));
    while (true)
    {
        struct sockaddr clientinfo = { 0 };
        SOCKET connectfd = accept(listenfd, &clientinfo, NULL);     
        struct sockaddr_in* ipv4info = (struct sockaddr_in*)&clientinfo;
        printf("Client connected on socket %x. Address %s:%d\n", connectfd, 
        inet_ntoa(ipv4info->sin_addr), ntohs(ipv4info->sin_port));
    }
```

然后自己加个接收函数，把接收到的报文打出来，用第一步找到的浏览器，用`http://www.blue-zero.com/WebSocket/`测试看看是不是收到了这些东西：
```
GET / HTTP/1.1
Host: 127.0.0.1:38950
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Origin: http://www.blue-zero.com
Sec-WebSocket-Version: 13
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.110 Safari/537.36
Accept-Encoding: gzip, deflate, sdch
Accept-Language: zh-CN,zh;q=0.8
Sec-WebSocket-Key: t1GMr+9AKhIzGNJwif4b2w==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

看了协议的就知道，这就是需要我们完成握手协议了。

握手协议是。。。
> 服务器端需要使用客户端发送的这个Key进行校验 ，然后返回一个校验过的字符串给客户端，客户端验证通过后才能正式建立Socket连接。服务器验证方法是： 首先进行 Key + 全局唯一标示符（GUID）“258EAFA5-E914-47DA-95CA-C5AB0DC85B11”连接起来，然后将连接起来的字符串使用SHA-1哈希加密，再进行base64加密，将得到的字符串返回给客户端作为握手依据。其中GUID是一个对于不识别WebSocket的网络端点不可能使用的字符串

基本就是这样啦！提一下注意的地方，用正则表达式获取KEY的时候，不要多任何东西，特别是\r\n，哈希加密，最好先写个算法测试，要与网上的加密工具得到的结果一致才行哦！然后是base64加密算法，你可以拿我这个验证，刚刚收到的KEY加上唯一标识字串，进行SHA-1加密，再进行base64编码，得到”18+q3KnnB/LLiQ1AgvyFPut9oUI=”这样一个字符串，然后将它拼到HTTP协议应答报文里

```
HTTP/1.1 101 Switching Protocols
Upgrade: WebSocket
Connection: Upgrade
Sec-WebSocket-Accept: 18+q3KnnB/LLiQ1AgvyFPut9oUI=
```

注意报文后有两个换行符！ 
将这个报文send给客户端，握手完成，后面你想干嘛就干嘛啦！

不对！ 
还有哦~~ 
在服务器发消息的时候，还有个麻烦的东西，要自己拼协议报文

```
0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
 +-+-+-+-+-------+-+-------------+-------------------------------+
 |F|R|R|R| opcode|M| Payload len |    Extended payload length    |
 |I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
 |N|V|V|V|       |S|             |   (if payload len==126/127)   |
 | |1|2|3|       |K|             |                               |
 +-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
 |     Extended payload length continued, if payload len == 127  |
 + - - - - - - - - - - - - - - - +-------------------------------+
 |                               |Masking-key, if MASK set to 1  |
 +-------------------------------+-------------------------------+
 | Masking-key (continued)       |          Payload Data         |
 +-------------------------------- - - - - - - - - - - - - - - - +
 :                     Payload Data continued ...                :
 + - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
 |                     Payload Data continued ...                |
 +---------------------------------------------------------------+
 ```

 还没好好了解上面这个报文的意思的话，又急着看到效果，跟我一样，先将第一个字节写死0x81，第二个自己自己填，就是你要发送的消息的长度，注意是Payload的长度哦！

这样就没得问题了！更多的，就需要各位详细了解协议了。

附上代码吧！ 
[源码下载](http://download.csdn.net/detail/u011414997/9529090)