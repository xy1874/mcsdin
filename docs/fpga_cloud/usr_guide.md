# FPGA云实验平台

## 1. 远程FPGA平台使用指南

### 1.1 登录
- **实验平台地址**: <a href="http://10.249.8.212:10707/" target=_blank>10.249.8.212:10707</a>
- **初始用户名**: `学号`
- **初始密码**: `stu` + `学号`
- 例如学号为`2023311A01`, 用户名为`2023311A01`, 密码为`stu2023311A01`。

<center><img src = "../assets/login.png" width = 100%></center>

### 1.2 查看资源

- 登录后将看到所有实验资源。点击 “快速使用” 以申请资源。

<center><img src = "../assets/view_resource.png" width = 100%></center>

### 1.3 使用FPGA开发板
#### 1.3.1 上传比特流文件

- 请注意：**上传到本平台的.bit文件，必须是使用本平台专用模板工程生成的**。

- 选择.bit文件并上传, 点击 “选择bit文件”。

- .bit文件通常位于工程文件的`.runs/impl_1`文件夹中。

<center><img src = "../assets/choose_bit_file.png" width = 100%></center>
<center><img src = "../assets/bit_file_loc.png" width = 500></center>

#### 1.3.2 烧录上板

- 点击 “上传bit文件并连接”, 等待烧录成功。

<center><img src = "../assets/upload_bit_file.png" width = 100%></center>

- 烧录成功后，网页将弹出成功提示。

<center><img src = "../assets/program_success.png" width = 100%></center>

#### 1.3.3 实验功能测试

- 点击按钮打开开关，观察开发板运行的显示效果。

<center><img src = "../assets/turn_on.png" width = 100%></center>

- 根据实验需求操作按钮。

<center><img src = "../assets/buttons.png" width = 100%></center>

- 观察实验结果。

<center><img src = "../assets/show_result.png" width = 100%></center>

### 1.4 释放资源

- 目前平台尚在逐步完善中，**若想再次上传比特流，需先点击 “退出并释放FPGA”，然后重新点击 “快速使用” 按钮，并重复<a href="#13-fpga" target=_blank>1.3小节</a>的操作**。

<center><img src = "../assets/release_resource.png" width = 100%></center>



## 2. 模板工程使用指南
### 2.1 解压模板工程

- 下载并解压模板工程<a href="https://pan.baidu.com/s/18VboNLgQD2MgVs-_pDCPbA?pwd=qns9" target=_blank>remoteFPGA.zip</a>，进入工程目录，双击.xpr文件打开Vivado工程。

<center><img src = "../assets/open.png" width = 400></center>

### 2.2 在工程中添加自己的模块

- 点击Add Sources创建自己的.v文件。

<center><img src = "../assets/newmodule.png" width = 100%></center>
<center><img src = "../assets/newdff.png" width = 600></center>

- 在.v文件中编写自己的代码。此处以实验1的D触发器为例。

<center><img src = "../assets/owncode.png" width = 100%></center>

- **除了编写自己的设计文件、仿真文件，并在top.v中实例化之外，不要修改其他文件（包括约束文件）**。

### 2.3 在top.v中实例化自己的模块

- 在top.v文件中实例化自己的模块。

<center><img src = "../assets/whereinstantiate.png" width = 550></center>
<center><img src = "../assets/instantiate.png" width = 550></center>

- 实例化方法：参考dff的实例化，接好信号。小括号中的信号是模块接口信号，将自己的模块的输入输出信号连接到top中的信号。

- 在top中，
    - LED灯对应`led[23:0]`
    - 拨码开关对应`switch[23:0]`
    - 五个按键对应`button[4:0]`
    - 4x4键盘的行列信号分别对应`keyboard_row[3:0]`和`keyboard_col[3:0]`
    - 数码管的段选信号对应`seg_ca`, `seg_cb`, `seg_cc`, `seg_cd`, `seg_ce`, `seg_cf`, `seg_cg`, `seg_dp`，位选信号对应`seg_en`

- 这些信号在工程中已经通过约束文件约束到对应管脚，故在使用时，你 **只需在top.v中将你的模块的输入输出信号连接到这些信号即可，无需再额外编写约束文件，也不要修改模板工程的约束文件**。

- 实例化时，**未使用的LED或数码管信号，需将其置为无效值**，如上图蓝色框所示。
    - 对于Minisys开发板，LED是高电平有效，故无效值是0；
    - 数码管的段选信号、位选信号均为低电平有效，故无效值是1。

### 2.4 生成比特流文件

- 按正常的操作流程，完成综合 (Synthesis)、实现 (Implementation)、生成比特流文件 (Bitstream) ，或直接点击“Generate Bitstream”按钮，此时Vivado将自动依次执行综合、实现、生成比特流文件的步骤。

<center><img src = "../assets/generatebitstream.png" width = 100%></center>

### 2.5 上传比特流文件

- 将生成的比特流文件上传到远程实验平台。

- 上传并连接后，当拨动拨码开关SW0和SW23时，LED0成功亮起，D触发器实验成功。

<center><img src = "../assets/test.png" width = 100%></center>



## 3. Bug反馈

- 请将使用过程中碰到的Bug填入到以下文档。
    - 请描述清楚Bug的 **现象** 及 **复现过程**，必要时 **附上截图说明**。
    - 如果可能，请留下联系方式，方便开发者联系并沟通具体情况。

<embed src = "https://docs.qq.com/doc/DZFdBZGtYU3NwenVq" width="100%" height="600">
