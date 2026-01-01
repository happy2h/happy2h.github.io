---
date: '2025-12-29T22:21:27+08:00'
draft: false
ShowToc: true
categories: 
- Linux
tags: 
- arch linux
- 教程
title: '安装ArchLinux及必要配置'
---



# Arch Linux安装

## 使用archinstall脚本进行安装

如果你有一块磁盘单独安装Arch Linux操作系统，可以直接使用archinstall脚本进行安装，具体配置可以参考这篇[博客](https://zhuanlan.zhihu.com/p/25308291469)。这里提一下需要注意的点：

archinstall脚本由于国内网络原因执行可能会一直卡在`fetch arch linux packages database`，这个时候需要调整下配置。

测试能否 ping 通公网，先按`Ctrl + C`退出archinstall，然后输入：

```bash
ping -c 3 archlinux.org
```

如果报错`Temporary failure in name resolution`则DNS有问题；如果ping不通检查网络；如果可以ping通但是archinstall执行卡需要调整镜像。

**DNS问题：**

```bash
echo "nameserver 223.5.5.5" > /etc/resolv.conf
echo "nameserver 8.8.8.8" >> /etc/resolv.conf
```

**网络问题：**

执行`iwctl`检查网络是否连接，**注意wifi名称不要有中文**。进入后执行查看网络连接：

```bash
device list
station wlan0 show
```

**镜像源问题：**

手动更新镜像源列表

```bash
reflector --country China --latest 10 --sort rate --save /etc/pacman.d/mirrorlist
```

测试pacman，如果测试成功archinstall就可以执行。

```bash
pacman -Sy
```

**最后检查时间问题：**

```bash
timedatectl set-ntp true
timedatectl status
```

其他磁盘分区和引导的问题参考源blog就行，对了桌面一定要选Gnome，伟大无需多言。

## 自定义安装

如果只有一个磁盘但是需要双系统，推荐参考视频[链接](https://www.youtube.com/watch?v=1J_Z_pzzbMo)自已安装，其实我也没有尝试过。

# 必要软件安装

## 换源

```bash
sudo nano /etc/pacman.d/mirrorlist
Server = http://mirrors.jlu.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.aliyun.com/archlinux/$repo/os/$arch
Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.wsyu.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.jlu.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch
Server = https://mirrors.neusoft.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.hit.edu.cn/archlinux/$repo/os/$arch
Server = http://mirror.nyist.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.neusoft.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.cqu.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.wsyu.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.nju.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.cqu.edu.cn/archlinux/$repo/os/$arch
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.hust.edu.cn/archlinux/$repo/os/$arch
Server = https://mirror.nyist.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.bfsu.edu.cn/archlinux/$repo/os/$arch
```

添加非官方源`sudo nano /etc/pacman.conf`，把[extra]和[multilib]这个取消注释（删除#）,`ctrl + O`保存，`ctrl + X`退出。

## 常用字体

可以使用pacman安装`pacman -S noto-fonts-cjk`，其他常用字体：

```
adobe-source-han-sans-cn-fonts
adobe-source-han-serif-cn-fonts
noto-fonts-cjk
wqy-microhei
wqy-microhei-lite
wqy-bitmapfont
wqy-zenhei
ttf-arphic-ukai
ttf-arphic-uming
```

## 安装yay

流行的AUR助手，方便下载和管理软件。

1. 安装构建环境和Git

```bash
sudo pacman -S --needed base-devel git
```

**用途**：`--needed` 参数会跳过已经安装的包，确保编译环境完整。

2. 下载yay-bin仓库

```bash
git clone https://aur.archlinux.org/yay-bin.git
```

3. 编译，**不要用root身份运行makepkg**

```bash
cd yay-bin
makepkg -si
```

`-s`： (syncdeps)：自动安装构建所需的依赖包；

`-i`： (install)：构建完成后自动安装生成的 `.pkg.tar.zst` 文件。

4. 附上常用的yay指令

```bash
# 更新系统和所有的AUR包
yay
#搜索并安装包
yay -S <包名>
# 卸载包
yay -Rns <包名>
# 查看AUR软件包
yay -Ps
```

## 输入法

推荐使用Fcitx5

```bash
sudo pacman -S fcitx5 fcitx5-rime fcitx5-configtool
```

修改环境变量

```bash
sudo nano /etc/environment
# 末尾添加以下内容
GTK_IM_MODULE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS=@im=fcitx5
```

## 代理软件

使用v2raya，直接用pacman安装软件及内核

```bash
sudo pacman -S v2raya v2ray
```

设置开开机启动

```bash
sudo systemctl enable --now v2raya
# 查看运行状态
systemctl status v2raya
```

## 浏览器

推荐chrome或firefox，还是用那个男人

```bash
sudo pacman -S firefox
yay -S google-chrome
```

## Gnome插件

访问Gnorme插件[网址](https://extensions.gnome.org/)会让你下载一个浏览器插件，安装即可。然后再终端中安装gnome插件

```bash
pacman -Sy gnome-browser-connector
```

在插件地址选择Gnome对应的版本点击install就可以安装了。Gnome版本可以在终端中执行`gnome-shell --version`查看。推荐几个常用必装的插件：

- AppIndicator and KStatusNotifierItem Suppor：后台运行应用放到状态栏
- Blur my Shell：美化插件
- Clipboard Indicator：剪切板插件，类似与Dito
- Dash to dock：gnome的dock默认在应用页面，这个插件把dock放到桌面
- Desktop Icons NG：桌面快捷方式
- System Monitor：查看cpu，内存，上传下载速度
- User Themes：美化插件

大致的软件就装到这里，大家可以去wallheaven上找一个喜欢的壁纸换上，相信你会喜欢上Linux和arch。

## 修改终端快捷键

之前用ubuntu的时候，默认使用`ctrl + alt + t`打开终端，现在给arch也设置同样的快捷键。

打开设置（setting）- 键盘（Keyboard）- 键盘快捷键（Keyboard Shortcuts）- 查看和自定义快捷键（View and Customize Shortcuts）然后自定义快捷键（Customs Shortcuts）：

- name：命名为console
- comman：kgx（这个是GNOME console的启动命令）
- shortcut：Ctrl + Alt + T
