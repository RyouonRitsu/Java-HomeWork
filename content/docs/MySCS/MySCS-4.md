---
weight: 204
math: true
---

# MySCS-4

编写好的代码需要使用patpat进行评测，AC后即可上传到云平台以完成本次的实验内容。希望大家认真且保质保量的完成，请勿抄袭，**如有发现抄袭现象将取消迭代作业成绩**。



## 题目背景

软件学院云平台（青春版），即MySCS，是一款更加轻量化的命令行操作的云平台，屏幕前的你拥有完全的自主知识产权，有极大的使用价值和收藏价值。本平台模拟一台操作机上的命令行，所有操作均在本地进行，不支持联网操作，同一时间的使用者有且仅有一个。

本次迭代主要依托于MySCS2，仅涉及MySCS3中学生选择课程相关的操作。



## 整体约束

本次迭代将不再重复考察此前类关系设计、错误处理等，而是将重点放置在对面向对象特性的考察。MYSCS4题目复杂性大大降低。如果同学们能够认真完成Lab，实现起来将会非常轻松。在本次题目中，我们将对程序实现提出一些具体类设计提示（当然这也是为了辅助同学们能够更轻松地实现题目需求），若同学们基于自己的类设计能有更好的实现也当然可以。

**注意：** 你不需要考虑任何错误数据，评测保证所有数据均为合法数据。




## 总体说明


### 你需要了解的：

**序列化与反序列化**

- Java 序列化就是指把 Java 对象转换为字节序列的过程

- Java 反序列化就是指把字节序列恢复为 Java 对象的过程。

序列化最重要的作用：在传递和保存对象时，保证对象的完整性和可传递性。对象转换为有序字节流，以便在网络上传输或者保存在本地文件中。

反序列化的最重要的作用：根据字节流中保存的对象状态及描述信息，通过反序列化重建对象。

总结：核心作用就是对象状态的保存和重建。（整个过程核心点就是字节流中所保存的对象状态及描述信息）

在本次迭代中，我们简化了传递这一过程。在本机中，由 A 学生将自己的虚拟机对象保存为序列化文件，再由 B 学生读取序列化文件，并反序列化生成虚拟机对象并持有。

**可供参考：**

理论：https://blog.csdn.net/weixin_45433031/article/details/115364830

实践：https://www.cnblogs.com/com-Jacob/p/16207339.html

> 例如你实例化了一个对象，名字为小明，此时这个小明对象只在你程序运行期间存在，运行结束后就消失了，但是你可以通过序列化，将小明对象转换为字节序列（在运行期间这个对象本质也只是一串数据）存储到本地文件中，之后即可通过反序列化将这个文件中存储的字节序列转换回一个名字叫小明的对象。



## 命令概览

| 命令                 | 功能描述                      |
| -------------------- | ----------------------------- |
| `requestVM 系统类型` | [申请虚拟机](#申请虚拟机)     |
| `startVM`            | [启动虚拟机](#启动虚拟机)     |
| `clearVM 机器编号`   | [清空虚拟机](#清空虚拟机)     |
| `logVM`              | [查看历史指令](#查看历史指令) |
| `uploadVM 路径`      | [上传虚拟机](#上传虚拟机)     |
| `downloadVM 路径`    | [下载虚拟机](#下载虚拟机)     |



## 功能描述

### 申请虚拟机

| 命令        | 参数       |
| ----------- | ---------- |
| `requestVM` | `系统类型` |

- 使用对象：**学生端**

- 功能：根据系统类型在当前课程下申请虚拟机，其中系统类型仅有三种可能：`Windows`, `Linux`, `MacOS`。

- 说明：每个学生在同一课程中仅能拥有一台虚拟机，若申请了新的虚拟机或者下载虚拟机（后续命令）则会直接覆盖当前拥有的虚拟机。

- 前置条件：已登录、已选择课程

- 成功输出

    ```bash
    requestVM success
    ```

    > 例如 A 学生在选择了课程 C2101 之后，可通过 requestVM Windows 指令来申请一台 Windows 系统的虚拟机对象。但是他可能用不太习惯，于是就又通过 requestVM Linux 指令来申请一台 Linux 系统的虚拟机对象覆盖掉原有的 Windows 系统。你可以考虑为学生维护一个课程到虚拟机的单值映射，并在该学生选择课程后，在该映射中查找到对应的虚拟机对象（若存在的话）。
    
- **提示**：首先创建一个虚拟机类（接口、抽象类均可）并继承或实现`Serializable`接口（`java.io.Serializable`）。并实现三个子类，分别代表`Windows`, `Linux`, `MacOS`三个系统，你不能通过为某一属性赋值为`Windows`, `Linux`, `MacOS`来显式标志系统类型。创建一个虚拟机工厂接口，在课程类中通过匿名内部类来实现该接口，并以此来创建虚拟机对象。




### 启动虚拟机

| 命令      |
| --------- |
| `startVM` |

- 使用对象：**学生端**

- 功能：启动自己在当前选择课程所拥有的虚拟机，首先打印开始提示，进入指令输入模式。此时输入的任何字符串都会被存储到虚拟机对象中，直至输入`EOF`，标志指令输入结束，打印结束提示。若当前未拥有虚拟机，则仅打印未拥有提示后结束。

- 前置条件：已登录、已选择课程

- 成功输出

    - 若当前拥有虚拟机，则首先打印：

        ```bash
        welcome to $系统类型
        ```

        接着进入指令输入模式。

        直到读取到输入为`EOF`后，打印：

        ```bash
        quit $系统类型
        ```

    - 若当前未拥有虚拟机，则打印：

        ```bash
        no VM
        ```

    

    示例：例如 A 学生在拥有了 Linux 虚拟机之后：

    ```bash
    输入：startVM
    输出：welcome to Linux
    输入：aaa
    输入：bbb
    输入：ccc
    输入：EOF
    输出：quit Linux
    ```

    此时你还需要在该 Linux 虚拟机对象中保存`aaa`,`bbb`,`ccc`这三条指令作为历史指令。

    > 你或许需要使用 instanceof 来获取虚拟机系统类型。



### 清空虚拟机

| 命令      | 参数       |
| --------- | ---------- |
| `clearVM` | `机器编号` |

- 使用对象：**老师端**

- 功能：清空在当前选择课程中指定**机器编号**的虚拟机的历史指令。虚拟机**机器编号**取决于它在该课程中的分配次序，例如第二台分配出去的虚拟机**机器编号**就是2。注意对于不同的课程而言，**机器编号**是独立的。

- 说明：评测数据保证清空的机器编号对应的虚拟机一定存在，但最好在程序中提供一定的检查，避免写出bug导致RE。

- 前置条件：已登录、已选择课程

- 成功输出

  ```bash
  clear $系统类型 success
  ```
  
- **提示**：由于`clearVM`命令的存在，最好能够在工厂创建虚拟机实例时，用列表按顺序存储下每次创建的虚拟机实例，从而可直接根据下标操作对应的虚拟机。


​	示例：此时老师在选择了课程 C2101 后，可通过命令`clearVM 2`来清空该课程分配的第二台虚拟机中的历史指令。假设还是此前的 A 同学，就他一个人在 C2101 中申请了两台机器，且第二台是 Linux 系统，则此时将会将他此时持有的该 Linux 虚拟机对象中的历史指令清空，并打印`clear Linux success`。



### 查看历史指令

| 命令    |
| ------- |
| `logVM` |

- 使用对象：**学生端**

- 功能：打印在当前选择课程下拥有的虚拟机中的所有历史指令。

- 前置条件：已登录、已选择课程

- 成功输出
  
  - 若当前拥有虚拟机，则打印虚拟机所有历史指令，如果存在历史指令的话。
  
  - 若当前未拥有虚拟机或历史指令为空，则打印：
  
    ```bash
    no log
    ```
  
- **提示**：注意在`startVM`命令中，会输入一些无交互性的指令，`logVM`会将这些历史指令打印，你需要将这些指令按行存储并打印，考虑在虚拟机中声明一个字符串列表来存储，这也是区别不同虚拟机的关键数据。


​	示例：

- 还是我们此前提到的 A 同学，在他的虚拟机被老师清空前：

```bash
输入：logVM
输出：aaa
输出：bbb
输出：ccc
```

- 在被清空之后或他还未拥有虚拟机时：

```bash
输入：logVM
输出：no log
```



### 上传虚拟机

| 命令       | 参数   |
| ---------- | ------ |
| `uploadVM` | `路径` |

- 使用对象：**学生端**

- 功能：将当前拥有的虚拟机序列化存储到`路径`。若路径文件不存在则创建，若存在则覆盖。

- 说明：关于如何进行序列化可参考**总体说明**中的**实践**参考（真的只有几行代码）。评测数据保证上传的虚拟机一定存在，且路径合法。

- 前置条件：已登录、已选择课程

- 成功输出
  
  ```bash
  uploadVM success
  ```

>  你可以参考文章中的写法，实际无需去考虑给定的路径格式，且该实现即为覆盖写入。



### 下载虚拟机

| 命令         | 参数   |
| ------------ | ------ |
| `downloadVM` | `路径` |

- 使用对象：**学生端**

- 功能：将`路径`对应文件反序列化得到虚拟机对象并持有。此时反序列化的虚拟机对象视作一台新的虚拟机，同样需要记入**机器编号**，并持有其引用，同样可被`clearVM`命令清空历史指令，即使它不是由工厂创建的。

- 说明：关于如何进行反序列化可参考**总体说明**中的**实践**参考（真的只有几行代码）。评测数据保证下载的虚拟机路径此前一定有上传过，且所有数据点之间的虚拟机序列化后存储文件互不干扰，且路径合法。

- 前置条件：已登录、已选择课程

- 成功输出

    ```bash
    downloadVM success
    ```



**综合上传与下载虚拟机的一个具体案例**

还是我们的 A 同学，在他课程 C2101 中**机器编号**为2的 Linux 虚拟机被老师清空前，他通过`uploadVM ./data/A_Linux.out`命令将他此时具有三条历史指令的虚拟机序列化存储到当前目录下的 data 文件夹下，并将文件命名为 A_Linux.out。

此时 B 同学知道了，在选择课程 C2102 后，即可通过`downloadVM ./data/A_Linux.out`命令将该虚拟机文件反序列化到该课程下并为自己所持有，若在课程 C2102 中还未有过其他虚拟机，则该虚拟机**机器编号**即为1。

那么此时 B 同学若使用`logVM`命令，则会打印那三条历史指令。

同样地，若 A 同学的 Linux 虚拟机被老师清空后，也可通过`downloadVM ./data/A_Linux.out`命令将该虚拟机文件反序列化到课程 C2101 下并为自己所持有，若中途没有创建出其他虚拟机，则此台新虚拟机的**机器编号**为3，这样他的三条历史指令又回来啦。
