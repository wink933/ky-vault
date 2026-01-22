---

topic: "ARP"  
module: "网络层"  
input_type: "概念"  
exam_weight: "高"  
one_liner: "把“下一跳IP”落到“可封装帧的目的MAC”，让IPv4转发能在二层真正发出去。"  
why_needed:

- "以太网发送必须知道目的MAC；上层常只有目的IP/下一跳IP，缺少映射就无法封装与发送"
    
- "跨网段通信真正要解析的是“默认网关(下一跳)的MAC”，否则会错误地去找远端主机MAC"
    
- "用ARP缓存把广播发现的成本摊薄到后续多次转发，降低时延与广播压力"  
    keywords:
    
- "ARP缓存(动态/静态/老化)"
    
- "Request广播/Reply单播"
    
- "下一跳ARP(网关ARP)"
    
- "免费ARP(Gratuitous ARP)"
    
- "代理ARP(Proxy ARP)"  
    signals:
    
- "题干出现：同一网段/局域网/以太网/默认网关/下一跳/MAC未知/ARP表未命中"
    
- "问：是否广播/广播几次/谁回包/目的MAC怎么填/以太网类型0x0806"
    
- "给IP与掩码与路由表，问：解析谁的MAC、帧目的MAC是谁"
---