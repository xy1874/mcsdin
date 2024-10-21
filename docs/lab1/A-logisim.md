## 1. Logisim运行环境

&emsp;&emsp;运行Logisim需要安装10.0以上的Java运行环境，否则无法支持高分辨率屏幕。

&emsp;&emsp;Java SE下载链接：<a href="https://www.oracle.com/java/technologies/javase-downloads.html" target="_blank">Java Downloads-ORACLE</a>。

## 2. Logisim使用教程

&emsp;&emsp;Logisim使用教程可以参考链接：<a href="http://www.cburch.com/logisim/docs.html" target="_blank">Logisim Documentation</a>。

## 3. Logisim使用技巧

- ++ctrl+r++：电路复位

- ++ctrl+t++：时钟单步

- ++ctrl+k++：时钟连续

- 方向键：改变组件朝向

- 戳工具（Poke Tool）：点击组件可以直接查看组件的值，如引脚的值、寄存器的值等。使用戳工具单击连线段可显示连线当前的值。此外，利用戳工具点击标签连接的线路，所有联通在一起的线路都会显示出来，可以很方便找到同名的标签。

- 编辑工具（Edit Tool）：允许用户重新安排现有组件，修改组件属性并添加连线。

## 4. Logisim电路连线颜色区分

- 绿色：表示高电平

- 蓝色：表示未知状态

- 灰色：表示飞线

- 红色：表示信号冲突，线携带错误值。这通常是因为门电路不能确定正确的输出，也许是因为它没有输入（此时线是正常情况）。也可能是因为两个组件试图向电线发送不同的值，例如：一个输入引脚将 0 置于导线上，另一个将 1 置于同一导线上，引起冲突。当任何比特携带错误值时，多比特线将变为红色。

- 黑色：表示多位总线

- 橙色：表示位宽不匹配