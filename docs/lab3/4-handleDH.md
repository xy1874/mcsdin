## 1. 数据冒险的检测

&emsp;&emsp;要想解决流水线处理器中的数据冒险问题，首先需要在指令流中检测出数据冒险。

&emsp;&emsp;下面以图4-1为例介绍RAW冒险三种情形的检测方法。

<center><img src = "../assets/4-1.png" width = 600></center>
<center>图4-1 RAW数据冒险的三种情形</center>

### 1.1 RAW情形A检测

&emsp;&emsp;相邻指令发生RAW冒险 —— 图4-1中，第2条指令在译码阶段访问的寄存器与第1条指令在执行阶段将要写回的寄存器冲突。

&emsp;&emsp;流水线CPU中，与情形A有关的数据通路如图4-2所示。

<center><img src = "../assets/4-2.png" width = 600></center>
<center>图4-2 RAW情形A有关的流水线数据通路</center>

&emsp;&emsp;分析图4-2所示的数据通路，不难发现RAW情形A的判断方法/检测逻辑如下：

<center>
( REG~**ID/EX**~.RD == ID.RS1 ) || ( REG~**ID/EX**~.RD == ID.RS2 )
</center>

&emsp;&emsp;其中，REG~ID/EX~表示译码阶段和执行阶段之间的流水线寄存器，下同；REG~ID/EX~.RD表示ID/EX流水线寄存器的RD字段，观察图4-2可知该字段暂存的是从ID级传递而来的目标寄存器的寄存器号。

&emsp;&emsp;显然，对于图4-1中的情形A，有REG~ID/EX~.RD == ID.RS1 == `5'd19`。

### 1.2 RAW情形B检测

&emsp;&emsp;间隔1条指令发生RAW冒险 —— 图4-1中，第3条指令在译码阶段访问的寄存器与第1条指令在访存阶段将要写回的寄存器冲突。

&emsp;&emsp;流水线CPU中，与情形B有关的数据通路如图4-3所示。

<center><img src = "../assets/4-3.png" width = 600></center>
<center>图4-3 RAW情形B有关的流水线数据通路</center>

&emsp;&emsp;分析图4-3所示的数据通路，不难发现RAW情形B的判断方法/检测逻辑如下：

<center>
( REG~**EX/MEM**~.RD == ID.RS1 ) || ( REG~**EX/MEM**~.RD == ID.RS2 )
</center>

&emsp;&emsp;显然，对于图4-1中的情形B，有REG~EX/MEM~.RD == ID.RS1 == `5'd19`。

### 1.3 RAW情形C检测

&emsp;&emsp;间隔2条指令发生RAW冒险 —— 图4-1中，第4条指令在译码阶段访问的寄存器与第1条指令在写回阶段将要写回的寄存器冲突。

&emsp;&emsp;流水线CPU中，与情形C有关的数据通路如图4-4所示。

<center><img src = "../assets/4-4.png" width = 690></center>
<center>图4-4 RAW情形C有关的流水线数据通路</center>

&emsp;&emsp;分析图4-4所示的数据通路，不难发现RAW情形C的判断方法/检测逻辑如下：

<center>
( REG~**MEM/WB**~.RD == ID.RS1 ) || ( REG~**MEM/WB**~.RD == ID.RS2 )
</center>

&emsp;&emsp;显然，对于图4-1中的情形C，有REG~MEM/WB~.RD == ID.RS1 == `5'd19`。

!!! hint "参考实现 :sparkles:"
    &emsp;&emsp;对于RAW情形C，给出检测逻辑的参考实现代码：

    ``` Verilog
    1|    wire rs1_id_wb_hazard = (wb_rd == id_rs1) & wb_we & id_rf1;
    2|    wire rs2_id_wb_hazard = (wb_rd == id_rs2) & wb_we & id_rf2;
    ```
    <center>图4-5 检测RAW冒险情形C的RTL参考实现</center>

    &emsp;&emsp;其中，`wb_rd`表示WB阶段的目标寄存器的寄存器号；`wb_we`表示WB阶段的写使能信号；`id_rs1`表示ID阶段的RS1寄存器号；`id_rf1`表示ID阶段的RS1寄存器的<u>读标志信号</u>，该信号用于标识ID阶段的RS1是否被读取。
    
    &emsp;&emsp;特别注意的是，此处增加`id_rf1`和`id_rf2`信号是为了避免I型、U型和J型指令执行时出现RAW冒险的误判。

&emsp;&emsp;请同学们参考上述实现代码，自行完成RAW三种情形的检测逻辑。

&emsp;&emsp;添加了数据冒险检测逻辑的流水线CPU数据通路形如图4-6所示。

<center><img src = "../assets/4-6.png"></center>
<center>图4-6 具有数据冒险检测功能的流水线CPU数据通路示例</center>

## 2. 数据冒险解决方法

### 2.1 流水线暂停

&emsp;&emsp;流水线发生数据冒险时，最朴素的解决方法就是暂停后续的指令，等待前面的指令执行完成，再重新让后续的指令执行。

!!! example "举栗说明 :chestnut:"
    &emsp;&emsp;对于如图4-1所示的RAW情形A，可让第二条指令暂停3个时钟周期，等待第一条指令写回后，再继续执行，如图4-7所示。

    <center><img src = "../assets/4-7.png" width = 600></center>
    <center>图4-7 流水线暂停</center>

&emsp;&emsp;流水线暂停除了可以解决数据冒险问题，还可以用于实现复杂操作指令，如乘除法指令。

!!! info "PS :mega:"
    &emsp;&emsp;乘除法指令需要通过硬件乘法器和硬件除法器实现。硬件的乘除法通常需要多个时钟周期才能完成。对于32bit的“IF-ID-EX-MEM-WB”的五级流水CPU，假设采用两位编码的Booth算法实现乘法器，乘法操作至少也需要16个时钟周期才能完成。因此，在不改变流水线深度的前提下，需要借助流水线暂停来延长执行阶段，才能实现乘除法指令。

    &emsp;&emsp;（1）硬件乘法

    &emsp;&emsp;可采用两位编码的Booth算法实现乘法器，相应的乘法器架构如图4-8所示。使用该乘法器实现32bit乘法，需要17个时钟周期。

    <center><img src = "../assets/4-8.png" width = 340></center>
    <center>图4-8 两位编码Booth乘法器架构图</center>

    &emsp;&emsp;（2）硬件除法

    &emsp;&emsp;可采用加减交替算法实现除法器，相应的除法器架构如图4-9所示。使用该除法器实现32bit除法，需要32个时钟周期。

    <center><img src = "../assets/4-9.png" width = 380></center>
    <center>图4-9 加减交替法除法器架构图</center>

&emsp;&emsp;要使流水线的执行暂停下来，只需暂停相关寄存器的更新即可，即：

&emsp;&emsp;（1）保持取指地址（即PC寄存器的值）不变；  

&emsp;&emsp;（2）保持各个流水线寄存器（IF/ID、ID/EX、EX/MEM、MEM/WB）不变；

&emsp;&emsp;暂停寄存器更新的方法是：<u>将寄存器的输入修改为寄存器自身的输出</u>。

&emsp;&emsp;添加了流水线暂停机制的流水线CPU数据通路形如图4-10所示。

<center><img src = "../assets/4-10.png"></center>
<center>图4-10 具有暂停功能的流水线CPU数据通路示例</center>

!!! hint "参考实现 :sparkles:"
    &emsp;&emsp;图4-9中的虚线表示使用生成的暂停信号暂停流水线寄存器。限于空间，具体的暂停逻辑未画出。对于IF/ID寄存器，其暂停逻辑的参考RTL实现如图4-11所示。
    
    ``` Verilog
     1|    always @(posedge clk or negedge rst_n) begin
     2|        if (~rst_n)             id_pc <= 32'h0;
     3|        else if (pipeline_stop) id_pc <= id_pc;
     4|        else                    id_pc <= if_pc;
     5|    end
     6|
     7|    always @(posedge clk or negedge rst_n) begin
     8|        if (~rst_n)             id_inst <= 32'h0;
     9|        else if (pipeline_stop) id_inst <= id_inst;
    10|        else                    id_inst <= if_inst;
    11|    end
    12|    
    13|    ......
    ```
    <center>图4-11 暂停IF/ID寄存器的RTL参考实现</center>

    &emsp;&emsp;请同学们参考上述实现，自行完成相关寄存器的暂停操作。

### 2.2 数据前递

&emsp;&emsp;数据<u>前递</u>又称数据<u>前推</u>、数据<u>旁路</u>、数据<u>转发</u>，指的是将某个流水线阶段的中间结果向前传递到前面的阶段，且被前递的数据直接参与前面阶段中的操作。

!!! example "举个栗子 :chestnut:"

    &emsp;&emsp;对于图4-1所示的RAW冒险，可采用数据前递方法解决 —— 将执行阶段、访存阶段或写回阶段的中间结果前递给译码阶段，并让前递而来的数据参与到指令译码时的源操作数选择，如图4-12所示。

    <center><img src = "../assets/4-12.png" width = 600></center>
    <center>图4-12 数据前递示意图</center>

    &emsp;&emsp;图4-12中，从EXE指向ID、从MEM指向ID以及从WB指向ID的三条紫色箭头，分别对应图4-1中RAW冒险的三种情形。第一条指令在执行阶段即可产生计算结果（x19寄存器的新值），此时通过执行阶段到译码阶段的数据前递，可让第二条指令在译码时获得x19的新值，从而在不需暂停流水线的情况下解决了第一、二条指令之间的RAW冒险。

&emsp;&emsp;添加了数据前递机制的流水线CPU数据通路形如图4-13所示。

<center><img src = "../assets/4-13.png"></center>
<center>图4-13 增加数据前递机制的流水线CPU数据通路</center>

!!! hint "参考实现 :sparkles:"
    &emsp;&emsp;译码阶段被前递而来的数据，需要加入到源操作数的选择逻辑当中，相应的RTL参考实现如图4-14所示。

    ``` Verilog
     1|    wire rs1_id_wb_hazard = (wb_rd == id_rs1) & wb_we & id_rf1;
     2|    wire rs2_id_wb_hazard = (wb_rd == id_rs2) & wb_we & id_rf2;
     3|
     4|    always @(posedge clk or negedge rst_n) begin
     5|        if (~rst_n)                 op_datA <= 32'h0;
     6|        else if (rs1_id_wb_hazard)  op_datA <= forward_dat;
     7|        else                        op_datA <= original_opA;
     8|    end
     9|
    10|    always @(posedge clk or negedge rst_n) begin
    11|        if (~rst_n)                 op_datB <= 32'h0;
    12|        else if (rs2_id_wb_hazard)  op_datB <= forward_dat;
    13|        else                        op_datB <= original_opB;
    14|    end
    ```
    <center>图4-14 译码阶段前递数据处理的RTL参考实现</center>

&emsp;&emsp;流水线暂停和数据前递并不冲突，有时甚至需要配合使用。

!!! question "思考题 :star2:"
    &emsp;&emsp;假设将图4-1中的第一条指令换成`lw x19, 0(x1)`，考虑此时的RAW情形A，能否使用数据前递解决？

### 2.3 乱序执行

&emsp;&emsp;流水线发生数据冒险时，除了暂停和前递，还可以让后续的无相关关系的指令先执行，使得冒险问题自然而然地解决。

&emsp;&emsp;实现乱序执行的基本思路是，检测指令流中是否存在冒险；若存在冒险，则让后续的无相关指令先执行。乱序执行<u>既可通过硬件动态实现，也可以通过编译器或汇编器静态实现</u>。

&emsp;&emsp;典型的乱序执行算法包括<u>记分牌算法</u>和<u>Tomasulo算法</u>等，其硬件实现方法较为复杂，此处不作详细介绍。感兴趣的同学们可自行查阅相关资料。

&emsp;&emsp;对乱序执行感兴趣的同学，建议先尝试通过汇编器静态实现。
