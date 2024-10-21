&emsp;&emsp;在前面的章节中，我们在虚拟机上使用Trace测试框架对CPU进行了功能上的仿真验证。在下板时，我们也可以使用Trace测试程序来调试，即将Trace比对测试的汇编程序导入IROM存储器并生成比特流下板运行。

&emsp;&emsp;本课程提供可在FPGA上运行Trace比对的汇编测试程序，该程序的源码见测试包的<a href="https://gitee.com/hitsz-cslab/cdp-tests/blob/miniRV/asm/start.dump" target="_blank">`cdp-tests / asm / start.dump`</a>。

!!! note "关于下板运行Trace的说明 :loudspeaker:"
    &emsp;&emsp;本节所介绍的下板运行Trace并非必做内容，而是一种在FPGA板上运行的调试手段。

## 1. DRAM访存地址修改

&emsp;&emsp;对于哈佛结构的CPU，汇编程序的代码段和数据段应当分别存放在IROM和DRAM。DRAM的基地址为`0x0000_0000`，而start.dump程序的数据段基地址为`0x0000_4000`。因此，为了使得CPU能够正确运行start.dump程序，我们需要将测试程序访问DRAM的地址减去`0x0000_4000`，即：

&emsp;&emsp;原DRAM实例化代码：

``` Verilog
1|    dram U_dram (
2|        .clk    (cpu_clk),
3|        .a      (waddr[15:2]),
4|        .spo    (rdata),
5|        .we     (we),
6|        .d      (wdata)
7|    );
```

&emsp;&emsp;修改后的DRAM实例化代码：

``` Verilog
1|    wire [31:0] waddr_tmp = waddr - 32'h4000;
2|    
3|    dram U_dram (
4|        .clk    (cpu_clk),
5|        .a      (waddr_tmp[15:2]),
6|        .spo    (rdata),
7|        .we     (we),
8|        .d      (wdata)
9|    );
```


<!-- ## 2. 存储器IP替换 -->
## 2. 导入测试程序

<!-- &emsp;&emsp;由于下板测试程序较大，导致导入.coe后重新综合工程耗时较长，因此课程指导书网站提供了已综合好的IROM和DRAM IP核，同学们可将其直接导入CPU工程中使用。

&emsp;&emsp;下面以导入DRAM为例，介绍导入已综合IP核的方法 (IROM同理)。 -->

<!-- &emsp;&emsp;首先，从指导书网站<a href="https://gitee.com/hitsz-cslab/cpu/blob/master/stupkt/download_test.zip" target="_blank">下载download_test.zip压缩包</a>文件并解压到无中文路径下。 -->

&emsp;&emsp;首先，从首页的课程材料下载链接中，下载onboard_trace.zip压缩包并解压。

<!-- &emsp;&emsp;然后，备份原工程，按照上一节所述修改RTL代码，并删除已实例化的IROM和DRAM IP核。 -->

&emsp;&emsp;然后，参照<a href="../../lab2/2-parts/#321-rom" target="_blank">实验2 - 3.2.1 定义程序ROM</a>，将`inst_rom.coe`和`data_ram.coe`分别导入到IROM和DRAM的存储器IP核中。

<!-- &emsp;&emsp;接着，按下图所示点击添加IP核。

<center><img src = "../assets/1.png" width = 450></center>

<center><img src = "../assets/2.png" width></center>

<center><img src = "../assets/3.png" width = 600></center>
<center>从解压的目录中添加已综合的DRAM IP核</center> -->

&emsp;&emsp;接着，运行综合、实现、生成比特流，最终下板运行Trace测试。



## 3. 测试结果说明

&emsp;&emsp;cdp-tests测试包提供了37条指令（24条必做和13条选做）的测试程序。每通过一个测试点，数码管显示的数值会加1，直至测试完毕。测试完毕时，数码管将以16进制形式显示测试总数和通过了的测试数量。

!!! example "举例说明 :chestnut:"
    &emsp;&emsp;如果实现了24条必做指令并通过了测试，则数码管将显示`0x25000018`。数码管高2位显示`0x25`，表示共有`37`个测试点，数码管低2位显示`0x18`，表示通过了`24`个测试点。

&emsp;&emsp;需要注意的是，对于测试不通过的情形，如果数码管显示的值停在n，则表示第n+1条指令的测试失败。测试失败时，可查看start.dump的测试代码以定位出错指令，并进行相应的调试。

&emsp;&emsp;在下板测试中，如果数码管显示卡在某一个功能点，没有继续计数，一种方法是采用虚拟机中的Trace测试框架来进一步定位错误点。

!!! warning "注意 :loudspeaker:"
    &emsp;&emsp;在虚拟机或远程平台运行Trace测试时，DRAM地址不需要减去0x4000；**只有在下板运行Trace时，DRAM地址才需要减去0x4000**。

    ``` Verilog
    1|    wire [31:0] waddr_tmp = waddr;// - 16'h4000;
    2|     
    3|    data_mem dmem (
    4|        .clk    (cpu_clk),
    5|        .a      (waddr_tmp[15:2]),
    6|        .spo    (rdata),
    7|        .we     (we),
    8|        .d      (wdata)
    9|    );
    ```

- ***Step1***：进入`cdp-test`目录，输入`make`命令以重新编译；

- ***Step2***：输入`make run TEST=start`以运行start测试。

&emsp;&emsp;例如，某次测试时报错，`debug_wb_pc`显示了`0x000018f8`，如下图所示。

<center><img src = "../assets/t-1.png" width = 400></center>

&emsp;&emsp;打开start.dump文件，找到PC值为`0x000018f8`的指令，即可定位到具体出错的指令，如下图所示。

<center><img src = "../assets/t-2.png" width = 650></center>

&emsp;&emsp;显然，在上述例子中，mycpu执行到auipc指令时出错。

&emsp;&emsp;如果在虚拟机的Trace测试框架中通过了测试，但下板运行Trace测试程序时出错，即出现仿真和下板不一致的情况，请参考<a href="../../home/problems/#36" target="_blank">常见问题汇总之3.6</a>。
