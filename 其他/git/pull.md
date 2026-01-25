## pull 是什么（一句话抓本质）

**`git pull` 的作用：把远端分支的新提交下载到本地，并把这些更新合并到你当前分支上。**

更精确地说：

> **`git pull` = `git fetch`（下载） + `git merge`（合并）**  
> （默认如此；也可以配置成 fetch + rebase）

---

# 1) pull 到底在干什么：两步走

当你在 `main` 上执行：

```bash
git pull
```

Git 实际做的是：

1. **fetch**：从远端下载最新提交，更新你本地的远端跟踪分支，比如 `origin/main`
    
2. **merge**：把 `origin/main` 合并进你当前的 `main`
    

所以 pull 的核心是：**先拿到远端变化，再把变化应用到你的当前工作分支**。

---

# 2) 为什么 pull 前要先关心工作区是否干净

如果你本地工作区有未提交改动，pull 可能会：

- 合并失败、出现冲突
    
- 或者直接拒绝（为了保护你本地改动）
    

建议习惯：

```bash
git status
```

若你有改动，三选一：

### 方案 A：先提交（最稳）

```bash
git add -A
git commit -m "wip: save"
git pull
```

### 方案 B：暂存起来（stash，适合不想提交半成品）

```bash
git stash
git pull
git stash pop
```

### 方案 C：丢弃改动（危险）

```bash
git restore .
git pull
```

---

# 3) pull 后可能出现三种情况

## 情况 1：Already up to date

远端没新提交，本地无需更新。

## 情况 2：Fast-forward（快进）

你本地没改、远端领先 → 直接把本地分支指针“快进”到最新提交，干净无冲突。

## 情况 3：Merge commit / 冲突

你本地也有提交，远端也有提交 → 需要合并，有可能出现冲突。

冲突处理流程（和 merge 一样）：

1. 打开冲突文件解决
    
2. `git add` 标记已解决
    
3. `git commit` 完成合并
    

---

# 4) pull vs fetch：最关键区别

|命令|做什么|会不会改你当前分支代码？|适合场景|
|---|---|---|---|
|`git fetch`|只下载更新到 `origin/*`|❌ 不会|想先看看远端改了啥|
|`git pull`|下载 + 合并到当前分支|✅ 会|想直接把远端更新合进来|

所以更稳的“专业姿势”是：

```bash
git fetch
git log --oneline --graph --decorate --all
# 确认无误后再：
git merge origin/main
```

但日常单人/小团队，`git pull` 够用。

---

# 5) pull --rebase：让历史更直（常见配置）

默认 pull 会 merge，可能产生很多 merge commit。  
如果你希望“拉取时用 rebase”，可以：

### 临时用一次

```bash
git pull --rebase
```

### 设置默认（可选）

```bash
git config --global pull.rebase true
```

效果：  
你的本地提交会“搬家”到最新远端提交之后，历史更线性。

⚠️ 注意：这会触发 rebase 的那套规则（可能需要解决冲突并 `rebase --continue`）。

---

# 6) pull 经常失败的原因与解决

## 6.1 错误：本地有未提交改动会被覆盖

解决：提交 / stash / 丢弃（见第 2 节）

## 6.2 错误：有冲突

解决：按冲突提示处理，然后 `add` + `commit`（或 rebase 模式下 `rebase --continue`）

## 6.3 错误：你没设置 upstream（追踪分支）

表现：Git 不知道 pull 哪个远端分支。  
解决：给当前分支设置 upstream：

```bash
git branch -u origin/main
# 或第一次 push 时用 -u：git push -u origin main
```

---

# 7) VS Code 里的 pull 对应什么

- **Pull**：等价 `git pull`
    
- **Sync Changes**：通常是 pull + push（按设置可能不同）
    
- 你也可以先 Fetch 再 Pull（更稳）
    

---

# 8) 一句话总结 pull

> **pull 是“把远端更新拿下来并合并到当前分支”，等价于 fetch + merge；想历史更直可用 pull --rebase，但要理解 rebase 的规则。**

---

