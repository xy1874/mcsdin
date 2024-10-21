&emsp;&emsp;数据通路是用于描述CPU内部信息流的模型，刻画的是指令执行过程中的主要信息的基本流动路径。在图论中，一条路径通常包含若干个顶点和若干条边。因此，数据通路的设计也包含功能部件 (路径的“顶点”) 和部件互连 (路径的“边”) 两个方面。

&emsp;&emsp;数据通路的主要功能部件包括时钟模块、程序计数器 (Program Counter, PC)、指令存储器 (Instruction ROM, IROM)、数据存储器 (Data RAM, DRAM)、寄存器文件 (Register File, RF)和算术逻辑运算单元 (Arithmetic and Logic Unit, ALU)，如图3-1所示。

<center><img src = "../assets/3-1.png" width = 450></center>
<center>图3-1 miniRV数据通路简图</center>


## 1. 时钟模块设计

&emsp;&emsp;Minisys开发板是一个以Xilinx Artix-7系列FPGA为主芯片的实验平台。在Vivado中创建项目时，需选择的芯片型号是<font color = blue>**XC7A100TFGG484-1**</font>。

&emsp;&emsp;单周期CPU需要通过系统时钟信号来控制指令执行的时序。一般地，CPU的执行速率与时钟频率成正比，即时钟频率越高，CPU的执行速度越快。然而，CPU内部的部件存在一定的物理延迟，如果时钟频率过高，这些部件来不及响应，就会产生不稳定的输出结果。本实践课程使用的Minisys开发板具有频率为100MHz的晶振时钟源，该频率对于单周期CPU而言太快。因此，我们需要采用Vivado自带的PLL时钟IP核来对Minisys的时钟进行分频，从而确保单周期CPU能够稳定工作。这里，不妨将晶振时钟分频成25MHz。

### 1.1 时钟IP核使用

&emsp;&emsp;打开Vivado，依次点击`IP Catalog`->`FPGA Features and Design`->`Clocking`，并双击`Clocking Wizard`，如图3-2所示。

<center><img src = "../assets/3-2.png"></center>
<center>图3-2 打开`Clocking Wizard` IP核</center>

&emsp;&emsp;然后，在`Clocking Wizard`对话框中，更改IP核名称为`cpuclk`，并设置采用`PLL`时钟，如图3-3所示。

<center><img src = "../assets/3-3.png" width = 550></center>
<center>图3-3 时钟IP核的基本设置</center>

&emsp;&emsp;点击切换到`Output Clocks`标签页，将`clk_out1`的输出频率设置为`25MHz`，去掉`Reset`并勾选`locked`，点击OK，如图3-4所示。

<center><img src = "../assets/3-4.png" width = 500></center>
<center>图3-4 设置时钟IP核的输出频率</center>

&emsp;&emsp;系统将弹出提示对话框，点击`Generate`按钮即可。此时可以在相应目录下生成`cpuclk.xci`模块。

### 1.2 时钟模块仿真

&emsp;&emsp;新建`cpuclk_sim.v`文件进行时钟仿真，示例代码如下。

``` Verilog
 1|`timescale 1ns / 1ps
 2|module cpuclk_sim();
 3|    // input
 4|    reg fpga_clk = 0;
 5|    // output
 6|    wire clk_lock;
 7|    wire pll_clk;
 8|    wire cpu_clk;
 9|
10|    always #5 fpga_clk = ~fpga_clk;
11|
12|    cpuclk UCLK (
13|        .clk_in1    (fpga_clk),
14|        .locked     (clk_lock),
15|        .clk_out1   (pll_clk)
16|    );
17|
18|    assign cpu_clk = pll_clk & clk_lock;
19|
20|endmodule
```
&emsp;&emsp;将`cpuclk_sim`文件设置为顶层文件，右键点击`cpuclk_sim.v`，在弹出的菜单中选择`Set as Top`以将其设置为顶层文件。

!!! info "补充说明 :mega:"
    &emsp;&emsp;由于PLL锁相环需要一定的初始化时间，因此仿真时，需要按下快捷键`Shift+F2`以多运行`5us`，才能看到`cpu_clk`的波形输出。


## 2. PC设计

&emsp;&emsp;PC是一个32位的寄存器，存储着当前指令的地址，因此又名为程序指针。对于32位的RISC架构CPU，指令均为32比特定长，即每条指令4个字节。因此，PC的第0位和第1位永远为0，故也可以把PC看成是一个30位的寄存器。

&emsp;&emsp;CPU复位时，PC被赋予了一个初始值，这个初始值就是CPU复位后执行的首条指令的地址。

&emsp;&emsp;对于分支指令，需要通过计算，从而判断是否需要使用立即数来更新PC；对于其他指令，则PC的新值等于其旧值加4，如图3-5所示。

<center><img src = "../assets/3-5.png" width = 250></center>
<center>图3-5 PC原理图</center>

&emsp;&emsp;在图3-5中，`pc_sel`信号来源于控制单元，用于选择PC的新值。一般地，可将产生下一个PC的电路封装起来，形成一个NPC模块。


## 3. 存储器设计

### 3.1 Distributed RAM

&emsp;&emsp;Block RAM和Distrubuted RAM是Vivado库中常用的两个存储器IP核。前者的读/写操作均采用同步时序，故读数据比读地址延后了一个时钟周期。这种时序难以满足单周期CPU访问程序ROM的取指需求。因此，本实验使用支持异步读的Distrubuted RAM来实现存储器。

&emsp;&emsp;Distributed RAM由FPGA内部的LUT资源构成。当其被配置成ROM时，可支持数据的异步读取，即给出地址后马上输出数据，如图3-6所示。

<center><img src = "../assets/3-6.png" width = 450></center>
<center>图3-6 Distrubuted RAM异步读时序</center>

&emsp;&emsp;当Distributed RAM被配置成RAM时，既支持异步读，也支持同步读和同步写。Distributed RAM的同步读和同步写时序与Block RAM类似，如图3-7所示。

<center><img src = "../assets/3-7.png" width = 640></center>
<center>图3-7 Distributed RAM同步读写时序</center>

### 3.2 程序ROM

#### 3.2.1 定义程序ROM

&emsp;&emsp;取指单元包含了存放miniRV汇编程序的程序ROM (Instructioin ROM, IROM)。我们需要使用Vivado自带的存储IP核`Distributed Memory Generator`来定义`IROM`。该IP核的手册见Xilinx的在线文档《<a href="https://www.xilinx.com/support/documentation/ip_documentation/dist_mem_gen/v8_0/pg063-dist-mem-gen.pdf" target="_blank">pg063-dist-mem-gen.pdf</a>》。

&emsp;&emsp;在Vivado的`Project Manager`下，点击`IP Catalog`。在搜索框中输入`distributed`，并双击`Distributed Memory Generator`，如图3-8所示。

<center><img src = "../assets/3-8.png"></center>
<center>图3-8 创建`IROM`</center>

&emsp;&emsp;此时会打开`Distributed Memory Generator`的配置对话框。在该对话框中，更改部件名称为`IROM`，设置数据深度和数据宽度，如图3-9所示。

<center><img src = "../assets/3-9.png" width = 550></center>
<center>图3-9 配置IROM</center>

!!! info "IROM配置说明 :mega:"
    &emsp;&emsp;本实验需要创建`64KB`大小的`IROM`，因此`IROM`共包含`16384`个数据单元 (数据位宽为32位)，对应地址线14根。

&emsp;&emsp;配置完成后，依次点击“OK”按钮和“Generate”按钮。此时，Vivado将在当前工程中生成一个名为`IROM.xci`的IROM模块。

&emsp;&emsp;需要注意的是，此时的IROM尚未初始化，其内部不含有任何有效数据。为了让CPU运行汇编程序，我们需要将汇编器生成的机器码导入到IROM当中。

&emsp;&emsp;Distributed Memory Generator的IP核支持通过`.coe`文件导入初始数据。`.coe`文件的语法如图3-10所示。

``` makefile
memory_initialization_radix = 16;  // 表明以下数据采用16进制 (支持2、8、10、16进制)
memory_initialization_vector =     // 下面放数据, 可以不放满; 数据单元之间用逗号分隔
3c01ffff,
343cf000,
3401ff0f,
af810c04,
8c020000,
8c030004,
00000000,
......        
00000000;   // 最后以分号结尾
```
<center>图3-10 .coe文件语法</center>

!!! info "IP核编址方式说明 :mega:"
    &emsp;&emsp;以图3-10所示的`.coe`文件为例，0地址对应第1个数据`0x3c01ffff`，1地址对应第2个数据`0x343cf000`，依此类推。

&emsp;&emsp;按照图3-10所示的语法，将汇编器生成的机器码拷贝到`.coe`文件中，保存并关闭，再将其拷贝到所在工程的根目录下。

&emsp;&emsp;然后，在Vivado中双击刚才创建的IROM IP核，在设置窗口中点击进入`RST & Initialization`标签页，导入`.coe`文件，如图3-11所示。

<center><img src = "../assets/3-11.png"></center>
<center>图3-11 导入程序到IROM</center>

#### 3.2.2 从程序ROM取指令

&emsp;&emsp;IP核配置完毕后，需要将其实例化，并使用PC从其中取出指令，如图3-12所示。

``` Verilog
 1|    ......
 2|    wire [13:0] inst_addr;
 3|    wire [31:0] inst;
 4|
 5|    // 64KB IROM
 6|    IROM Mem_IROM (
 7|        .a      (inst_addr),
 8|        .spo    (inst)
 9|    );
10|
11|    ......
12|endmodule
```
<center>图3-12 IROM实例化</center>

&emsp;&emsp;miniRV的每条指令都是4个字节，因此PC的值是4的整数倍，即`PC[1:0]`恒等于`2'b00`。相应地，IROM的数据宽度是32位，则每个数据单元正好存放一条指令。因此，在图3-12中，使用`PC[15:2]`作为地址来访问IROM。

### 3.3 数据RAM

#### 3.3.1 定义数据RAM

&emsp;&emsp;与IROM一样，DRAM同样也使用Distributed Memory Generator来实现。

&emsp;&emsp;建立DRAM的操作大体上与IROM类似，但有2处不同：

&emsp;&emsp;一是需要将`Memory Type`设置成`Single Port RAM`，如图3-13所示。

<center><img src = "../assets/3-13.png" width = 500></center>
<center>图3-13 配置存储器类型</center>

&emsp;&emsp;二是需要在`Port config`标签页下，将输入、输出端口都配置成`Non Registered`，如图3-14所示。

<center><img src = "../assets/3-14.png" width = 500></center>
<center>图3-14 配置DRAM的端口属性</center>

!!! warning "注意 :loudspeaker:"
    &emsp;&emsp;需要注意的是，按照图3-15配置后，DRAM的读时序是异步读取，写时序是同步写入。

&emsp;&emsp;配置好DRAM后，需要在数据存储单元中将其例化，如图3-15所示。

``` Verilog
1|    // 64KB DRAM
2|    DRAM Mem_DRAM (
3|        .clk    (clk_bridge2dram),
4|        .a      (addr_bridge2dram[15:2]),
5|        .spo    (rdata_dram2bridge),
6|        .we     (wen_bridge2dram),
7|        .d      (wdata_bridge2dram)
8|    );
```
<center>图3-15 DRAM实例化</center>


## 4. 寄存器堆设计

&emsp;&emsp;miniRV含有32个32位寄存器，详细说明见上一节中的“<a href="../1-inst/#1-minirv" target="_blank">miniRV通用寄存器</a>”。

&emsp;&emsp;分析指令格式易知，一条指令最多需要访问3个寄存器，这决定了寄存器堆必须具有3个端口——2个读端口 (对应`rs1`和`rs2`)和1个写端口 (对应`rd`)，如图3-16所示。

<center><img src = "../assets/3-16.png" width = 160></center>
<center>图3-16 寄存器堆模块图</center>

&emsp;&emsp;我们在数字逻辑设计的实验中已经设计过寄存器堆，此处不再赘述。


## 5. ALU设计

&emsp;&emsp;ALU是算术逻辑运算单元，负责完成CPU中的算术、逻辑、移位和比较等运算。因此，在设计ALU的时候，需要先设计完成各类运算的子部件，然后使用译码器来控制ALU在运行时使用的具体子部件，并使用多路选择器产生ALU输出，如图3-17所示。

<center><img src = "../assets/3-17.png" width = 300></center>
<center>图3-17 ALU模块图</center>

&emsp;&emsp;具体实现时，出于简便性考虑，可以使用Verilog运算符来实现加法、减法、移位等运算。
