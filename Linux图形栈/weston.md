# weston相关

## 参考链接
[weston源码托管](https://gitlab.freedesktop.org/wayland/weston)  
[weston编译说明](https://wayland.freedesktop.org/building.html)  
[weston文档](https://wayland.pages.freedesktop.org/weston/)  
[weston.ini文档](https://www.mankier.com/5/weston.ini)  
[debian源码托管-包含debian目录](https://salsa.debian.org/xorg-team/wayland/weston)  
[Wayland与Weston简介-多图必看](https://blog.csdn.net/jinzhuojun/article/details/47290707)  


## weston启动
```
# 查看命令说明
$ weston -h
# 以drm-backend作为后端，同时启动xwayland
$ weston --xwayland --backend=drm-backend.so 1> $HOME/.weston.log 2>&1
```

## 配置文件

weston启动时会在以下目录查找weston.ini配置文件，一般保存在 /etc/xdg/weston/weston.ini

- $XDG_CONFIG_HOME/weston.ini   (if $XDG_CONFIG_HOME is set)
- $HOME/.config/weston.ini      (if $HOME is set)
- weston/weston.ini in each $XDG_CONFIG_DIR    (if $XDG_CONFIG_DIRS is set)
- /etc/xdg/weston/weston.ini    (if $XDG_CONFIG_DIRS is not set)


## 源码目录介绍
- compositor 目录包含了 Weston 的核心代码，实现了窗口管理器和桌面环境的基本功能，包括窗口的创建、显示、移动、调整大小等操作，以及输入设备的管理、屏幕输出的管理等。其中，compositor.c 文件是 Weston 的主要代码文件，包含了窗口管理器和桌面环境的核心逻辑

- clients 目录包含了 Weston 的客户端应用程序，它们与 Weston 通过 Wayland 协议进行通信。这些客户端应用程序可以显示窗口、处理输入事件、与窗口管理器交互等。其中，weston-terminal 和 weston-info 是 Weston 的两个主要客户端应用程序，它们可以在 Weston 窗口管理器中显示终端窗口和系统信息

- desktop-shell 目录包含了 Weston 的桌面环境实现，它提供了 Weston 桌面环境的图形界面，包括菜单、任务栏、桌面图标等。desktop-shell 目录中的代码主要负责 Weston 桌面环境的布局、渲染等操作

- ivi-shell 目录是 Weston 的 IVI（In-Vehicle Infotainment）插件，它提供了 Weston 在车载信息娱乐系统中的支持。ivi-shell 目录中的代码主要负责 Weston 在车载信息娱乐系统中的分层、视图、输入设备管理等操作

- libweston 目录包含了 Weston 的核心库代码。它提供了 Weston 中的一些基本功能，例如输入设备管理、屏幕输出管理、OpenGL ES 渲染等。libweston 目录中的代码主要以库的形式提供给 Weston 的其他组件使用

- protocols 目录包含了 Weston 的协议定义，它定义了 Weston 与客户端应用程序之间的通信协议，例如窗口的创建、显示、调整大小等操作。protocols 目录中的代码主要以头文件的形式提供给 Weston 的其他组件使用

- include 目录包含了 Weston 的头文件，它定义了 Weston 的公共接口，例如 Weston 的配置文件、日志接口、输入设备接口等

- tests 目录包含了 Weston 的测试代码，它用于测试 Weston 的各种功能和接口，以及 Weston 的兼容性和性能
