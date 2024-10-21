&emsp;&emsp;（1）根据给出的数据通路表的示例，完成数据通路表，画出数据通路图;

&emsp;&emsp;（2）根据给出的控制信号表的示例，完成控制信号表，在数据通路图上添加控制信号，完成单周期CPU设计结构图;

&emsp;&emsp;（3）从指导书首页的实验包下载链接中下载Vivado模板工程（`proj_miniRV.zip`或`proj_miniLA.zip`）；划分好子模块，新建`.v`源文件并使用Verilog HDL实现各模块 (芯片型号：<font color=blue>**XC7A100TFGG484-1**</font>);

&emsp;&emsp;（4）编写仿真程序和使用Trace验证方法，测试设计的正确性;

&emsp;&emsp;（5）设计总线桥和I/O接口（必做外设接口：数码管、拨码开关、按键开关、LED）；

&emsp;&emsp;（6）在汇编器中导出汇编实验的机器码，参照<a href="../2-parts/#321-rom" target="_blank">3.2.1 定义程序ROM</a>，将机器码拷贝到新建的`.coe`后缀的文本文件中，再将`.coe`文件导入到存储器IP核；

&emsp;&emsp;（7）添加约束文件，运行综合、实现、生成比特流，并完成下板运行及设计优化。
