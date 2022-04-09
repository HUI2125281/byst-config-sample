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
  我还没全搞懂。 7 是覆盖 Max Boost Spawns，客户端的数值要小于等于服务器的。 
  

可以开三个窗口，分别是server，shadow，本地，本地发请求，使用nload在server和shadow查看网络流量

shadow
```
Device enp0s3 [10.0.0.28] (1/1):
========================================================================================================
Incoming:





                                                                       Curr: 905.06 kBit/s
                                                         .             Avg: 56.80 kBit/s
                                                         ##..          Min: 888.00 Bit/s
                                                        .####| .       Max: 17.16 MBit/s
                                                        #########|##|  Ttl: 3.59 GByte
Outgoing:





                                                                       Curr: 907.23 kBit/s
                                                         .             Avg: 66.16 kBit/s
                                                         ##..          Min: 7.08 kBit/s
                                                        .####| .       Max: 18.58 MBit/s
                                                        #########|##|  Ttl: 3.13 GByte

```
byst server
```
Device enp0s3 [10.0.0.40] (1/1):
================================================================================
Incoming:
                  #############|#####################
                  ###################################
                  ###################################
                  ###################################
                  ###################################
                 |###################################  Curr: 15.94 MBit/s
                 ####################################  Avg: 1000.38 kBit/s
                 ####################################  Min: 0.00 Bit/s
                 ####################################  Max: 241.18 MBit/s
                |####################################  Ttl: 147.48 GByte
Outgoing:



                    .
                    #|.
                   #### |                              Curr: 3.06 MBit/s
                   #######                             Avg: 188.32 kBit/s
                   #######..   .                ..||#  Min: 4.42 kBit/s
                  .##########|##||||||||#|||||#######  Max: 37.48 MBit/s
                 .###################################  Ttl: 129.10 GByte
```


本地的命令，使用curl，请求本地的http代理，输出速度等结果

```
curl  --proxy http://127.0.0.1:8083  -Lo /dev/null -skw "time_connect: %{time_connect} s\ntime_namelookup: %{time_namelookup} s\ntime_pretransfer: %{time_pretransfer} s\ntime_starttransfer: %{time_starttransfer} s\ntime_redirect: %{time_redirect} s\nspeed_download: %{speed_download} B/s\ntime_total: %{time_total} s\n\n"  http://cachefly.cachefly.net/200mb.test
```
