# ICMP

  

## 本质

- IP 的差错回报/诊断机制（封装在 IP 里发回源主机）

  

## 机制链条

触发(路由器/主机处理异常)

-> 生成ICMP(回源IP)

-> 携带原IP头+原数据前8B

-> 源主机/应用据此报错/调策略/诊断

  

## 高频类型

- Time Exceeded：TTL=0（traceroute）

- Dest Unreachable：网络/主机/端口不可达

- Frag Needed：DF=1且超MTU（PMTUD）

- Redirect：更优下一跳（常禁）

  

## 易错

- ICMP不负责可靠传输；可能被过滤/限速