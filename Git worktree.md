---
share: true
---
## 常用命令

### 创建工作树

这是最常用的操作。假设你正在 `main` 分支工作，突然要去修个紧急 Bug

```bash
# 在上一级目录创建一个名为 hotfix 的文件夹，并在其中检出 hotfix-branch 分支
git worktree add ../hotfix-folder hotfix-branch

# 如果想基于远程分支创建一个新分支并开启工作树
git worktree add ../new-feature -b feature-v2 origin/feature-v2
```

### 查看状态

输出会显示每个工作树所在的绝对路径、对应的提交哈希以及分支名

```bash
git worktree list
```

### 删除工作树

当你完成了紧急修复，不再需要那个目录时

```bash
# 推荐做法：这会同步清理 git 内部的状态记录并删除目录
git worktree remove ../hotfix-folder
```

**注意**：如果你直接在文件管理器里删除了文件夹，Git 的记录里还会残留该项。需要手动清理这些“幽灵”记录。

```bash
git worktree prune
```

### 避坑指南与贴士

- **不能在不同工作树同时检出同一分支**：Git 不允许两个地方同时修改同一个分支，这会造成 HEAD 指针混乱。如果你非要这么干，得先在另一个工作树切换走。
    
- **共享 .git 目录**：所有的工作树都共享同一个 `.git` 文件夹（位于主工作树）。这意味着你在任何一个工作树里 `fetch` 了代码，其他工作树都能立即看到。
    
- **路径建议**：建议将工作树目录放在主仓库目录之外（例如 `../temp-work`），这样可以避免主仓库的 `.gitignore` 或构建脚本扫描到这些额外的文件夹。