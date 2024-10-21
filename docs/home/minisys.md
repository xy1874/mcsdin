## 1. Minisys简介

&emsp;&emsp;本课程使用Xilinx的Minisys开发板作为实验平台。Minisys开发板采用型号为<font color = blue>**XC7A100TFGG484-1**</font>的主芯片，具有15850个逻辑Slice、135个36Kb的Block RAM存储器、240个DSP48E1数字信号处理器、6个时钟管理模块，内部时钟最高可达450MHz。

&emsp;&emsp;Minisys开发板的板上时钟源是频率为100MHz的晶振时钟，该时钟源连接到FPGA的`Y18`引脚。复位按钮是板上的S6按键开关，连接到FPGA的`P20`引脚，其电路图如图1所示。显然，按键开关S6是 **高电平复位**。

<center><img src = "../assets/b-1.png" width = 250></center>
<center>图1 复位键的电路图</center>

&emsp;&emsp;更多关于Minisys开发板的信息，请看首页实验包下载链接中的《Minisys开发板硬件手册》。

## 2. 使用注意事项

### 2.1 Minisys的版本区别

&emsp;&emsp;Minisys开发板有<u>老版</u>和<u>新版</u>之分，两个版本的主要区别是老版采用Micro USB接口（`J22`）作为JTAG下载口，且下载速度较慢；新版采用USB Type-C接口（`J8`）作为JTAG下载口，且下载速度较快，如图2所示。

<center><img src = "../assets/b-2a.png" width = 500></center>
<center>图2 a) 老版Minisys使用Micro USB接口</center>

<center><img src = "../assets/b-2b.png" width = 500></center>
<center>图2 b) 新版Minisys使用USB Type-C接口</center>

&emsp;&emsp;==**老版的Minisys开发板必须使用外置的<font color = red>5V</font>电源供电。**==

<!-- &emsp;&emsp;新版可以通过电源开关旁边的跳线帽`PWR_SLC`选择供电方式 —— 当跳线帽接到上方的“USB”处，则使用USB供电；当跳线帽接到下方的“J10_IN”处，则使用独立的5V电源供电，如图3所示。

<center><img src = "../assets/b-3.png" width = 350></center>
<center>图3 跳线帽选择供电方式</center>

!!! warning "注意！ :loudspeaker:"
    &emsp;&emsp;（1）老版和新版的电源电压虽然都是5V，但二者的电源接口物理规格不同，**禁止混用电源适配器**。  
    &emsp;&emsp;（2）对于课程实验，一般使用USB供电即可。  
    &emsp;&emsp;（3）跳线帽很小，容易丢失。**非必要时，禁止拔出跳线帽！**  
    &emsp;&emsp;（4）更换供电方式时，需<u>先将Minisys开发板放置在干净整洁无杂物的台面上</u>，防止因意外丢失跳线帽，或使跳  
    &emsp;&emsp;线帽跌落地面沾上灰尘从而污染开发板。 -->

### 2.2 注意事项

<!-- &emsp;:star: T2506座位有 **38块**、T2612有 **42块**、T2615有 **44块** Minisys开发板。 -->

&emsp;:star: T2506、T2612的开发板全都是Type C接口的版本；T2615过道左侧均为Micro USB的版本，过道右侧均为Type C接口的版本。

&emsp;:star::star: **禁止将开发板带出实验室！**

&emsp;:star::star: 开发板如果装在防静电袋的，使用完毕后，务必 **将开发板按原样装回防静电袋**。

&emsp;:star::star: **禁止随意丢弃防静电袋！** 防静电袋的正确装袋方式如图4所示。

<center><img src = "../assets/b-4.png" width = 350></center>
<center>图4 开发板的正确装袋方式</center>

&emsp;:star: **禁止将USB线和开发板同时装入防静电袋！** 袋中应只装开发板，USB线应单独存放。

<!-- &emsp;:star::star: 对于T2507和T2608，请 **将电源适配器和USB线单独放在** 大纸箱旁边的 **小纸箱**！ -->

&emsp;:star::star::star: <u>老版Minisys的Micro USB接口牢固性比较差</u>，**插拔USB线时请温柔操作，否则可能损坏接口！**

<!-- &emsp;:star::star: 将开发板放回大纸箱时，**严禁将开发板堆叠！** 堆叠容易损坏开发板上的元器件。 -->

<!-- &emsp;:star::star: 将开发板放回大纸箱时，不管有无防静电袋，都应当 **将有拨码开关的一边朝下，竖立放置**，如图5所示。 -->

<!-- <center><img src = "../assets/b-5.png" width = 350></center>
<center>图5 开发板放置时，拨码开关一侧朝下，竖立放置</center> -->

&emsp;:star::star: 下板调试完毕或下课时，必须将开发板按要求装好在防静电袋里，并放回原处。

&emsp;:star::star: **禁止用手接触电路板上的芯片、金属线路等！** 人手上有静电、汗液和灰尘，会损伤甚至损坏开发板。

&emsp;:star: 使用开发板时，**注意防水防尘，禁止在旁边饮食！**
