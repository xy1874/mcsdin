## 1. 编写miniLA汇编程序

&emsp;&emsp;本实验为miniLA指令集提供了两套汇编与模拟运行环境，一套是我校同学自行开发的LoongSonAssembler工具，另一套则是基于龙芯官方的<a href="https://gitee.com/loongson-edu/la32r-toolchains" target=_blank>LA32R GNU工具</a>和<a href="https://gitee.com/wwt_panache/la32r-nemu" target=_blank>LA32R-NEMU模拟器</a>。

### 1.1 编写汇编程序

=== "LoongSonAssembler环境"

    &emsp;&emsp;LoongSonAssembler是一个支持LA32R指令集的，具有编辑器、调试器和模拟器等功能的集成开发工具，其使用方法与RARS接近，详见《<a href="../B-LAasm" target=_blank>附录B - LoongSonAssembler简介</a>》。

    &emsp;&emsp;新建汇编源文件`calculatorA.asm`，在LoongSonAssembler或代码编辑器编写计算器程序。

    &emsp;&emsp;程序编写完成后，在LoongSonAssembler打开`calculatorA.asm`，点击左侧工具栏的蓝色齿轮按钮（或按下快捷键 ++f5++）即可对代码进行汇编，如图2-a所示。

    <center><img src = "../assets/2-a.png" width = 600></center>
    <center>图2-a 使用LoongSonAssembler对代码进行汇编</center>

    &emsp;&emsp;汇编后，请在LoongSonAssembler中完成汇编程序的初步调试。

=== "LA32R GNU + NEMU环境"

    &emsp;&emsp;首先，参照《<a href="../C-LAenvir" target=_blank>附录C - LA32R GNU+NEMU环境搭建</a>》搭建LA32R的汇编和模拟器环境，并使用`mkdir`命令在用户目录下创建一个工作目录。

    &emsp;&emsp;接着，使用第三方代码编辑器新建汇编源文件`calculatorA.asm`并编写计算器程序。
    
    &emsp;&emsp;将编写好的.asm文件拷贝到LA32R汇编环境的工作目录中，并执行命令`./lasm.sh <asm file>`对`.asm`文件进行汇编。该命令将生成`.bin`和`.hex`文件。其中，**`.bin`文件是存储着汇编程序机器码的二进制文件，用于在LA32R NEMU模拟器中调试程序**；`.hex`文件则是汇编程序机器码的16进制文本文件，用于在下一步将程序导入到Logisim的SoC中运行。

    &emsp;&emsp;如果编写的汇编程序不含数据段，则sh脚本将生成`.bin`和`.hex`两个文件。
    
    &emsp;&emsp;如果编写的汇编程序包含数据段，则sh脚本将生成`.bin`、`_text.hex`和`_data.hex`三个文件。其中，`_text.hex`和`_data.hex`文件分别是程序的代码段和数据段的十六进制机器码文本文件，而`.bin`文件则同时包含了代码段和数据段，其结构如图2-I所示。

    <center><img src = "../assets/2-I.png" width = 200></center>
    <center>图2-&#8544; 程序包含数据段时，`.bin`文件的结构</center>

    &emsp;&emsp;由图2-&#8544;可知，汇编程序的 **代码段将存放在`.bin`文件的`0`偏移地址处**，而汇编程序的 **数据段则存放在`.bin`文件的`0x00010000`偏移地址处**。

    &emsp;&emsp;进行下一步之前，请首先在LA32R-NEMU中完成汇编程序的初步调试。
    
    !!! tip "如何使用NEMU模拟器调试程序? :books:"
        &emsp;&emsp;在搭建好的环境中，执行命令`la32r-nemu-interpreter <.bin file>`从而将`.bin`文件中的程序加载到NEMU模拟器中。需要注意的是，整个`.bin`文件将被加载到`0x1C000000`地址处，即 **在NEMU模拟器中，代码段的基地址是`0x1C000000`，数据段的基地址是`0x1C010000`**。
    
        &emsp;&emsp;NEMU模拟器的常用命令如下：
        
        &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`help`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;：打印使用指南  
        &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`b <inst address>`&ensp;：添加断点并运行到断点指令处  
        &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`si`&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;&nbsp;：执行单条指令  
        &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`x <n> <address>`&emsp;：打印主存地址`address`开始的`n`个存储字，且`n`是10进制数  
        &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;`info r`&emsp;&emsp;&emsp;&emsp;&ensp;&emsp; ：打印所有寄存器的值

### 1.2 导出机器码

=== "LoongSonAssembler环境"

    &emsp;&emsp;接下来，需要导出汇编程序的机器码，为下一步在Logisim中的SoC电路上运行程序做准备。
    
    &emsp;&emsp;依次点击菜单栏的 “Result” -> “另存为*.txt”，并在某个文件夹下将导出的机器码另存为`.hex`文本文件，如图2-b所示。

    <center><img src = "../assets/2-b.png" width = 700></center>
    <center>图2-b 导出汇编程序的机器码</center>

    &emsp;&emsp;给文件命名时，可将后缀名命名为`.hex`或`.txt`。不管后缀名是什么，所导出的文件都是文本文件，都可以使用常见的文本编辑器打开，比如记事本、VSCode等等。

    &emsp;&emsp;打开导出的文本文件，即可查看程序的机器码，如图2-c所示。

    <center><img src = "../assets/2-c.png" width = 320></center>
    <center>图2-c 查看所导出的程序机器码</center>

    !!! tip "关于程序中的数据段 :books:"
        &emsp;&emsp;如果汇编程序中定义了数据段，则需要在LoongSonAssembler中分别导出代码段和数据段。具体操作是，在图2-b所示的界面中，依次点击 “Data” -> “另存为*.txt” 即可。此时，所导出的程序将由两个`.hex`文件组成，一个存储代码段，另一个存储数据段。

        &emsp;&emsp;如果汇编代码中没有定义数据段，则代码在汇编后不会生成.data段，自然就不需要导出了。

=== "LA32R GNU + NEMU环境"
    
    &emsp;&emsp;在1.1小节执行`./lasm.sh <asm file>`命令时，sh脚本已经将程序的代码段或数据段导出到`.hex`文本文件中。

&emsp;&emsp;被导出的程序可在下一步加载到Logisim中的SoC电路上运行。其中，代码段加载到指令存储器IROM，数据段加载到数据存储器DRAM。


## 2. 在Logisim上运行程序

### 2.1 LoongArch SoC电路

&emsp;&emsp;打开Logisim工具，并依次点击菜单栏的 “File” -> “Open”，打开 LoongArch_SoC.circ 的SoC电路设计文件。该 SoC 电路包含LoongArch单周期CPU、指令存储器、数据存储器以及外设接口电路和外设，如图2-d所示。

<center><img src = "../assets/2-d.png"></center>
<center>图2-d LoongArch SoC顶层电路图</center>

!!! example "外设的编址方式 :computer:"
    &emsp;&emsp;在本课程中，存储器和外设统一编址，即存储器和外设共用整个32位地址空间，其中最高的64KB (`0xFFFF0000` ~ `0xFFFFFFFF`) 用作I/O地址空间。

&emsp;&emsp;在图2-d所示的LoongArch SoC电路中，各模块的层次关系为：

<style>
.center {
    margin: auto;
    width: 48%;
}
</style>

<div class="center">
<p style="line-height:100%">&#128311; SoC顶层模块</p>
<p style="line-height:100%">&emsp;&emsp; &#128312; CPU：         LoongArch单周期CPU  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; PC：          程序指针寄存器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; Controller：  控制器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; RegFile：     寄存器堆  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; ImmModel：    立即数扩展器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; Branch：      条件分支比较器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; ALUModel：    算术逻辑单元  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; IROM：        指令存储器（只读）  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; DRAM：        数据存储器  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; IOBus：       总线模块  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; SwitchDriver：拨码开关的I/O接口电路  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; LEDDriver：   LED的I/O接口电路  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; DigitDriver： 数码管的I/O接口电路</p>
</div>

&emsp;&emsp;本实验使用的LoongArch SoC支持如表2-a所示的25条LoongArch指令。

<center>表2-a LoongArch SoC支持指令</center>
<center><img src = "../assets/t2-a.png"></center>

### 2.2 加载程序

&emsp;&emsp;在Logisim中双击打开LoongArch SoC的`IROM`模块，在IROM的存储器上点击右键选择`Clear Contents`，然后再次右键选择`Edit Contents`，如图2-e所示。

<center><img src = "../assets/2-e.png" width = 300></center>
<center>图2-e 清空指令存储器</center>

&emsp;&emsp;打开1.2小节的`.hex`文件，将其内容复制并粘贴到IROM存储器的编辑框中，从而将程序的机器码指令存放到指令存储器的0地址处，如图2-f所示。

<center><img src = "../assets/2-f.png" width = 100%></center>
<center>图2-f 将`.hex`文件中的机器码放置到IROM</center>

&emsp;&emsp;粘贴完成后，关闭图2-f右侧的Hex Editior窗口即可。

!!! info "如果你的程序有数据段 :bookmark_tabs:"
    &emsp;&emsp;若所编写的汇编程序除了代码段，还含有数据段，则参照以上操作，将数据段导出的`.hex`文件的内容放置到DRAM模块的存储器中。
    
    &emsp;&emsp;DRAM和IROM的主要区别是，在Logisim中按 ++ctrl+r++ 复位时，DRAM存储的数据会被清空，而IROM存储的指令不会被清空。

### 2.3 执行程序

&emsp;&emsp;按下快捷键 ++ctrl+k++ 进入连续时钟执行模式，再次按下 ++ctrl+k++ 暂停执行。在此模式下，Logisim将持续不断地给SoC电路提供时钟信号。

&emsp;&emsp;按下快捷键 ++ctrl+t++ 进入单步时钟执行模式。在此模式下，Logisim每次给SoC电路提供一个时钟信号。使用单步时钟执行模式，开发者可以方便地查看SoC电路和程序的运行过程，从而对程序或电路进行调试。

&emsp;&emsp;按下快捷键 ++ctrl+r++ 复位整个SoC电路。

&emsp;&emsp;在程序运行过程中，SoC电路的信号将实时发生变化，如图2-g所示。

<center><img src = "../assets/2-g.png"></center>
<center>图2-g 程序执行时，各信号根据当前执行的指令发生相应的变化</center>

&emsp;&emsp;程序调试完毕后，可将SoC电路的时钟频率调高，从而使得CPU运行得更快。依次点击菜单栏的 “Simulate” -> “Tick Frequency”，将时钟频率设置到最高的4.1kHz，如图2-h所示。

<center><img src = "../assets/2-8.png" width = 450></center>
<center>图2-h 修改时钟频率</center>
