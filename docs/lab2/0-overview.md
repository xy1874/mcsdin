## 1. 实验目的

&emsp;&emsp;（1）理解单周期CPU工作过程；

&emsp;&emsp;（2）理解指令存储器和数据存储器的哈佛结构存储；

&emsp;&emsp;（3）熟悉miniRV指令集；

&emsp;&emsp;（4）掌握单周期CPU设计与实现方法。

&emsp;&emsp;（1）理解流水线CPU工作过程；

&emsp;&emsp;（2）理解流水线冲突产生的原因及其解决方法；

&emsp;&emsp;（3）掌握流水线CPU设计与实现方法。



## 2. 实验内容

&emsp;&emsp;本实验要求同学们在Minisys开发板 (芯片型号：<font color = blue>**XC7A100TFGG484-1**</font>) 上实现支持miniRV-1指令集的单周期CPU。

&emsp;&emsp;要求所实现单周期CPU的时钟频率<u>不低于25MHz</u>。

&emsp;&emsp;miniRV指令集如表1-1所示。其中，黑色字体的24条指令必做，蓝色字体的13条选做。

<center>表1-1 miniRV指令概述</center>
<center><img src = "../assets/1-1.png" width = 550></center>

!!! info "关于miniLA :loudspeaker:"
    &emsp;&emsp;miniLA指令集如表1-1A所示。其中，黑色字体的25条指令必做，蓝色字体的13条选做。

    <center>表1-1A miniLA指令概述</center>
    <center><img src = "../assets/1-1A.png" width = 550></center>


&emsp;&emsp;本实验要求同学们在Minisys开发板 (芯片型号：<font color = blue>**XC7A100TFGG484-1**</font>) 上，改造单周期CPU并实现支持miniRV-1指令集的流水线CPU。

&emsp;&emsp;具体要求如下：

&emsp;&emsp;（1）<u>至少</u>实现 **五级** 流水线；

&emsp;&emsp;（2）支持必做的24条指令，可添加选做指令；

&emsp;&emsp;（3）改造单周期CPU数据通路，设计理想流水线CPU；

&emsp;&emsp;（4）实现流水线暂停机制，解决数据冒险；

&emsp;&emsp;（5）实现数据前递机制，解决数据冒险；

&emsp;&emsp;（6）实现分支预测（选做），解决控制冒险；

&emsp;&emsp;（7）所实现的流水线CPU主频<u>不低于50MHz</u>。
