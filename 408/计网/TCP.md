【输入类型】概念  
【考点定位】

- 传输层：TCP 连接管理（3 次握手 / 4 次挥手、TIME_WAIT）
    
- 传输层：可靠传输（序号/确认/重传/滑动窗口）
    
- 传输层：拥塞控制（cwnd、ssthresh、RTO vs 3DupACK、Tahoe/Reno/NewReno）
    

---yaml  
topic: TCP  
module: 传输层  
input_type: 概念  
exam_weight: 高

one_liner: >-  
连接加序号窗口让不可靠IP变可靠字节流

why_needed:

- 端到端可靠传输
    
- 拥塞与流控分离
    

keywords:

- 三次握手
    
- 四次挥手
    
- 序号与确认号
    
- 滑动窗口
    
- RTO超时重传
    
- 快重传
    
- cwnd
    
- ssthresh
    
- TIME_WAIT
    

signals:

- SYN ACK FIN
    
- TIME_WAIT 2MSL
    
- 3个重复ACK
    
- RTO 超时
    
- 接收窗口rwnd
    
- 拥塞窗口cwnd
    

pitfalls:

- TIME_WAIT作用
    
- 流量控制vs拥塞控制
    
- 4次挥手与半关闭
    
- Reno恢复策略
    

comparisons:

- TCP_vs_UDP
    
- 流量控制_vs_拥塞控制
    
- 超时重传_vs_快重传
    

links:

- MSS与MTU
    
- RTT与RTO估计
    
- SACK与累计确认
    
- 端口与多路复用
    

source: >-  
2026计算机网络.pdf 第5章 传输层 5.3 TCP

pattern_name: TCP三件套_连管可靠拥塞  
last_update: 2026-01-21

tags:

- "#计网"
    
- "#408"
    
- "#TCP"  
    ---endyaml
    

## 1) 速记总结

- 本质：**连接管理 + 序号确认 + 窗口**，把“可能丢/乱/重”的IP变成“按序交付的字节流”
    
- 解决：
    
    - 可靠：丢了能发现并重传，乱了能排序
        
    - 控制：**流量控制**不压垮接收端，**拥塞控制**不压垮网络
        
- 考法：
    
    - 握手/挥手每步“带什么标志/确认什么/为什么不能少”
        
    - TIME_WAIT / CLOSE_WAIT 出现场景与危害
        
    - cwnd/ssthresh 曲线题：RTO vs 3DupACK 分支 + Reno/Tahoe差异
        

## 2) 机制链条（可背版本）

```text
触发 → 输入 → 核心步骤 → 输出 → 副作用 → 异常分支
建立连接 → 主动打开 → (1) SYN=x  (2) SYN=y,ACK=x+1  (3) ACK=y+1 → 双方进入ESTABLISHED → 维护状态/缓存/定时器 → SYN丢失: 超时重传; 半开连接: SYN队列压力

可靠传输 → 发送字节流 → (1) 分段+序号 (2) 累计ACK确认 (3) 超时重传RTO (4) 乱序缓存后按序交付 → 应用层“有序字节流” → 需要缓存/重传/定时器 → 丢包分支: RTO超时 vs 3重复ACK触发快重传

流量控制 → 接收端处理慢 → (1) 接收端通告rwnd (2) 发送端限制未确认数据≤rwnd → 防止接收端溢出 → 可能降低吞吐 → rwnd=0: 持续探测(零窗口探测)

拥塞控制 → 网络出现拥塞迹象 → (1) cwnd控制在途量 (2) 慢启动/拥塞避免 (3) 快重传/快恢复 → 缓解拥塞+稳定吞吐 → 牺牲时延/吞吐波动 → RTO: ssthresh大幅下降,cwnd重置; 3DupACK: 进入快速恢复
```

## 3) 状态机/流程（伪图示）

```text
【三次握手】
[CLOSED]
  --主动打开/发送SYN--> [SYN_SENT]
[SYN_SENT]
  --收SYN+ACK/发ACK--> [ESTABLISHED]
[LISTEN]
  --收SYN/发SYN+ACK--> [SYN_RCVD]
[SYN_RCVD]
  --收ACK--> [ESTABLISHED]
关键分支:
  if 第3次ACK丢: 服务器重发SYN+ACK; 客户端收到后再发ACK

【四次挥手(全双工/半关闭)】
[ESTABLISHED]
  --主动关闭/发FIN--> [FIN_WAIT_1]
[FIN_WAIT_1]
  --收ACK--> [FIN_WAIT_2]
[FIN_WAIT_2]
  --收FIN/发ACK--> [TIME_WAIT] --等待2MSL--> [CLOSED]
被动关闭端:
[ESTABLISHED] --收FIN/发ACK--> [CLOSE_WAIT] --应用close/发FIN--> [LAST_ACK] --收ACK--> [CLOSED]

TIME_WAIT要点:
  - 等“最后ACK可重传” + 清旧报文(2MSL)防止新连接被旧段污染
  - 典型考点: 为什么不是CLOSE_WAIT; 为什么要2MSL

【拥塞控制(必须会画/会分支)】
初始: cwnd=1 MSS; ssthresh=大
慢启动: cwnd指数增长(每RTT翻倍级)
拥塞避免: cwnd线性增长(每RTT+1 MSS级)

丢包分支:
  - RTO超时:
      ssthresh = cwnd/2
      cwnd = 1 MSS
      进入慢启动
  - 3重复ACK(快重传触发):
      ssthresh = cwnd/2
      Tahoe: cwnd=1 MSS -> 慢启动
      Reno: cwnd=ssthresh -> 快恢复(再转拥塞避免)
      NewReno: 若部分ACK(只确认一部分重传段)则继续留在快恢复直到“全部丢失段”被确认
```

## 4) 代价与边界

- 代价：
    
    - 状态维护：连接状态、发送/接收缓存、定时器、重传队列
        
    - 控制开销：ACK、窗口通告、拥塞窗口调整导致吞吐波动
        
- 边界/不适用：
    
    - 实时强、可容忍少量丢包（语音/直播）往往不选TCP（排队+重传带来抖动）
        
- 风险：
    
    - SYN洪泛/半开连接压力（队列耗尽）
        
    - TIME_WAIT过多导致端口资源紧张（高并发短连接）
        

## 5) 对比与替代（最小对比表）

|维度|TCP|UDP|
|---|---|---|
|目标|可靠有序字节流|尽力而为报文|
|代价|状态+重传+拥塞控制|低开销无连接|
|效果/适用|文件/网页/数据库|实时/自定义可靠|
|典型考法|握手挥手+窗口+cwnd|首部字段+校验+应用层补可靠|

## 6) 真题命题模板

- SYN/ACK/FIN → 问法：写出握手/挥手每步与状态 → 必答点：
    
    - 每步“确认谁的序号+1”
        
    - 半关闭：一端FIN后仍可接收数据
        
    - TIME_WAIT作用与2MSL
        
- TIME_WAIT / CLOSE_WAIT → 问法：哪个状态是谁没关/为什么堆积 → 必答点：
    
    - CLOSE_WAIT堆积=应用没close
        
    - TIME_WAIT堆积=主动关闭端多，需等2MSL
        
- cwnd/ssthresh/重复ACK/RTO → 问法：画cwnd变化、判断阶段、算新ssthresh → 必答点：
    
    - RTO vs 3DupACK两分支
        
    - Reno/Tahoe/NewReno恢复策略差一句
        

## 7) 易错点清单（带自检）

- 错误结论：TIME_WAIT是“等对方发完数据”
    
    - 错因：把TIME_WAIT当成被动等待
        
    - 自检：只要你是主动关闭端且收到了对方FIN，你就要进TIME_WAIT等2MSL
        
- 错误结论：拥塞控制=流量控制
    
    - 错因：都像“调速”
        
    - 自检：看变量：接收端通告的是rwnd；网络侧调的是cwnd
        
- 错误结论：四次挥手一定是4个报文，永远不能合并
    
    - 错因：忽略ACK可捎带
        
    - 自检：被动端“ACK+FIN能否同段”取决于是否立刻close(是否有待发数据)
        

## 8) 迁移题型模型（可直接套用）

- 识别：
    
    - 出现SYN/FIN/ACK、TIME_WAIT、cwnd/ssthresh、重复ACK、RTO
        
- 步骤：
    
    1. 先判主题：连接管理 / 可靠传输 / 流控 / 拥塞
        
    2. 写关键变量：seq/ack、rwnd、cwnd、ssthresh
        
    3. 走流程：握手或挥手状态机；或拥塞控制分支(RTO vs 3DupACK)
        
    4. 最后补一句“为什么需要/代价是什么”（高分点）
        
- 验算/检查点：
    
    - ACK是否“对方seq+1”
        
    - 主动关闭端是否必经TIME_WAIT
        
    - 丢包类型是否分清：RTO(更严重) vs 3DupACK(还能收到ACK)
        

## 9) 知识网络（用于串题）

- 上游前置：
    
    - IP尽力而为、分段与MTU/MSS
        
- 下游应用：
    
    - HTTP/HTTPS、FTP、SMTP等都默认依赖TCP可靠性
        
- 横向对比：
    
    - 可靠传输：链路层ARQ vs 传输层TCP（作用范围不同）
        
    - 控制：流量控制(rwnd) vs 拥塞控制(cwnd)
        

需要我按 **“握手/挥手/可靠传输/流控/拥塞控制”** 各拆一张更细的卡片（每张只讲一件事，配典型真题问法）也可以。