---
epoch: 1779265643753
modelKey: Qwen3.5|3rd party (openai-format)
tags:
  - copilot-conversation
topic: LaTeX IP Subnet Formula Fix
---

**user**: $$\left.\begin{aligned}

&\text{208.115.21.00}000000 \

&\cdots\cdots \

&\text{208.115.21.00}111111

\end{aligned}\right} \begin{aligned}

&\text{地址块 208.115.21.0/26，子网掩码 255.255.255.192} \

&\text{可分配地址 62 个，分配给部门 1。}

\end{aligned}$$

$$\left.\begin{aligned}

&\text{208.115.21.010}00000 \

&\cdots\cdots \

&\text{208.115.21.010}11111

\end{aligned}\right} \begin{aligned}

&\text{地址块 208.115.21.64/27，子网掩码 255.255.255.224} \

&\text{可分配地址 30 个，分配给部门 2。}

\end{aligned}$$

$\vdots$

$$\left.\begin{aligned}

&\text{208.115.21.01100}000 \

&\cdots\cdots \

&\text{208.115.21.01100}111

\end{aligned}\right} \begin{aligned}

&\text{地址块 208.115.21.96/29，子网掩码 255.255.255.248} \

&\text{可分配地址 6 个，分配给部门 3。}

\end{aligned}$$

$$\left.\begin{aligned}

&\text{208.115.21.01101}000 \

&\cdots\cdots \

&\text{208.115.21.11111}111

\end{aligned}\right} \text{剩余 256 - 64 - 32 - 8 = 152 个地址，留作备用。}$$

这段公式无法正常渲染
[Context: Notes: 408/计网/Chapter 4 ：网络层/4.2 IPv4地址/IPv4地址.md]
[Timestamp: 2026/05/20 16:27:23]

**ai**: 

您指出的问题很准确！这些 LaTeX 公式确实存在语法错误，导致无法正常渲染。主要问题是：

1. `\left.\begin{aligned}` 和 `\right}` 搭配不当
2. 大括号 `{` 没有正确闭合
3. 应该使用 `\left\{` 或 `\begin{cases}` 环境

下面是修复后的版本，可以直接替换原内容：

---

### 修复后的公式（可直接复制替换）

```latex
\begin{cases}
\begin{aligned}
&\text{208.115.21.00}000000 \\
&\cdots\cdots \\
&\text{208.115.21.00}111111
\end{aligned}
\end{cases}
\begin{aligned}
&\text{地址块 208.115.21.0/26，子网掩码 255.255.255.192} \\
&\text{可分配地址 62 个，分配给部门 1。}
\end{aligned}
```

```latex
\begin{cases}
\begin{aligned}
&\text{208.115.21.010}00000 \\
&\cdots\cdots \\
&\text{208.115.21.010}11111
\end{aligned}
\end{cases}
\begin{aligned}
&\text{地址块 208.115.21.64/27，子网掩码 255.255.255.224} \\
&\text{可分配地址 30 个，分配给部门 2。}
\end{aligned}
```

```latex
\begin{cases}
\begin{aligned}
&\text{208.115.21.01100}000 \\
&\cdots\cdots \\
&\text{208.115.21.01100}111
\end{aligned}
\end{cases}
\begin{aligned}
&\text{地址块 208.115.21.96/29，子网掩码 255.255.255.248} \\
&\text{可分配地址 6 个，分配给部门 3。}
\end{aligned}
```

```latex
\begin{cases}
\begin{aligned}
&\text{208.115.21.01101}000 \\
&\cdots\cdots \\
&\text{208.115.21.11111}111
\end{aligned}
\end{cases}
\text{剩余 256 - 64 - 32 - 8 = 152 个地址，留作备用。}
```

---

### 更简洁的替代方案（推荐）

如果您希望公式更简洁，也可以使用以下写法：

```latex
\begin{aligned}
&\text{208.115.21.0/26: } 208.115.21.00000000 \sim 208.115.21.00111111 \\
&\quad \text{子网掩码 255.255.255.192，可分配 62 个地址，分配给部门 1} \\
\\
&\text{208.115.21.64/27: } 208.115.21.01000000 \sim 208.115.21.01011111 \\
&\quad \text{子网掩码 255.255.255.224，可分配 30 个地址，分配给部门 2} \\
\\
&\text{208.115.21.96/29: } 208.115.21.01100000 \sim 208.115.21.01100111 \\
&\quad \text{子网掩码 255.255.255.248，可分配 6 个地址，分配给部门 3} \\
\\
&\text{剩余地址：256 - 64 - 32 - 8 = 152 个，留作备用}
\end{aligned}
```

---

### 修复说明

| 原写法 | 问题 | 修复方式 |
|--------|------|----------|
| `\left.\begin{aligned}` | 左括号未闭合 | 改用 `\begin{cases}` |
| `\right}` | 右括号错误 | 改用 `\end{cases}` |
| 大括号 `{` 未闭合 | 语法错误 | 使用 `\left\{` 或 `\begin{cases}` |

---

**建议**：使用第二种"更简洁的替代方案"，因为：
1. 语法更简单，兼容性更好
2. 阅读更直观，便于复习
3. 在 Obsidian 中渲染更稳定

您可以选择其中一种方案替换原文中的公式部分。
[Timestamp: 2026/05/20 16:27:34]