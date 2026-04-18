# WSL Ubuntu (cc-switch) 安装与乱码修复指南



## 一、 环境确认

在 WSL 终端中确认你的 CPU 架构，以确保下载正确的安装包：

```bash
uname -m
```

- 返回 `x86_64`：选择 **x86_64** 版本（绝大多数电脑）。
- 返回 `aarch64` 或 `arm64`：选择 **arm64** 版本。

------

## 二、 快速下载与安装

请在 WSL 终端中依次执行以下命令：

### 1. 下载安装包 (以 x86_64 为例)

Bash

```bash
wget https://github.com/farion1231/cc-switch/releases/download/v3.13.0/CC-Switch-v3.13.0-Linux-x86_64.deb
```

### 2. 执行安装

使用 `apt` 安装本地包，它会自动处理所需的依赖项：

Bash

```bash
sudo apt update
sudo apt install ./CC-Switch-v3.13.0-Linux-x86_64.deb -y
```

------

## 三、 修复中文乱码问题

由于 WSL 默认环境精简，缺少中文字体和语言包，需按以下步骤修复：

### 1. 安装中文字体

安装文泉驿微米黑字体以支持图形界面显示：

Bash

```bash
sudo apt update
sudo apt install fonts-wqy-microhei fonts-wqy-zenhei xfonts-intl-chinese -y
```

### 2. 配置中文语言环境

安装语言包并生成中文区域设置：

Bash

```bash
sudo apt install language-pack-zh-hans -y
sudo locale-gen zh_CN.UTF-8
```

### 3. 设置环境变量 (永久生效)

将语言变量写入配置文件，使系统默认使用 UTF-8 中文编码：

Bash

```bash
echo 'export LANG=zh_CN.UTF-8' >> ~/.bashrc
echo 'export LANGUAGE=zh_CN:zh' >> ~/.bashrc
echo 'export LC_ALL=zh_CN.UTF-8' >> ~/.bashrc
source ~/.bashrc
```

------

## 四、 运行与注意事项

- **启动软件**：在终端直接输入 `cc-switch` 即可。
- **图形界面支持**：
  - **Windows 11 / Windows 10 (21H2+)**：支持 WSLg，窗口会自动弹出。
  - **旧版本**：若无法打开界面，请检查是否安装并运行了 X Server（如 VcXsrv）。
- **权限说明**：若软件涉及修改 `hosts` 文件，请注意 WSL 的 `/etc/hosts` 默认受 Windows 管理。若修改无效，需在 `/etc/wsl.conf` 中设置 `generateHosts = false`。