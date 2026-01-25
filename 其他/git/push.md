## push 是什么（一句话抓本质）

**`git push` 的作用：把你本地仓库里新增的提交（commits）上传到远端仓库（比如 GitHub），让远端分支更新到你的最新状态。**

> commit 发生在本地；push 才把这些 commit “同步”到 GitHub。

---

# 1) push 在“本地 ↔ 远端”模型里的位置

你日常完整链路通常是：

1. 改文件（工作区）
    
2. `git add`（暂存区）
    
3. `git commit`（本地仓库产生历史节点）
    
4. `git push`（把本地新增历史上传到远端）
    

对应命令：

```bash
git add -A
git commit -m "feat: ..."
git push
```

---

# 2) push 到底在“推”什么？

push 推的不是“文件”，而是：

- 你本地分支上**新增的提交对象**
    
- 以及分支指针要移动到的新位置
    

远端接收后，就把远端分支（比如 `origin/main`）指向你推过去的最新 commit。

所以：

- ✅ push 很快：因为 Git 只传“远端没有的对象”
    
- ✅ push 让别人能 pull 到你提交的历史
    
- ❌ push 不会自动把你没 commit 的工作区改动上传（因为它们不在历史里）
    

---

# 3) push 的基本用法（新手先吃透这三种）

## 3.1 已建立追踪关系：直接 `git push`

如果你这个分支之前已经设置过 upstream（追踪远端分支），那么：

```bash
git push
```

就等价于：

> 把当前分支推到它所追踪的远端分支上

如何看追踪关系：

```bash
git branch -vv
```

---

## 3.2 第一次推一个新分支：`-u` 非常重要

比如你新建了分支 `feature/login`，第一次推送：

```bash
git push -u origin feature/login
```

- `origin`：远端名（通常默认就是 origin）
    
- `feature/login`：你要推送的本地分支
    
- `-u`：设置 upstream（之后就可以直接 `git push` / `git pull`）
    

---

## 3.3 推送 main（最常见）

```bash
git push origin main
```

如果你已经设置 upstream，就直接：

```bash
git push
```

---

# 4) push 经常失败的原因（以及正确处理方式）

## 4.1 被拒绝：non-fast-forward（远端比你新）

错误大意通常是：远端有你没有的提交，你不能直接覆盖。

解决步骤：

1. 先拉取并合并/变基：
    

```bash
git pull
```

2. 解决冲突（如果有）→ commit
    
3. 再推：
    

```bash
git push
```

> 更稳的习惯：**push 前先 pull**（尤其多设备/多人协作）

---

## 4.2 你 rebase 过：普通 push 会失败（因为历史被重写）

如果你对已推送的分支做了 rebase，commit id 变了，远端不接受普通 push。

更安全的强推方式：

```bash
git push --force-with-lease
```

解释：

- `--force-with-lease` 会先检查远端是否被别人更新过
    
- 如果远端被更新了，它会拒绝，避免你覆盖别人的提交
    

⚠️ 强推只在你确认“这个分支只有你在用”时做。

---

## 4.3 没权限/认证失败

常见于：

- HTTPS 需要 token（不能用密码）
    
- SSH key 没配好
    

排查：

```bash
git remote -v
ssh -T git@github.com
```

---

# 5) push 和分支/PR 的关系（GitHub 协作必懂）

- 你在本地创建分支并提交后，必须 **push 分支到远端**，GitHub 才能看到它
    
- 然后你在 GitHub 网页上发 **PR**（比如 `feature/login` → `main`）
    

典型流程：

```bash
git switch -c feature/login
# ... commit ...
git push -u origin feature/login
# 去 GitHub 发 PR
```

---

# 6) push 的几个实用小技巧

## 6.1 一次性推所有分支（不常用，慎用）

```bash
git push --all
```

## 6.2 同时推 tag（发布版本用）

```bash
git push --tags
```

## 6.3 推送删除远端分支

比如合并后清理远端 feature 分支：

```bash
git push origin --delete feature/login
```

---

# 7) VS Code 里 push 怎么对应

VS Code Source Control 面板里：

- **Push**：等价 `git push`
    
- **Sync Changes**：通常相当于 pull + push（视设置而定）
    
- 第一次 push 新分支也会提示你“Publish Branch”（等价 `git push -u origin <branch>`）
    

---

# 8) 一句话总结 push

> **push = 把本地 commit 历史同步到远端分支，供备份与协作使用；被拒绝先 pull，rebase 后用 --force-with-lease 但要慎重。**

---

如果你愿意，我可以再把与 push 最紧密的一块“拉下来讲清楚”：

- `git pull`（为什么它是 fetch + merge）
    
- `git fetch`（只下载不合并）  
    这样你就能彻底理解“为什么 push 会被拒绝，以及最正确的处理姿势”。