## Commit 是什么（本质）

**commit（提交）**是 Git 里一个“版本点/快照点”：

> 把**暂存区**里的内容封存成一次不可变的历史记录，写进本地仓库 `.git`。

你可以把它理解为：**给项目拍了一张“可回到过去”的照片**。

关键结论（一定要记住）：

- ✅ `commit` **只发生在本地**（不需要联网）
    
- ✅ `commit` 提交的是 **暂存区**（不是工作区）
    
- ✅ 每个 commit 都有一个 **唯一 ID（哈希）**，可定位可回滚
    

---

## Commit 里到底包含什么信息

一次 commit 通常包含：

- **快照内容**：当时项目的文件状态（由暂存区决定）
    
- **元信息**：作者、时间
    
- **提交说明**：commit message
    
- **父提交（parent）**：指向上一版，形成历史链（merge commit 可能有多个父）
    

查看某次提交：

```bash
git show <commit_id>
```

查看历史：

```bash
git log --oneline --graph --decorate
```

---

## Commit 是怎么产生的（从工作区到仓库）

你改文件后，典型流程是：

1. 工作区修改
    
2. `git add` 进入暂存区
    
3. `git commit` 写入仓库历史
    

```bash
git add -A
git commit -m "feat: add login"
```

### 你必须理解的区别

- `git add`：决定“这次要提交哪些改动”
    
- `git commit`：把这些改动“归档为一个历史节点”
    

---

## Commit message 怎么写（写给未来的自己和队友）

### 1) 好 message 的最小模板

- **做了什么**（动词开头）
    
- **影响范围/模块**
    
- **必要时说明原因**
    

示例：

- `net: add TCP congestion control notes`
    
- `fix: correct typo in README`
    
- `refactor: reorganize src structure`
    
- `feat: implement user login`
    

### 2) 推荐规范（轻量版 Conventional Commits）

```
type(scope): summary
```

常用 type：

- `feat` 新功能
    
- `fix` 修 bug
    
- `docs` 文档
    
- `refactor` 重构（不改变功能）
    
- `test` 测试
    
- `chore` 杂项（依赖、脚本等）
    

---

## 你经常会遇到的 4 个 commit 相关问题

### 1) 我 commit 了，GitHub 上怎么没变？

因为 `commit` 只在本地，要同步到远端还得：

```bash
git push
```

---

### 2) 我修改了文件但 commit 没包含它？

因为你可能没 add 它。commit 只打包暂存区。

提交前用这两句核对：

```bash
git diff           # 工作区还有什么没 add
git diff --staged  # 暂存区将提交什么
```

---

### 3) 我写错了提交信息 / 少提交了文件，怎么补救？

#### 方案 A：修改最近一次提交（还没 push 时最常用）

```bash
git add -A
git commit --amend
```

- 你可以补文件、改 message
    
- 会“重写”最近一次 commit（所以 **push 之后慎用**）
    

---

### 4) 我想撤销某次 commit，怎么办？

看你要“改历史”还是“不改历史”。

#### 推荐：revert（安全，不改历史）

```bash
git revert <commit_id>
```

它会生成一个新 commit 来反向抵消那次提交。

#### 强力：reset（改历史，慎用，尤其是已 push）

```bash
git reset --hard <commit_id>
```

---

## 进阶：一次 commit 应该多大？

**最佳实践：小步、可解释、可回滚。**

- ✅ 一个 commit 做“一件事”
    
- ✅ 能用一句话说明
    
- ✅ 出问题时能单独 revert 这一个 commit
    

反例：

- ❌ “乱七八糟一堆改动”塞进一个 commit（以后很难回溯）
    

---

## 进阶：为什么 Git 的 commit 这么强

因为 commit 是“历史链”的节点，带来三大能力：

1. **时间旅行**：回到任意节点
    
2. **对比**：任意两个节点 diff
    
3. **分支协作**：不同分支在不同节点前进，最后合并
    

---

## 提交前 10 秒检查（强烈建议养成习惯）

```bash
git status
git diff --staged
```

确认：

- 暂存区包含你想提交的内容
    
- 工作区没有你“误以为会提交”的东西
    
---

