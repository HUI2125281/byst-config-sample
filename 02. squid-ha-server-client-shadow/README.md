使用haproxy+byst实现影子加速

你需要 先搞定 01. squid-server-client

然后在影子服务器上做转发，可以iptables或者haproxy

然后修改客户端的 client  confi.ini  
```
Connect To:STRING=<you server here>:3389
```   
  为
```  
  Connect To:STRING=<you byst server here>:3389; boost 7 -f:0.9,5,300   -shadows:<you byst shadow server here>:3389
```
  可是
```
  ; boost 7 -f:0.9,5,300  
```
  我还没搞懂。
