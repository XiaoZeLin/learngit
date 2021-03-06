
#Lab3:DOL实例分析&编程##
_ _ _
#####概要#####

首先，这次实验任务是理解DOL的example文件，并在此基础上进行修改。掌握DOL的eaxmple文件结构和实现过程，并把修改后的结果截图，分析修改的思路。

##### EXAMPLE文件的组成

每个example文件主要是由.c文件、.h文件和.xml文件组成。其中，.c文件是c语言代码文件，负责实现具体的功能；.h文件是.c文件的头文件，定义了相关的全局变量和宏定义；.xml文件是定义各个不同的.c文件是如何按顺序执行，规定了信息变量是如何在进程之间传递。


##### ECAMPLE1文件分析和修改
一开始，example 1 文件的.c部分是由generater.cpp、consumer.cpp和square.cpp组成：generater.cpp是用循环输出变量i（i是从0到9的十个数），然后square.cpp是将接收的的i进行平方处理再传递给consumer.cpp，consumer.cpp是负责把i的平方结果输出到屏幕。
所以，要做的任务是将i的平方计算改为计算i的立方，并将结果显示出来。因此，只需将square.cpp文件以下代码：
```
i = i*i;     //平方
```
改为：
```
i = i*i*i;   //立方
```
输出结果：


![example1平方](https://github.com/XiaoZeLin/photo/blob/master/example1.PNG)

##### ECAMPLE2文件分析和修改
example 2的每个cpp文件和.h文件与eaxmple 1文件的一样，只是.xml文件不一样：example 2的xml文件使用iterator使square.cpp执行了三次，得到i^8(即i的8次方)。这次任务是把square的执行次数改为两次，即要得到i的4次方的结果。原xml文件：
```
 <variable value="3" name="N"/>

  <!-- instantiate resources -->
  <process name="generator">
    <port type="output" name="10"/>
    <source type="c" location="generator.c"/>
  </process>

  <iterator variable="i" range="N">
    <process name="square">
      <append function="i"/>
      <port type="input" name="0"/>
      <port type="output" name="1"/>
      <source type="c" location="square.c"/>
    </process>
  </iterator>

  <process name="consumer">
    <port type="input" name="100"/>
    <source type="c" location="consumer.c"/>
  </process>

  <iterator variable="i" range="N + 1">
    <sw_channel type="fifo" size="10" name="C2">
      <append function="i"/>
      <port type="input" name="0"/>
      <port type="output" name="1"/>
    </sw_channel>
  </iterator>

```
上面这段代码创建了一个generater、一个consumer、N个square和N+1个channel（名为C2）。以上各模块需要连接的代码如下：
```
 <!-- instantiate connection -->
  <iterator variable="i" range="N">
    <connection name="to_square">
      <append function="i"/>
      <origin name="C2">
        <append function="i"/>
        <port name="1"/>
      </origin>
      <target name="square">
        <append function="i"/>
        <port name="0"/>
      </target>
    </connection>

    <connection name="from_square">
        <append function="i"/>
        <origin name="square">
          <append function="i"/>
          <port name="1"/>
        </origin>
        <target name="C2">
          <append function="i + 1"/>
          <port name="0"/>
        </target>
    </connection>
  </iterator>

  <connection name="g_">
    <origin name="generator">
     <port name="10"/>
    </origin>
    <target name="C2"> 
      <append function="0"/>
      <port name="0"/>
    </target>
  </connection>

  <connection name="_c">
    <origin name="C2">
      <append function="N"/>
      <port name="1"/>
    </origin>
    <target name="consumer">
      <port name="100"/>
    </target>
  </connection>

</processnetwork>
```
上述代码定义了各种模块连接，一开始是generater与c2相连，然后c2把所有的square连接起来，最后和consumer相连，因为在代码最开始的地方定义了iterator变量N等于3，所以会执行三次square，修改的代码为：
```
 <variable value="2" name="N"/>
```
Dot图片：

![dot](https://github.com/XiaoZeLin/Python_Opencv_Computer/blob/master/Dot.PNG)

输出结果：

![example2](https://github.com/XiaoZeLin/photo/blob/master/eample2.PNG)

##### 实验感想
其实这次试验并不是很难，只需要改动两个地方而已。不过需要理解模块与线之间是如何相连的，感觉理解这部分是最有趣的了！最后，由于不明白dot文件是什么和怎么生成就弄了好久。。。不过，实验完成的感觉挺好的。


_ _ _
