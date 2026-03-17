---
share: true
---

## 安装到指定目录
```bash
"Docker Desktop Installer.exe" install --installation-dir=E:\dev\docker\docker_desktop
```

## 迁移数据（避免C盘占满）

### 彻底关闭 Docker 和 WSL
```bash
wsl --shutdown
```

### 导出数据 (Export)
```bash
wsl --export docker-desktop "E:\dev\docker\docker_desktop.tar"
```

### 注销原有的 C 盘实例 (Unregister)

```bash
wsl --unregister docker-desktop
```

### 导入到 E 盘新路径 (Import)
格式：wsl --import <名称> <新安装位置> <备份文件路径> --version 2
```bash
wsl --import docker-desktop "E:\dev\docker\data" "E:\dev\docker\docker_desktop.tar" --version 2
```