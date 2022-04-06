# byst-config-sample
给出可用的相关配置，供大家参考

# 遇到问题可以查这些问题
看看日志

用nc，curl查看服务是否可用，查看ip地址，测试下载速度
```
nc -z -w 3 10.0.0.40 3391  && echo $?
curl --proxy http://127.0.0.1:8081 -L ip.sb
curl  --proxy http://127.0.0.1:8080  -Lo /dev/null -skw "%{speed_download}\n"  http://cachefly.cachefly.net/100mb.test

```
用netstat看服务是否起来

一层层的找问题

请注意ipv6

请注意防火墙是否打开

使用iperf3,监听在ipv4，使用-4
```
iperf3 -s 127.0.0.1 -4 -p 7070
```
# 其他配置

BYST 单节点 N:M 加速测试，BYST Client 配置 M=7：
```
Connect To:STRING=vps1:6900; boost 7 -f:0.9,5,300
```
在 vps2 上装了个 HAProxy 做影子加速节点，BYST Client 配置双节点分布式 N:M 聚合加M=5：
```
Connect To:STRING=vps1:6900; boost 5 -f:0.9,3,100 -shadows:vps2:6900
```
使用iptables，作为影子节点做转发：
```

echo "1" > /proc/sys/net/ipv4/ip_forward
cat /proc/sys/net/ipv4/ip_forward
=1为开启转发
iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination BYST-Svr-IP:port
iptables -t nat -A POSTROUTING -p tcp -d BYST-Svr-IP --dport 8080 -j SNAT --to-source Local-ip
```
