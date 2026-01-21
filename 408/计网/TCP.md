---yaml  
topic: TCP（连接管理/可靠传输/流控/拥塞控制）  
module: 传输层  
input_type: 概念  
exam_weight: 高  
one_liner: 在不可靠IP上提供可靠有序字节流  
why_needed: [应对丢包乱序重复, 匹配接收端与网络容量]  
keywords: [三次握手, 四次挥手, 序号seq, 确认ACK, 重传RTO, 快重传, 滑动窗口, rwnd, cwnd, ssthresh, 慢启动, 拥塞避免, 快恢复, TIME_WAIT, 2MSL, Tahoe, Reno, NewReno, SACK]  
signals: [SYN, FIN, TIME_WAIT, 3个重复ACK, RTO超时, cwnd, ssthresh, rwnd, MSS]  
pitfalls: [TIME_WAIT原因, rwnd与cwnd混淆, 3dupACK与超时混淆, CLOSE_WAIT含义]  
comparisons: [TCP_vs_UDP, RTO_vs_3dupACK, Tahoe_vs_Reno_vs_NewReno]  
links: [MSS与MTU, 可靠传输机制, 滑动窗口, 拥塞控制, HTTP长连接]  
source: 2026计算机网络.pdf 第5章 传输层 TCP  
pattern_name: TCP四大题型通用解题模板  
last_update: 2026-01-21  
tags: [#计网, #408, #TCP, #传输层]  
---endyaml

## 1) 速记总结

- 本质：连接+序号+窗口+重传，把IP变成可靠字节流。
    
- 解决：丢包乱序重复；接收端与网络容量匹配（rwnd/cwnd）。
    
- 考法：握手/挥手状态机；TIME_WAIT；cwnd曲线（RTO vs 3dupACK）；min(rwnd,cwnd)。
    

## 2) 机制链条（可背版本）

连接：主动打开→SYN→SYN+ACK→ACK→ESTABLISHED→代价：状态/半连接→异常：SYN flood  
可靠：发段并缓存→累计ACK→RTO或3dupACK→重传→按序交付→代价：缓存/控制流量  
流控：接收端通告rwnd→发送端≤rwnd→零窗口探测→避免死锁  
拥塞：慢启动→拥塞避免→丢包(RTO/3dupACK)→调整cwnd/ssthresh→Tahoe/Reno/NewReno分支

## 3) 状态机/流程（伪图示）

三次握手：  
[CLOSED]--SYN-->[SYN_SENT]--SYN+ACK/ACK-->[ESTABLISHED]  
[LISTEN]--SYN-->[SYN_RCVD]--ACK-->[ESTABLISHED]

四次挥手：  
主动：[ESTABLISHED]->FIN_WAIT_1->FIN_WAIT_2->TIME_WAIT(2MSL)->CLOSED  
被动：[ESTABLISHED]->CLOSE_WAIT->LAST_ACK->CLOSED

## 4) 代价与边界

- 代价：连接建立RTT；缓存+定时器；ACK控制开销；拥塞算法复杂。
    
- 边界/不适用：强实时/可容忍丢包场景；无线高误码下易误判拥塞。
    
- 风险：SYN flood、资源耗尽、Bufferbloat带来RTT膨胀。
    

## 5) 对比与替代（最小对比表）

|维度|TCP|UDP|
|---|---|---|
|目标|可靠有序字节流|低开销数据报|
|代价|连接状态/重传/拥塞控制|应用自担可靠性|
|典型考法|握手挥手、cwnd曲线、TIME_WAIT|首部字段、校验、应用层补救|

## 6) 真题命题模板

- SYN/FIN/TIME_WAIT → 问法：状态迁移/为何需要 → 必答点：关键状态+2MSL两原因
    
- cwnd/ssthresh/3dupACK/RTO → 问法：画曲线/算窗口 → 必答点：慢启动/拥塞避免/两类丢包分支
    
- rwnd/接收缓存/零窗口 → 问法：能发多少/死锁如何避免 → 必答点：min(rwnd,cwnd)+persist探测
    
- 常见变式：Tahoe vs Reno vs NewReno（多丢包/快恢复差异）
    

## 7) 易错点清单（带自检）

- 错误结论：收到FIN就都关闭
    
    - 错因：忽略半关闭与CLOSE_WAIT
        
    - 自检：被动端是否进入CLOSE_WAIT、还可能继续发送？
        
- 错误结论：TIME_WAIT只为“对方关闭”
    
    - 错因：没写出ACK可重发+清理旧报文
        
    - 自检：能否说出2个原因？
        
- 错误结论：窗口=拥塞窗口
    
    - 错因：混淆rwnd/cwnd
        
    - 自检：发送上限是否写成min(rwnd,cwnd)？
        

## 8) 迁移题型模型（可直接套用）

- 识别：看到SYN/FIN→状态机；看到cwnd/ssthresh→拥塞；看到rwnd→流控；看到RTO/3dupACK→重传分支
    
- 步骤：1)定题型 2)列变量 3)按事件推进 4)给结论（状态/下一报文/窗口值或曲线）
    
- 验算/检查点：是否区分RTO与3dupACK；是否写出TIME_WAIT两原因；ack是否为“期望下一个字节”。
    

## 9) 知识网络（用于串题）

- 上游前置：IP尽力而为、RTT、MSS/分段、差错与丢包模型
    
- 下游应用：HTTP/HTTPS、长连接、TLS
    
- 横向对比：UDP/QUIC；链路层ARQ vs 端到端TCP