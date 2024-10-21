## 1. 使用RARS编写汇编程序

### 1.1 编写汇编程序

&emsp;&emsp;RARS (RISC-V Assembler and Runtime Simulator) 是一个轻量级的交互式集成开发环境，用于使用RISC-V汇编语言进行编程，具有代码提示，模拟运行，调试，统计等。

&emsp;&emsp;在RARS上新建汇编源文件`calculator.asm`，运用miniRV指令集编写简易计算器程序。

&emsp;&emsp;代码编写完成后，依次点击菜单栏的 “Run” -> “Assemble” 对代码进行汇编，或点击工具栏中间的汇编按钮进行汇编，如图2-1所示。

<center><img src = "../assets/2-1.png"></center>
<center>图2-1 对编写完成的代码进行汇编</center>

&emsp;&emsp;汇编后，请在RARS中完成汇编程序的初步调试。

### 1.2 导出机器码

&emsp;&emsp;接下来，需要导出汇编程序的机器码，为下一步在Logisim中的SoC电路上运行程序做准备。

&emsp;&emsp;依次点击菜单栏的 “File” -> “Dump Memory”，并在后续弹出的对话框中选择 “Dump Format” 为 “Hexademical Text”，如图2-2所示。

<center><img src = "../assets/2-2.png"></center>
<center>图2-2 以16进制文本格式导出程序的机器码</center>

&emsp;&emsp;点击图2-2中的 “Dump To File” 按钮后，将弹出保存文件的对话框。自行选择合适的文件夹，并为导出的文本文件起名。

&emsp;&emsp;给文件命名时，可将后缀名命名为`.hex`或`.txt`。不管后缀名是什么，所导出的文件都是文本文件，都可以使用常见的文本编辑器打开，比如记事本、VSCode等等。

&emsp;&emsp;打开导出的文本文件，即可查看程序的机器码，如图2-3所示。

<center><img src = "../assets/2-3.png" width = 400></center>
<center>图2-3 查看所导出的程序机器码</center>

!!! tip "关于程序中的数据段 :books:"
    &emsp;&emsp;如果汇编程序中定义了数据段，则需要在RARS中分别导出代码段和数据段。具体操作是，在图2-2所示的 “Dump Memory To File” 对话框中，将左侧的 “Memory Segment” 下拉选择成 “.data” 后，再点击 “Dump To File” 按钮即可。此时，所导出的程序将由两个`.hex`文件组成，一个存储代码段，另一个存储数据段。

    &emsp;&emsp;如果汇编代码中没有定义数据段，则代码在汇编后不会生成.data段，自然就不需要导出了。

&emsp;&emsp;被导出的程序可在下一步加载到Logisim中的SoC电路上运行。其中，代码段加载到指令存储器IROM，数据段加载到数据存储器DRAM。



## 2. 在Logisim上运行程序

### 2.1 RISC-V SoC电路概述

&emsp;&emsp;打开Logisim工具，并依次点击菜单栏的 “File” -> “Open”，打开 RISC-V_SoC.circ 的SoC电路设计文件。该 SoC 电路包含RISC-V单周期CPU、指令存储器、数据存储器以及外设接口电路和外设，如图2-4所示。

<center><img src = "../assets/2-4.png"></center>
<center>图2-4 RISC-V SoC顶层电路图</center>

!!! example "外设的编址方式 :computer:"
    &emsp;&emsp;在本课程中，存储器和外设统一编址，即存储器和外设共用整个32位地址空间，其中最高的64KB (`0xFFFF0000` ~ `0xFFFFFFFF`) 用作I/O地址空间。

&emsp;&emsp;在图2-4所示的RISC-V SoC电路中，各模块的层次关系为：

<style>
.center {
    margin: auto;
    width: 48%;
}
</style>

<div class="center">
<p style="line-height:100%">&#128311; SoC顶层模块</p>
<p style="line-height:100%">&emsp;&emsp; &#128312; CPU：         RISC-V单周期CPU  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; PC：          程序指针寄存器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; Controller：  控制器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; RegFile：     寄存器堆  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; ImmModel：    立即数扩展器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; Branch：      条件分支比较器  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; ALUModel：    算术逻辑单元  </p>
<p style="line-height:90%">&emsp;&emsp;&emsp;&emsp; &#128313; CSR：    控制状态寄存器  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; IROM：        指令存储器（只读）  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; DRAM：        数据存储器  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; IOBus：       总线模块  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; SwitchDriver：拨码开关的I/O接口电路  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; LEDDriver：   LED的I/O接口电路  </p>
<p style="line-height:100%">&emsp;&emsp; &#128312; DigitDriver： 数码管的I/O接口电路</p>
</div>

&emsp;&emsp;本实验使用的RISC-V SoC支持如表2-1所示的24条RISC-V指令。

<center>表2-1 RISC-V SoC支持的部分指令</center>
<center><img src = "../assets/t2-1.png"></center>

### 2.2 加载程序

&emsp;&emsp;在Logisim中双击打开RISC-V SoC的`IROM`模块，在`Program_ROM`存储器上点击右键选择`Clear Contents`，然后再次右键选择`Edit Contents`，如图2-5所示。

<center><img src = "../assets/2-5.png" width = 300></center>
<center>图2-5 清空指令存储器</center>

&emsp;&emsp;打开1.2小节的`.hex`文件，将其内容复制并粘贴到IROM存储器的编辑框中，从而将程序的机器码指令存放到指令存储器的0地址处，如图2-6所示。

<center><img src = "../assets/2-6.png" width = 100%></center>
<center>图2-6 将`.hex`文件中的机器码放置到IROM</center>

&emsp;&emsp;粘贴完成后，关闭图2-6右侧的Hex Editior窗口即可。

!!! info "如果你的程序有数据段 :bookmark_tabs:"
    &emsp;&emsp;若所编写的汇编程序除了代码段，还含有数据段，则参照以上操作，将数据段导出的`.hex`文件的内容放置到DRAM模块的存储器中。
    
    &emsp;&emsp;DRAM和IROM的主要区别是，在Logisim中按 ++ctrl+r++ 复位时，DRAM存储的数据会被清空，而IROM存储的指令不会被清空。

### 2.3 执行程序

&emsp;&emsp;按下快捷键 ++ctrl+k++ 进入连续时钟执行模式，再次按下 ++ctrl+k++ 暂停执行。在此模式下，Logisim将持续不断地给SoC电路提供时钟信号。

&emsp;&emsp;按下快捷键 ++ctrl+t++ 进入单步时钟执行模式。在此模式下，Logisim每次给SoC电路提供一个时钟信号。使用单步时钟执行模式，开发者可以方便地查看SoC电路和程序的运行过程，从而对程序或电路进行调试。

&emsp;&emsp;按下快捷键 ++ctrl+r++ 复位整个SoC电路。

&emsp;&emsp;在程序运行过程中，SoC电路的信号将实时发生变化，如图2-7所示。

<center><img src = "../assets/2-7.png"></center>
<center>图2-7 程序执行时，各信号根据当前执行的指令发生相应的变化</center>

&emsp;&emsp;程序调试完毕后，可将SoC电路的时钟频率调高，从而使得CPU运行得更快。依次点击菜单栏的 “Simulate” -> “Tick Frequency”，将时钟频率设置到最高的4.1kHz，如图2-8所示。

<center><img src = "../assets/2-8.png" width = 450></center>
<center>图2-8 修改时钟频率</center>

!!! info "SoC电路的例外与中断 :feet:"
    &emsp;&emsp;SoC电路支持 **ecall** 系统调用和 **外部中断** 两种例外的处理。例外处理程序存放在IROM模块的`Trap_Handler_ROM`存储器中。

    &emsp;&emsp;**例外处理程序的统一入口地址是`0x1C090000`**。发生例外时，例外处理程序将根据例外的原因作出相应的处理 —— 如果是`ecall`指令，例外处理程序将在数码管和LED上显示寄存器`a0`的值；如果是外部中断（即图2-7所示的button按钮），例外处理程序将在数码管和LED上显示`0x11111111` ~ `0x88888888`。
