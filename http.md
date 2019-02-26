# HTTP协议
+ 基于TCP/IP
+ 应用层协议
+ 不涉及数据包传输
+ 规定客户端、服务器间通讯格式
+ 默认80端口(HTTPS:443)

## HTTP/0.9
+ 只有get命令： Get/index.html
+ TCP连接建立-->客户端请求服务器index.html-->服务器发送完、关闭TCP
+ 只能回应HTML格式

## HTTP/1.0
+ 命令：GET、POST、HEAD
+ 请求格式：GET/HTTP/1.0
+ 头信息：
1. User-Agent:Mozilla/5.0 (Macintpsh;Intel Mac OS X 10.10.5)
2. Accept:* / * **( * 表示可以接受任何格式)**
+ 回应格式：
1. HTTP/1.0 200 OK  **(200:状态吗，OK:状态描述)**
2. Content-Type:text/plain  **(数据格式，数据类型统称呢各位MIME TYPE,与Accept相对应)**
3. Expires:Thu,05 Dec 1997 16:00:00 GMT
4. Last-Modified:Web,5 August 1996 15:55:28 GMT
5. Server:Apache 0.84
6. < html >< head ></ head >< /html >
7. Content-Encoding:gzip **(数据压缩方法)**
+ 缺点：每个TCP连接只能发送一个请求
+ 解决：浏览器和服务器添加字段：Connection:keep-alive(要求不要关闭TCP连接，就可以复用)

## HTTP/1.1
+ 持久连接：Connection:keep-alive;主动关闭连接Connection:close
+ 管通机制：同一个连接可同时发送多个请求
+ Connection-Length告知回应的是哪次请求，耗时，解决：使用“分块传输编码”
+ Transfer-Encoding:chunked，表明回应将由数量未定的数据块组成
+ 命令：GET、POST、HEAD、PUT、PATCH、OPTIONS、DELETE
+ 请求头增加：HOST:www.example.com, 指定服务器域名
+ 缺点：同一个TCP连接，数据通信按次序进行，只有处理完一个回应才进行下一个，易造成”队头阻塞“
+ 解决：1、减少请求数；2、同时多开持久连接

## SPDY协议
解决HTTP/1.1效率不高、在Chrome上可行，被当作HTTP/2基础

## HTTP/2协议
+ 二进制协议：头信息与数据体为二进制，统称为“帧”，好处：可定义额外的帧
+ 多工：复用TCP连接，一个连接里客户端和浏览器同时发送多个请求/回应，不用按顺序一一对应，避免队头阻塞
（eg:A、B请求，A请求耗时，发送处理部分，回应B,再发送A剩余部分）
+ 数据流：每个请求/回应的数据包，数据包发送标记数据流ID区分：客户端奇数、服务器偶数；
         取消请求：HTTP/1.1关闭TCP、HTTP/2发送信号（RST、STREAM帧）
+ 头信息压缩：除了gzip/compress，客户端和服务器同时维护头信息表，以后发送索引号即可。
+ 服务器推送：服务器预期到客户端请求网页后，可能会再请求静态资源，主动把静态资源随网页一起发送。

## HTTP与HTTPS
+ HTTPS相对于HTTP加入了SSL层（SSL依靠证书验证服务器身份）
+ 需ca申请收费证书
+ 安全但耗时多，缓存不是很好
+ 注意兼容HTTP和HTTPS
