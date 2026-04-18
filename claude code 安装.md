# Claude Code安装

> 官网：https://claude.com/product/claude-code



### 使用 npm 卸载（最常见方式）

如果你是通过 `npm` 安装的，

```bash
npm install -g @anthropic-ai/claude-code
```

请运行以下命令卸载：

```bash
npm uninstall -g @anthropic-ai/claude-code
```

### 安装

#### windows

```bash
irm https://claude.ai/install.ps1 | iex
```

安装成功需要添加路径到配置文件

```bash
# 1. 定义需要添加的路径
$newPath = "C:\Users\luis\.local\bin"

# 2. 获取当前用户的 PATH 变量
$oldPath = [Environment]::GetEnvironmentVariable("Path", "User")

# 3. 检查路径是否已存在，若不存在则追加
if ($oldPath -notlike "*$newPath*") {
    $updatedPath = "$oldPath;$newPath"
    [Environment]::SetEnvironmentVariable("Path", $updatedPath, "User")
    Write-Host "✅ 路径已成功添加到 User PATH。" -ForegroundColor Green
    Write-Host "请重启 PowerShell 窗口以使配置生效。" -ForegroundColor Cyan
} else {
    Write-Host "ℹ️ 路径已存在，无需重复添加。" -ForegroundColor Yellow
}
```

#### wsl

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

安装成功需要添加路径到配置文件

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

#### mac

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

安装成功需要添加路径到配置文件

```bash
# 将路径写入 .zshrc 配置文件
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc

# 立即在当前窗口生效
source ~/.zshrc
```



### 验证与生效

运行完上述命令后，请**关闭并重新打开**一个新的 PowerShell 窗口，执行以下命令验证

```bash
claude --version
```