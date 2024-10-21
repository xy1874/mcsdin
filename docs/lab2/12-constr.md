&emsp;&emsp;下板前，需编写引脚约束文件（.xdc），以将顶层模块的接口信号与Minisys开发板上的FPGA芯片引脚连接起来。

## 1. 添加/创建约束文件

&emsp;&emsp;在Vivado中，右键点击`Project Manager`下面`Sources`中的`Constraints`，在弹出的菜单中选择`Add Source…`，并按照图13-1所示的设置点击`Next`。

<center><img src="../assets/13-1.png"></center>
<center>图13-1 添加或创建约束文件</center>

## 2. I/O引脚约束

&emsp;&emsp;约束文件初始为空，其基本语法如图13-2所示。

```
1| # 语法格式为set_property PACKAGE_PIN (pin location) [get_ports (port name)]
2| set_property PACKAGE_PIN P5 [get_ports sw_i[0]]
3| set_property PACKAGE_PIN F6 [get_ports led_o[0]]
4|
5| # 语法格式为set_property IOSTANDARD (level:LVDS,LVCMOS18,LVCMOS33 etc.) [get_ports (port name)]
6| set_property IOSTANDARD LVCMOS33 [get_ports sw_i[0]]
7| set_property IOSTANDARD LVCMOS33 [get_ports led_o[0]]
```
<center>图13-2 约束文件基本语法</center>

&emsp;&emsp;约束文件主要包含两部分内容，第一部分是信号和FPGA芯片引脚的对应关系，比如第1行是将FPGA芯片的`P5`引脚和信号`sw_i[0]`信号绑定。第二部分是引脚的电气属性设置，`IOSTANDARD`全部设置成`LVCMOS33`，表示该IO电平标准是`CMOS 3.3v`（即高电平对应3.3V，低电平对应0.0V）。

!!! info "PS :mega:"
    &emsp;&emsp;图13-2所示的引脚约束代码也可写成如图13-3所示的语句。

    ```
    1| set_property -dict { IOSTANDARD LVCMOS33 PACKAGE_PIN P5 } [get_ports sw_i[0]]
    2| set_property -dict { IOSTANDARD LVCMOS33 PACKAGE_PIN F6 } [get_ports led_o[0]]
    ```
    <center>图13-3 约束语句的另一种写法</center>

&emsp;&emsp;引脚分配既可以参考<a href="https://gitee.com/hitsz-cslab/cpu/blob/master/stupkt/Minisys%E5%BC%80%E5%8F%91%E6%9D%BF%E7%A1%AC%E4%BB%B6%E6%89%8B%E5%86%8C.pdf" target="_blank">Minisys开发板手册</a>，也可以直接在开发板的丝印上读出。例如，观察开发板最右侧的拨码开关下方丝印着“`SW0-W4`”，表示拨码开关SW0对应FPGA的`W4`引脚。

## 3. 时钟约束

&emsp;&emsp;约束文件除了对物理引脚进行约束，还可以对时序进行约束。感兴趣的同学们可以阅读参考资料《<a href="http://xilinx.eetrend.com/blog/2019/100018252.html" target="_blank">约束功能概述</a>》。

&emsp;&emsp;如果Vivado工程需要使用时钟信号，为了方便后续时序分析，以及为了让工具能够根据给定的频率进行逻辑优化，则需要添加始终约束文件。

!!! info "PS :mega:"
    &emsp;&emsp;在工程中，除了顶层模块I/O引脚的约束文件外，还可以新建一个始终约束文件`clock.xdc`，在其中添加如图13-4所示的约束代码。

    ```
    1| # 语法格式为create_clock -name (clock name) -period (time) [get_ports (port name)]
    2| create_clock -name fpga_clk -period 10 [get_ports fpga_clk]
    ```
    <center>图13-4 时钟约束</center>

    &emsp;&emsp;其中，`-period`项表示时钟信号的周期大小（单位：ns）。比如Minisys的板上时钟为100MHz，则时钟周期为10ns，因此是`-period 10`。
