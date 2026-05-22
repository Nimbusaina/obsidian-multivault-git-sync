[English](README.md) | [中文](README.zh-CN.md)

# MultiVault Git Sync

一个面向 Obsidian 的保守型 Git 同步插件。

适用于：

```text id="14m4s5"
一个 Git repository
↳ 管理多个 Obsidian Vault
↳ 多设备同步
↳ 手动 / 半自动同步
```

---

# 1. 插件用途

该插件用于解决：

```text id="c0bh7p"
多个 Obsidian Vault
共享同一个 Git 仓库
```

时产生的一系列同步问题。

典型结构：

```text id="3pgmwb"
Obsidian library/
├── My notebook/
├── My blog/
├── File vault/
├── HWK-Obsidian/
└── .git/
```

其中：

* 每个子目录是独立 Vault
* 整个目录由一个 Git 仓库管理

插件提供：

* 多 Vault Git 同步
* Repository 级锁机制
* 保守型冲突处理
* 大文件保护
* 多设备同步支持

---

# 2. 适用环境

推荐环境：

| 组件            | 推荐                       |
| ------------- | ------------------------ |
| 系统            | Windows 10 / 11          |
| Obsidian      | Desktop 版本               |
| Git           | 已安装并加入 PATH              |
| GitHub        | HTTPS 仓库                 |
| Repository 类型 | 一个 repository 管理多个 vault |
| 同步方式          | 手动同步优先                   |

推荐目录结构：

```text id="wvm7fa"
F:\Obsidian library\
├── VaultA\
├── VaultB\
├── VaultC\
└── .git\
```

不同设备：

```text id="5vkquq"
不要求相同盘符
不要求相同绝对路径
```

例如：

```text id="v5yrjn"
设备 A:
F:\Obsidian library\

设备 B:
E:\Obsidian library\
```

完全支持。

---

# 3. 与 Obsidian Git 插件的区别

## Obsidian Git

默认设计：

```text id="jlwm4z"
一个 Vault
↳ 一个 Git Repository
```

特点：

* 自动化程度较高
* 自动 pull / push
* 面向单 Vault 使用
* 插件设置随 Vault 同步
* 对多 Vault repository 支持较弱

在多 Vault repository 中容易出现：

* 多 Vault 同时执行 Git
* index.lock 冲突
* 插件设置同步冲突
* workspace.json 冲突
* rebase 中断
* 插件自同步冲突

---

## MultiVault Git Sync

默认设计：

```text id="jlwm6n"
一个 Repository
↳ 多个 Vault
↳ 多个设备
```

特点：

* 保守型同步策略
* Repository 级锁
* 手动同步优先
* Rebase 状态检测
* 大文件检测
* Pull 前本地备份
* Stop-on-conflict 策略

插件刻意避免：

* 自动 Markdown merge
* 自动冲突覆盖
* 完全无人值守后台同步

---

# 4. 插件版本

## v0.1.x

初始版本。

功能：

* 多 Vault repository 同步
* Repository 锁机制
* pull / commit / push 流程
* 启动同步
* 定时同步

实际使用中发现的问题：

* 插件自身冲突
* workspace.json 冲突
* rebase 中断
* conflict backup 被 Git 跟踪
* 大 PDF 上传失败
* 多设备插件设置冲突

---

## v0.2.0

稳定化版本。

新增：

---

### 1. Conflict Backup

pull 前：

```text id="jlwm2p"
本地已修改文件
→ 自动复制到:
.obsidian-git-conflicts/
```

用于避免本地内容丢失。

---

### 2. Rebase / Merge Detection

插件检测：

```text id="jlwm8m"
rebase-merge
MERGE_HEAD
CHERRY_PICK_HEAD
```

若 repository 状态异常：

```text id="jlwm9u"
立即停止同步
```

---

### 3. Large File Protection

默认：

```text id="jlwm4o"
>50 MB → warning
>100 MB → 阻止同步
```

适配 GitHub 文件限制。

---

### 4. Conflict Commands

新增命令：

```text id="jlwm2z"
Show Git conflict status
Abort current Git rebase or merge
Scan large files before Git commit
```

---

### 5. Repository Lock

全局锁文件：

```text id="jlwm0v"
.git/obsidian-multivault-sync.lock
```

避免多个 Vault 同时同步。

---

# 5. 安装流程

## 5.1 安装 Git

下载：

[Git for Windows](https://git-scm.com/download/win?utm_source=chatgpt.com)

检查：

```powershell id="jlwm1m"
git --version
```

---

## 5.2 安装 GitHub CLI

下载：

[GitHub CLI](https://cli.github.com?utm_source=chatgpt.com)

登录：

```powershell id="jlwm6x"
gh auth login
```

---

## 5.3 Clone Repository

例如：

```powershell id="jlwm5r"
cd F:\
git clone https://github.com/USERNAME/REPOSITORY.git "Obsidian library"
```

---

## 5.4 安装插件

将：

```text id="jlwm7w"
main.js
manifest.json
```

放入：

```text id="jlwm2j"
Vault/.obsidian/plugins/multivault-git-sync/
```

随后在 Obsidian 中启用插件。

---

# 6. Repository 配置

推荐 GitHub repository 设置：

| 设置         | 推荐      |
| ---------- | ------- |
| Visibility | Private |
| License    | 初期可不设置  |
| README     | 可选      |
| .gitignore | 必须      |

推荐 `.gitignore`：

```gitignore id="jlwm4k"
*.pdf

.obsidian-git-conflicts/

**/.obsidian/workspace.json
**/.obsidian/workspaces.json

**/.obsidian/plugins/multivault-git-sync/
```

---

# 7. 插件配置

## Repository Root

必须填写：

```text id="jlwm7m"
包含 .git 的目录
```

例如：

```text id="jlwm1q"
F:\Obsidian library
```

而不是：

```text id="jlwm6r"
F:\Obsidian library\My notebook
```

---

## 推荐同步设置

只建议：

```text id="jlwm9r"
一个 Vault
开启自动同步
```

推荐：

| 设置                 | 主 Vault | 其他 Vault |
| ------------------ | ------- | -------- |
| Sync on startup    | ON      | OFF      |
| Auto sync interval | 5~10 分钟 | 0        |

避免 Repository 锁竞争。

---

# 8. 推荐同步流程

## 设备 A

```text id="jlwm8w"
打开 Vault
→ Sync repository now
→ 编辑
→ Sync repository now
```

---

## 设备 B

```text id="jlwm2u"
打开 Vault
→ Sync repository now
→ 编辑
→ Sync repository now
```

避免：

```text id="jlwm3k"
两个设备同时编辑同一文件
```

---

# 9. 冲突处理

插件采用：

```text id="jlwm0j"
Stop-on-conflict
```

而不是自动 merge。

发生冲突时：

```text id="jlwm7x"
同步立即停止
```

本地内容仍可从：

```text id="jlwm5m"
.obsidian-git-conflicts/
```

恢复。

---

## 常见冲突类型

### 1. 同文件同时修改

例如：

```text id="jlwm4m"
a.md
```

在两个设备同时修改。

Git 会产生 conflict。

需人工处理。

---

### 2. modify/delete conflict

例如：

```text id="jlwm3d"
设备 A:
删除 a.md

设备 B:
修改 a.md
```

Git 要求人工决定。

---

### 3. 插件设置冲突

通过忽略：

```text id="jlwm1u"
multivault-git-sync/data.json
```

解决。

---

# 10. 大文件建议

GitHub 不适合作为大型教材 / 数据集仓库。

推荐：

| 内容       | 推荐                      |
| -------- | ----------------------- |
| Markdown | GitHub                  |
| 小图片      | GitHub                  |
| PDF      | OneDrive / NAS / Zotero |
| 大数据      | 外部存储                    |

避免同步：

```text id="jlwm6u"
100MB+
```

文件。

---

# 11. 当前限制

尚未实现：

* 自动 Markdown merge
* 可视化 diff UI
* 自动 conflict-copy
* Vault 级选择同步
* Git LFS 集成
* 移动端支持

---

# 12. 推荐使用哲学

插件优先考虑：

```text id="jlwm9m"
Repository 安全性
高于
最大自动化
```

同步应当：

* 可观察
* 可中断
* 可恢复
* 可人工控制

而不是完全无人值守。
