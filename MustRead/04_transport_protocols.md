# 常见的音视频传输协议对比

| 协议名称 | 作用 | 发布时间 | 拥有者 | 优点 | 缺点                   | 
| --- | --- | --- | --- | --- |----------------------| 
| RTP | 实时传输协议（Real-time Transport Protocol）| 1996 | IETF | 实时性好、支持任意编码格式、建立连接速度快 | 无法保证可靠性，需要结合RTCP进行控制 | 
| RTSP | 实时流协议（Real Time Streaming Protocol）| 1998 | IETF | 实现点播和直播功能、支持播放器拖动等 | 数据完整性无法得到保证，不稳定      | 
| RTMP |                  实时消息传输协议（Real-Time Messaging Protocol）| 2002 | Adobe | 实时性好、支持流媒体数据和交互式协议通信 | 兼容性受限、安全性问题          | 
| HTTP | 超文本传输协议（HyperText Transfer Protocol）| 1991 | IETF |  兼容性好、易于开发、支持多种媒体格式 | 实时性差、延迟高             | 
| SIP | 会话初始化协议（Session Initiation Protocol）| 1999 | IETF | 易于扩展、支持多种传输方式（TCP、UDP、TLS等） | 安全性存在问题              | 
| HLS |     HTTP直播流媒体协议（HTTP Live Streaming）| 2009 | Apple | 兼容性好、支持多种容器和编码格式 | 实现过程复杂、HLS直播端播放器有延迟  | 
| WebRTC |  Web实时通信协议（Web Real-Time Communication）| 2011 | W3C |集成于浏览器内、无需安装插件 | 不支持所有浏览器，需要服务器支持     |