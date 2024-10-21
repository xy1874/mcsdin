## 1. LA32R GNU工具安装

&emsp;&emsp;龙芯官方提供编译好的<a href="https://gitee.com/loongson-edu/la32r-toolchains" target=_blank>LA32R GNU工具</a>。因此，只需要下载并解压官方仓库中的发行版，然后再配置环境变量即可。

&emsp;&emsp;首先，从龙芯LA32R GNU仓库的<a href="https://gitee.com/loongson-edu/la32r-toolchains/releases/tag/v0.0.3" target=_blank>发行版页面</a>中，根据本地计算机的架构及系统下载相应的压缩包。

&emsp;&emsp;以下将以Linux系统为例介绍实验工具和环境的安装。

&emsp;&emsp;在Linux终端执行`tar -xf *.tar.xz -C <target direcory>`解压GNU工具包到特定路径。

!!! warning "命令中的尖括号是什么意思？ :mag:"
    &emsp;&emsp;命令中的尖括号部分应当替换成实际的路径，比如想解压到`/opt`目录，则应执行`sudo tar -xf *.tar.xz -C /opt`。以下同理。

&emsp;&emsp;然后，编辑`~/.bashrc`文件，在文件末尾添加`export PATH=$PATH:<your directory>/loongson-gnu-toolchain-8.3-x86_64-loongarch32r-linux-gnusf-v2.0/bin/`后保存文件。注意，编辑该文件一般需要root权限。

&emsp;&emsp;接着，在终端执行`source ~/.bashrc`命令使环境变量生效。

&emsp;&emsp;完成以上操作后，在终端输入`loo`并按下 ++tab++ 键，若终端自动将GNU工具的名称补全，则表示LA32R GNU工具安装完成。

&emsp;&emsp;最后，下载实验包中的`lasm.sh`脚本，并将其拷贝到你的工作目录下。

## 2. LA32R NEMU环境安装

&emsp;&emsp;<a href="https://gitee.com/wwt_panache/la32r-nemu" target=_blank>LA32R-NEMU</a>是基于NEMU项目移植的轻量级LA23R单周期CPU模拟器。

&emsp;&emsp;为了方便调试，本课程对LA32R NEMU模拟器输出的调试信息进行了少量调整。同学们只需下载并使用实验包中的`la32r-nemu-interpreter`即可。

&emsp;&emsp;下载实验包中的`la32r-nemu-interpreter`后，编辑`~/.bashrc`文件，在文件末尾添加`export PATH=$PATH:<direcotry of la32r-nemu-interpreter>`后保存文件。

&emsp;&emsp;接着，在终端执行`source ~/.bashrc`命令使环境变量生效。

&emsp;&emsp;对于Linux系统，还需执行命令`sudo apt install -y libsdl2-2.0-0`以安装运行库。

&emsp;&emsp;安装完成后，执行命令`la32r-nemu-interpreter`运行LA32R的NEMU模拟器，如图C-1所示。在模拟器中执行`help`命令以查看模拟器所支持的命令和功能；执行`q`命令退出模拟器。

<center><img src = "../assets/C-1.png"></center>
<center>图C-1 运行LA32R的NEMU模拟器</center>
