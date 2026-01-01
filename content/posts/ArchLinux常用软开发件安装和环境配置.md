---
date: '2026-01-01T10:50:02+08:00'
draft: false
ShowToc: true
categories: 
- Linux
tags: 
- arch linux
- 教程
title: 'ArchLinux常用软开发件安装和环境配置'
---

##  安装JetBrain-toolbox

使用aur安装即可，安装后可以直接在里面下载各种IDE：

```bash
yay -S jetbrains-toolbox
```

## 配置JDK环境

以JDK21为例子，第一步通过官方仓库安装open-jdk-21

```bash
sudo pacman -S jdk21-openjdk
```

第二步配置环境变量，直接使用archlinux-java配置

```bash
# 检查已经安装的版本
archlinux-java status
# 设置默认版本
sudo archlinux-java set java-21-openjdk
# 新开一个终端验证
java -version
javac -version
```

## 配置Maven环境

Arch官方仓库安装：

```bash
sudo pacman -S maven
```

准备用户配置文件

```bash
mkdir -p ~/.m2
cp /usr/share/java/maven/conf/settings.xml ~/.m2/settings.xml
```

修改配置文件中的镜像源，一般不用修改本地仓库配置，默认就在`~/.m2/respositry`

```bash
nano ~/.m2/settings.xml
```

打开后搜索mirrors修改为阿里云镜像：

```xml
<mirrors>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云公共仓库</name>
        <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
</mirrors>

```

## 安装MiniConda

使用AUR安装：

```bash
yay -S miniconda3
```

初始化Conda，安装完成后，你需要让你的 Shell（如 bash 或 zsh）识别 `conda` 命令。激活基础环境：

```bash
source /opt/miniconda3/bin/activate
```

使用`conda init`写入配置文件。创建并使用制指定环境：

```bash
conda create -n py310 python=3.10
conda activate py310
```

默认情况下，打开终端会自动加载base环境，可以通过以下命令取消：

```bash
conda config --set auto_activate_base false
```

## 安装Conda驱动

博主的GPU是RTX5070，以这个为例，其他的型号可以自行搜索。这里英伟达驱动选择dkms版本：

```bash
# 1. 更新系统（防止部分更新导致依赖破损）
sudo pacman -Syu

# 2. 安装驱动、工具和系统级 CUDA 编译器
# 注意：如果是极新硬件，建议安装 open-gpu-kernel 模块或者专有驱动，这里推荐稳妥的专有驱动路径
sudo pacman -S nvidia-dkms nvidia-utils opencl-nvidia lib32-nvidia-utils cuda
```

- `nvidia-dkms`: 核心驱动模块。
- `nvidia-utils`: 用户空间工具（包括 `nvidia-smi`）。
- `lib32-nvidia-utils`: 32位支持（运行 Steam 游戏必装，需要在 `/etc/pacman.conf` 中开启 `[multilib]` 仓库）。
- `cuda`: 系统级的 CUDA 编译器（nvcc），某些源码编译安装 Python 库时需要。
