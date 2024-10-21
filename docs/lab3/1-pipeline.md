&emsp;&emsp;流水线（Pipeline）技术是指在程序执行时，将多条指令的执行操作在时间上进行重叠，从而提高指令级并行度（Instruction-Level Parallelism，ILP）的一种技术手段。IBM在1961年推出的IBM 7030 Stretch超级计算机是世界上最早使用流水线技术的计算机。流水线技术现已广泛应用在现代处理器当中。

&emsp;&emsp;流水线技术的基本原理是，在CPU中利用多个不同功能的电路单元组成一条指令处理流水线，然后将一条指令的执行过程切分成多个阶段，并由这些电路单元分别依次执行，一方面能够实现在一个时钟周期内同时执行多条指令，提高各电路单元的利用率，另一方面有利于提高CPU主频，从而提高CPU的运算速度。

&emsp;&emsp;令$T_{non-Pipeline}$表示程序的非流水式执行时间，$T_{Pipeline}$表示程序的流水式执行时间，$N$表示流水线级数，则在理想情况下，流水线处理器的执行执行时间可用以下公式计算：

$$T_{Pipeline} = \frac{T_{non-Pipeline}}{N}$$



## 1. 流水线划分

### 1.1 流水级的划分原则

&emsp;&emsp;在设计流水线处理器时，流水级（Pipeline Stage）的划分直接决定了流水线是否高效。在划分流水线时，通常需要从 **功能**、**延时**、**位宽** 三个方面进行考虑。

#### 1.1.1 按功能划分

&emsp;&emsp;如果电路本身具有若干个明确的子功能，或其工作时存在若干个逻辑上具备先后关系的阶段，则可以将各项子功能或各个阶段分别划分成一个流水级。

!!! example "举个栗子 :chestnut:"
    &emsp;&emsp;例如，CPU执行执行指令的过程可以分成取指、译码、执行、访存、写回五个阶段，因此常常会将流水线划分成对应的五个流水级。

#### 1.1.2 按延时划分

&emsp;&emsp;单周期CPU的主频取决于执行时间最长的指令（如Load指令）。同理，流水线CPU的主频取决于耗时最长的流水级。因此，在设计流水线时，应尽量使得每个流水级的延时相近，因为这样才能使处理器运行在更高的频率，从而获得更好的性能。

#### 1.1.3 按位宽划分

&emsp;&emsp;划分流水线时，需要增加流水线寄存器。为了节省寄存器的数量和面积，在设计流水线时，还需尽量使得各流水级之间的交互信号尽可能少。

!!! info "补充说明 :fire:"
    &emsp;&emsp;实际划分流水线时，一般需要同时考虑以上3项的流水线划分原则。

### 1.2 流水线实例

#### 1.2.1 RISC经典五级流水线

&emsp;&emsp;按照RISC处理器的经典五级流水结构，可将单周期CPU的执行阶段划分为如图1-1所示的五级流水线。

<center><img src = "../assets/1-1.png" width = 600></center>
<center>图1-1 五级流水线</center>

- **取指** 阶段（Instruction Fetch，**IF**）：是指将指令从指令存储器中读取出来的过程。

- **译码** 阶段（Instruction Decode，**ID**）：是指将取指阶段取出的指令进行翻译的过程。译码阶段将得到指令的操作码、功能码、操作数寄存器号等信息，然后从寄存器堆（Register File）取出操作数，同时产生指令执行所需的控制信号。

- **执行** 阶段（instruction EXecute，**EX**）：是指根据指令的操作码和功能码，对指令操作数进行运算的过程。如果指令是一条加法运算指令，则对操作数进行加法操作；如果是减法运算指令，则进行减法操作。CPU中的算术逻辑单元（Arithmetic Logical Unit，ALU）是实施具体运算的硬件功能单元，是执行阶段的核心部件。

- **访存** 阶段（MEMory access，**MEM**）：是指访存指令从存储器读出数据，或将数据写入存储器的过程。

- **写回** 阶段（Write Back，**WB**）：是指将指令执行结果写回到目标寄存器的过程。运算类指令的执行结果是执行阶段的输出，而访存指令的执行结果则是访存阶段从存储器读取出的数据。

#### 1.2.2 ARM10六级流水线

&emsp;&emsp;ARM10处理器将指令的执行过程划分成取指、分派、译码、执行、访存、写回六级流水线。

- 取指阶段（IF）：进行指令分支预测及读取指令。

- 分派阶段（instruction ISSue，ISS）：判断指令是否是协处理器指令。

- 译码阶段（ID）：根据指令格式解析指令，并从寄存器堆中取出源操作数。

- 执行阶段（EX）：使用ALU完成指令所需的运算操作。

- 访存阶段（MEM）：进行指令的访问数据存储器操作。

- 写回阶段（WB）：将指令执行结果写回到寄存器堆中的指定寄存器。

#### 1.2.3 ARM11八级流水线

&emsp;&emsp;<a href="https://developer.arm.com/documentation/ddi0360/f/?lang=en" target="_blank">ARM11处理器</a>将指令的执行过程划分成八级流水线。流水线的前端包括取指1、取指2、译码和分派四个阶段；流水线后端则分成了ALU、MAC（Multiply-ACcumulate，乘累加）和访存三条不同的子流水线，如图1-2所示。

<center><img src = "../assets/1-2.png"></center>
<center>图1-2 ARM11八级流水线结构</center>

- 取指阶段1（IF1）：进行动态分支预测及读取指令。

- 取指阶段2（IF2）：进行静态分支预测。

- 译码阶段（ID）：根据指令格式解析指令，并判断指令是否是协处理器指令。

- 分派阶段（ISS）：从寄存器堆中取出源操作数，并分派指令到相应的子流水线。

- 移位阶段（SHift，SH）：进行数据移位操作。

- 运算阶段（ALU）：进行整数的算术逻辑运算。

- 饱和阶段（SATuration，SAT）：对整数进行限幅操作。

- 执行写回阶段（Write Back execution，WBex）：乘法或主执行流水线（Main Execution Pipeline）的写回阶段——将运算结果写回到寄存器堆的指定寄存器。

- 乘累加阶段1（MAC1）：乘累加运算的第1个阶段。

- 乘累加阶段2（MAC2）：乘累加运算的第2个阶段。

- 乘累加阶段3（MAC3）：乘累加运算的第3个阶段。

- 地址生成阶段（ADDress Generation，ADDG）：通过计算产生访存操作地址。

- 数据缓存读取阶段1（Data Cache access，DC1）：访问数据Cache的第1个阶段。

- 数据缓存读取阶段2（Data Cache access，DC2）：访问数据Cache的第2个阶段。

- 访存写回阶段（Write Back from Load Store Unit，WBls）：访存结果的写回——将加载存储单元（Load Store Unit，LSU）的数据写回到寄存器堆的指定寄存器。



## 2. 流水线的段寄存器

&emsp;&emsp;理想情况下，流水线的各个阶段总是在执行不同的指令。为了保证指令获得正确的执行结果，需要在相邻的两个执行阶段之间添加寄存器，用于暂存指令执行的中间结果，这些寄存器被称为流水线的段寄存器（或流水线寄存器），如图1-3所示。

<center><img src = "../assets/1-3.png"></center>
<center>图1-3 流水线的段寄存器</center>

&emsp;&emsp;流水线寄存器的主要作用是：

&emsp;&emsp;（1）暂存流水级的输出信息，交给下一级处理，并共享给其他指令；

&emsp;&emsp;（2）切割单周期处理器的组合逻辑，降低指令的执行时延，提升CPU时钟频率。

&emsp;&emsp;下面以IF-ID-EX-MEM-WB的五级流水结构为例，对段寄存器的设计进行简要介绍。

- **IF/ID流水寄存器**

&emsp;&emsp;取指级和译码级之间的段寄存器用于传递取出的指令及PC值等，如图1-4所示。

<center><img src = "../assets/1-4.png" width = 110></center>
<center>图1-4 IF/ID流水寄存器</center>

&emsp;&emsp;IF/ID流水寄存器的参考RTL实现如图1-5所示。

``` Verilog
1|    always @ (posedge clk or negedge rst_n) begin
2|        if (~rst_n) id_pc <= 32'h0;
3|        else        id_pc <= if_pc;
4|    end
5| 
6|    always @ (posedge clk or negedge rst_n) begin
7|        if (~rst_n) id_inst <= 32'h0;
8|        else        id_inst <= if_inst;
9|    end
```

<center>图1-5 IF/ID流水寄存器参考实现</center>

!!! hint "小提示 :bulb:"
    &emsp;&emsp;流水线的段寄存器是一个概念上的寄存器，其实现形式不定 —— 可以仅用一个若干位的寄存器实现，也可以用若干个“零散的、较小的”寄存器凑在一起形成一个“完整的”段寄存器。

- **ID/EX流水寄存器**

&emsp;&emsp;译码级和执行级之间的段寄存器用于传递当前指令的操作类型、立即数、源操作数及各控制信号，如图1-6所示。

<center><img src = "../assets/1-6.png" width = 120></center>
<center>图1-6 ID/EX流水寄存器</center>

- **EX/MEM流水寄存器**

&emsp;&emsp;执行级和访存级之间的段寄存器用于传递当前指令的访存相关控制信号以及ALU计算结果等，如图1-7所示。

<center><img src = "../assets/1-7.png" width = 120></center>
<center>图1-7 EX/MEM流水寄存器</center>

- **MEM/WB流水寄存器**

&emsp;&emsp;访存级和写回级之间的段寄存器用于传递当前指令的写回结果，如图1-8所示。

<center><img src = "../assets/1-8.png" width = 95></center>
<center>图1-8 MEM/WB流水寄存器</center>

!!! warning "请注意 :gun:"
    &emsp;&emsp;上述流水线寄存器仅为示例，同学们<u>需根据自己的设计来决定流水线的级数和段寄存器的结构、存储的信息等</u>。
