# Fedora sogoupinyin rpm安装
- 本仓库基于官方sogoupinyin debian安装包(sogoupinyin_4.2.1.145_amd64.deb)构建rpm源码包，并发布编译后的rpm二进制包。
- 发布的sogoupinyin rpm包适用于fedora36。

## 前言
文档分两部分讲述：
- sogoupinyin rpm包的构建
- sogoupinyin rpm包的安装

参考资料：
[Fedora Workstation 36安装搜狗拼音输入法](https://blog.csdn.net/codestore/article/details/132255059)

[解决了一个在 LinuxMint 中运行搜狗拼音输入法的问题_linux mint 20 sougoupingyin-CSDN博客](https://blog.csdn.net/wesleyflagon/article/details/131564216)

## sogoupinyin rpm包的构建

1. 从搜狗输入法官网上下载sogou输入法deb安装包

sogoupinyin_4.2.1.145_amd64.deb

2. 转换为rpm源码包

```bash
# 需要提前安装alien工具
sudo alien -r -g -c -v sogoupinyin_4.2.1.145_amd64.deb
```

3. 改造rpm源码包
- spec文件添加依赖（没有求证过是否完整，或者冗余。在我的fedora36上工作ok，我的工作环境可能已经非clean的了，这部分需要注意。）

```python
Requires: qt5-qtbase
Requires: fcitx
Requires: fcitx-ui-light
Requires: fcitx-qt5
Requires: fcitx-gtk3
Requires: fcitx-table
Requires: fcitx-table-extra
Requires: fcitx-table-other
Requires: fcitx-table-chinese
Requires: fcitx-data
Requires: fcitx-pinyin
Requires: fcitx-configtool
```

- spec文件删除冗余路径

```python
类似以下这种系统已经存在的路径
%dir "/"
%dir "/etc/"
%dir "/etc/X11"
...
```

- 添加fcitx.sh系统配置文件,路径’/etc/profile.d/fcitx.sh’
- 修改对qt5的依赖

默认情况下sogoupinyin pkg依赖于包内部的qt5组件，运行时会存在问题。删除他们，将依赖指向系统的qt5组件

- 修改sogoupinyin fcitx库的安装路径到/usr/lib64/fcitx
4. 构建rpm包

```bash

rpmbuild --target=x86_64 --buildroot /full/path/to/rpm_project/ -bb /path/to/rpm_sepc_file
```

注意，生成的rpm包在编译命令执行路径的上一级。


## sogoupinyin rpm包的安装
- 卸载自带ibus

```bash
sudo dnf remove ibus
```

- 安装sogoupinyin rpm

```bash
# 使用dnf localinstall会自动安装依赖
sudo dnf localinstall sogoupinyin-4.2.1.145-2.x86_64.rpm
```

- 安装出入法管理器

```bash
sudo dnf install im-chooser
```

选择默认输入法为fcitx，添加sougoupinyin
![Selection_167](https://github.com/steveliu121/fedora_sogoupinyin/assets/20109927/938a7a72-a208-41a7-81a1-c9e8e56362b4)

![Selection_168](https://github.com/steveliu121/fedora_sogoupinyin/assets/20109927/0a1aabfe-c316-4ca1-aad0-f76c4be580b9)

- 重新登录，输入法生效
