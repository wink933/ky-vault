
## rebase 是什么（先一句话抓本质）

**`git rebase` 的本质：把一串提交“搬家”到另一个基底（base）上，让历史变成更直的一条线。**

它做的不是“合并两条线”，而是：

> **把你的分支提交一个个“摘下来”，再按顺序“重新应用”到新的起点上**  
> 所以这些提交会变成“新的提交”（commit id 会变）。

---

# 1) rebase 为什么叫“变基”

假设你从 `main` 的 C 开了 `feature`：

```
A --- B --- C            (main)
              \
               D --- E    (feature)
```

这时 `main` 又往前走了两步：

```
A --- B --- C --- F --- G   (main)
              \
               D --- E      (feature)
```

你想让 `feature` 看起来像是从最新 `main(G)` 开始写的（历史更直），于是：

```bash
git switch feature
git rebase main
```

结果变成：

```
A --- B --- C --- F --- G --- D' --- E'   (feature)
```

注意：

- D、E 变成了 **D'、E'**（新的提交）
    
- `feature` 的历史变直了
    
- `main` 没动（rebase 发生在 feature 上）
    

---

# 2) rebase 和 merge 的核心区别（用一句话区分）

- **merge**：把两条历史“汇合”，保留分叉结构（可能产生 merge commit）
    
- **rebase**：把一条历史“搬到”另一条历史后面，形成线性历史（重写提交）
    

> rebase 更“整理历史”，merge 更“忠实记录发生过分叉”。

---

# 3) rebase 什么时候用（非常实用的场景）

## 场景 A：让你的功能分支跟上最新 main（最常用）

你在 `feature` 开发时，`main` 已更新很多次，想把这些更新“整洁地”带到你的分支：

```bash
git switch feature
git fetch origin
git rebase origin/main
```

优点：

- 历史线性、干净
    
- 之后提 PR 更好 review（少 merge 噪音）
    

---

## 场景 B：合并前整理提交（交作业/开源特别常见）

你在 feature 上提交了很多“wip、fix、oops”：

- `wip: ...`
    
- `fix typo`
    
- `try again`
    

你想合并前压缩成 2~3 个高质量 commit：  
用 **交互式 rebase**（后面第 7 节讲）。

---

# 4) rebase 的最大风险：它会“重写历史”

为什么危险？

- rebase 会让你的提交变成新的提交（ID 变了）
    
- 如果这些提交已经被别人基于它继续开发，或者你已经 push 给团队使用  
    → 你 rebase 后，历史就“分裂”，别人会很痛苦
    

**黄金规则（新手务必记住）：**

> ✅ **只在“自己独占”的分支上 rebase**  
> ❌ 不要在“已经共享/多人使用”的分支上 rebase

---

# 5) 最常用 rebase 命令（你先把这几条吃透）

## 5.1 更新 feature 到最新 main

```bash
git switch feature
git fetch origin
git rebase origin/main
```

## 5.2 如果 rebase 过程中出现冲突：怎么做

流程和 merge 类似，但命令不同：

1. 解决冲突（编辑文件）
    
2. 标记已解决：
    

```bash
git add <file>
```

3. 继续 rebase：
    

```bash
git rebase --continue
```

如果你想放弃这次 rebase：

```bash
git rebase --abort
```

如果你想跳过当前这个提交（不推荐，除非你明确知道后果）：

```bash
git rebase --skip
```

---

# 6) rebase 后 push 可能会失败：为什么？怎么推？

因为 rebase 重写了提交历史，你本地分支历史和远端分支不再“同一条线”，普通 `git push` 会被拒绝。

此时你可能需要“强制推送”，但要用更安全的方式：

```bash
git push --force-with-lease
```

解释：

- `--force-with-lease` 会检查远端是不是被别人更新过  
    如果远端比你想象的更新，它会拒绝（避免覆盖别人的提交）
    
- 比 `--force` 安全得多
    

⚠️ 再强调一次：只有在**你确认这个分支是你一个人在用**时才这么做。

---

# 7) 交互式 rebase（interactive rebase）：整理提交的神器

你想把最近 5 个提交整理一下：

```bash
git rebase -i HEAD~5
```

会打开一个编辑界面，列出类似：

```
pick a1b2c3 feat: add login UI
pick d4e5f6 wip: debug
pick 111222 fix: typo
pick 333444 wip: try again
pick 555666 feat: add API
```

你可以改成：

- `pick`：保留
    
- `reword`：改提交信息
    
- `squash` / `s`：把这个提交合并到上一个提交
    
- `drop`：丢弃这个提交
    

例如把 wip/fix 合并进前一个：

```
pick a1b2c3 feat: add login UI
s d4e5f6 wip: debug
s 111222 fix: typo
s 333444 wip: try again
pick 555666 feat: add API
```

结果：历史更干净，PR 更好看。

---

# 8) rebase vs merge：怎么选（给你一个可执行的决策表）

|目标|选什么|理由|
|---|---|---|
|团队协作，避免改历史|merge|不重写历史，最安全|
|个人分支想保持线性历史|rebase|历史干净，review 友好|
|分支已经 push 给别人用|merge|rebase 会让别人很难同步|
|PR 前想整理 wip 提交|rebase -i|压缩/改写提交信息|

**新手建议默认策略：**

- 平时协作：以 **merge** 为主
    
- 个人分支合并前：可以 **rebase 到最新 main + 交互式整理**
    

---

# 9) 一句话总结 rebase

> **rebase 是“搬家重放提交”，让历史线性；强大但会重写历史，因此只在自己独占的分支上使用。**

---

如果你想把 ==rebase== 真正搞懂，我可以给你一套“可复现练习”：

1. 造一个 `main` + `feature` 分叉
    
2. 在 main 增加提交
    
3. 在 feature rebase 到 main
    
4. 人为制造冲突，练习 `--continue/--abort`
    
5. 最后演示 `--force-with-lease` 的安全推送

