# WSL指南

## 安装

## 修复环境变量path【PowerShell 管理员模式】

```bash
# 1. 强制提升当前会话的路径（确保接下来的命令能跑通）
$env:Path = "C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Windows\System32\OpenSSH\;" + $env:Path

# 2. 重新定义并尝试永久写入系统变量
$essentialPaths = @(
    "C:\Windows\system32",
    "C:\Windows",
    "C:\Windows\System32\Wbem",
    "C:\Windows\System32\WindowsPowerShell\v1.0\",
    "C:\Windows\System32\OpenSSH\"
)

try {
    # 获取原始路径并去重补全
    $regPath = [Microsoft.Win32.Registry]::LocalMachine.OpenSubKey("System\CurrentControlSet\Control\Session Manager\Environment").GetValue("Path", "", [Microsoft.Win32.RegistryValueOptions]::DoNotExpandEnvironmentNames)
    $currentPaths = $regPath -split ";" | Where-Object { !([string]::IsNullOrWhiteSpace($_)) }
    
    $finalPaths = $essentialPaths.Clone()
    foreach($p in $currentPaths) {
        if ($essentialPaths -notcontains $p) { $finalPaths += $p }
    }
    
    $newPathString = $finalPaths -join ";"

    # 写入注册表
    [Environment]::SetEnvironmentVariable("Path", $newPathString, "Machine")
    Write-Host "✅ 成功！系统 PATH 已修复。" -ForegroundColor Green
} catch {
    Write-Warning "❌ 写入失败：请确认你是否开启了『管理员权限』的 PowerShell 窗口。"
}

# 3. 广播通知（修正了之前的语法错误）
$definition = '[DllImport("user32.dll", SetLastError = true, CharSet = CharSet.Auto)] public static extern IntPtr SendMessageTimeout(IntPtr hWnd, uint Msg, UIntPtr wParam, string lParam, uint fuFlags, uint uTimeout, out UIntPtr lpdwResult);'
$win32 = Add-Type -MemberDefinition $definition -Name "Win32Utils" -Namespace "Native" -PassThru
$outResult = [UIntPtr]::Zero
[void]$win32::SendMessageTimeout([IntPtr]0xffff, 0x001A, [UIntPtr]::Zero, "Environment", 0x02, 1000, [ref]$outResult)

Write-Host "🚀 配置已广播，请新开一个窗口测试 'wsl' 命令。" -ForegroundColor Cyan
```



## **【PowerShell 管理员模式】权限**下执行

```bash
# 确保虚拟化平台已开启
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform -NoRestart
# 确保 WSL 功能已开启
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux -NoRestart
```

**如果这两行提示需要重启（Restart Needed: True），请务必重启后再试。**

设置 WSL 2 为默认版本

```bash
wsl --set-default-version 2
```

查看可用的发行版列表

```bash
wsl --list --online
```

指定安装某个版本

```bash
wsl --install -d Ubuntu-22.04
```



## 安装命令提示403

报错 **403** 通常是因为 `wsl --install` 默认会去 GitHub 或微软 CDN 下载最新的内核包，而你的网络环境可能被拦截了。

作为架构师，我建议跳过自动安装脚本，采用**分步激活指令**，这能避开 403 错误并强制开启底层组件。

------

### 第一步：强制开启底层功能 (无需联网)

请以 **管理员身份** 打开 PowerShell，依次执行以下两条指令。这两条指令调用的是本地系统组件，不会产生 403 错误：

【PowerShell 管理员模式】

```bash
# 1. 开启 WSL 基础功能
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# 2. 开启虚拟机平台 (WSL 2 必需)
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

**⚠️ 重要：执行完这两行后，请立即重启电脑。** 只有重启后，Windows 才会真正加载 WSL 的底层驱动。





## WSL2 代理配置与镜像模式

### 解决方案

#### 启用镜像模式 (Recommended)

通过在 Windows 用户目录下创建 `.wslconfig` 文件，强制 WSL2 使用镜像模式。

1. 修改配置文件

在 Windows 中按下 `Win + R`，输入 `%USERPROFILE%` 并回车。在该目录下新建或修改 `.wslconfig` 文件，内容如下：

```bash
# 文件名: .wslconfig
[wsl2]
# 启用镜像网络模式，使 WSL 与 Windows 共享 IP 地址
networkingMode=mirrored

# 允许 WSL 访问宿主机的代理
dnsTunneling=true
firewall=true
autoProxy=true
```

2. 重启 WSL 服务

打开 PowerShell（管理员权限），执行以下命令让配置生效：

```bash
wsl --shutdown
```

重新启动你的 WSL 分发版，此时 WSL 内部将直接支持 `localhost` 代理。

#### 替代方案：手动配置代理（针对无法开启镜像模式的情况）

在 WSL 终端中执行以下命令获取宿主机 IP：

Bash

```
cat /etc/resolv.conf | grep nameserver | awk '{print $2}'
```

#### 2. 配置环境变量

在 `~/.bashrc` 或 `~/.zshrc` 中添加以下逻辑，自动捕获 IP 并设置代理（假设代理端口为 7890）：

Bash

```
# 获取宿主机 IP
export hostip=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
# 设置代理地址
export http_proxy="http://${hostip}:7897"
export https_proxy="http://${hostip}:7897"
```

