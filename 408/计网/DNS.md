---
tags: [408, 计网, 应用层, DNS]
aliases: [域名系统]
---

## DNS 一页通
- 核心：分层授权 + 递归解析 + 迭代查询 + 缓存(TTL)
- 角色：Stub / 递归DNS / 根 / TLD / 权威
- 必背流程：Cache命中? 否→根→TLD→权威；遇CNAME继续
- 高频考点：递归vs迭代、TTL与查询次数、CNAME链、各服务器职责
- 易错：把递归DNS当权威；以为每次都问根；CNAME当最终IP