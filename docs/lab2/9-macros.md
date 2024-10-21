&emsp;&emsp;不管是使用高级语言进行软件开发，还是使用HDL进行硬件设计，都要尽量遵循代码可配置的原则。对于常量或参数，应当在代码中使用宏定义的方式来引用。使用宏定义，不仅可以保证代码具有良好的可配置性，还能提高代码的规范性和可读性。

&emsp;&emsp;我们在“控制单元设计”的一节中，设计了如表5-2所示的控制信号取值表。应该注意到，明明是控制信号的取值表，表中的npc_op和alu_op却用字符串作为信号取值，显得“格格不入”。事实上，npc_op和alu_op的取值就是以宏定义的方式来表示的。将npc_op、alu_op与其他控制信号相比，可以发现宏定义的表达方式不仅更方便阅读、更容易为人所理解，而且有利于代码维护。

&emsp;&emsp;下面简要介绍如何在Verilog HDL中使用宏定义：

&emsp;&emsp;(1) 新建一个独立的.v文件 (如param.v)，用来编写宏定义；

&emsp;&emsp;(2) 使用``define`关键字定义宏参数，如图10-1所示。

``` Verilog
 1|// file: param.v
 2|`ifndef CPU_PARAM
 3|`define CPU_PARAM
 4|
 5|    // syntax: `define <macro name> <parameter>
 6|    `define ADD     'b000
 7|    `define SUB     'b001
 8|    `define OR      'b010
 9|    `define AND     'b011
10|    ......
11|
12|    `define STATE_IDLE 'b0001
13|    `define STATE_WRIT 'b0010
14|    `define STATE_WORK 'b0100
15|    `define STATE_RETU 'b1000
16|    ......
17|
18|`endif
```
<center>图10-1 Verilog HDL定义宏参数的语法</center>

&emsp;&emsp;(3) 在Vivado中，将宏定义文件 (param.v) 设置成全局引用即可，如图10-2所示。

<center><img src = "../assets/10-2.png" width = 400></center>
<center>图10-2 设置宏定义文件为全局引用</center>
