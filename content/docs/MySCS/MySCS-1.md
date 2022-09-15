---
weight: 201
math: true
---

# MySCS -1

同学们，从这周开始，我们会迭代完成一个MySCS，即从零开发的软件学院云平台（青春版）。

我们每两周会增加一次新的需求，如，MySCS-1需要大家完成账号注册和登录系统，MySCS-2需要大家在1的基础上添加课程系统或其它系统等。

编写好的代码需要大家用patpat评测，所有测试点都通过以后即可上传到云平台完成本次实验。希望大家认真完成哦！请勿抄袭，抄袭会被查到的（盯

## 题目背景

软件学院云平台（青春版），即MySCS，是一款更加轻量化的命令行操作的云平台，屏幕前的你拥有完全自主知识产权，有极大的使用价值和收藏价值。

MySCS分为教师端和学生端，可以实现传统scs的大部分功能，如教师端对课程的增删改查，对课程所属的实验的增删改查，教师还可以为课程添加学生和助教，学生可以查看参与的课程并提交实验作业，助教可以协同老师管理课程等。

## 命令概览

| 命令行                                   | 功能           | 输出                                                         |
| ---------------------------------------- | -------------- | ------------------------------------------------------------ |
| QUIT                                     | 系统关机       | ----- Good Bye! -----                                        |
| register 学工号 名 姓 邮箱 密码 确认密码 | 注册           | register success<br />already logged in<br />user id illegal<br />user id duplication<br />user name illegal<br />email address illegal<br />password illegal<br />passwords inconsistent |
| login 学工号 密码                        | 登录           | Hello 名~<br />Hello Professor 姓~<br />already logged in<br />user id illegal<br />user id not exist<br />wrong password |
| printInfo                                | 打印本人信息   | Name: 名 姓<br/>ID: xxx<br/>Type: Student（或Professor）<br/>Email: xxx<br /><br />login first |
| printInfo 学号                           | 打印其他人信息 | Name: 名 姓<br/>ID: xxx<br/>Type: Student（或Professor）<br/>Email: xxx<br /><br />login first<br />user id illegal<br />user id not exist<br />permission denied |
| logout                                   | 退出当前用户   | Bye~<br />not logged in                                      |

## 功能描述

#### 1.开关机

实现最基本的命令读入和退出功能

首先，你的任务是编写一个Test类：

* 当程序启动，进入main方法，并连续读入输入的命令行，命令行的基本格式为：

```shell
命令 [参数1] [参数2] [参数3]
```

其中参数数目为不定项；命令和参数，参数和参数之间由一个或多个空白符分隔；命令之前和最后一个参数之后可以没有或有若干个空白符。

当输入的某一行只有空白符，什么都不会输出。

* 当终端输入 `QUIT` 时，系统退出（程序退出状态为0），并在终端打印一行字符：

```shell
----- Good Bye! -----
```

#### 2.用户注册

用户需要注册账号并登录后才能使用MySCS。

用户注册时，需要提供学工号，名，姓，邮箱，密码和确认密码。

##### 学号

* 对学生而言，一般情况下学号为8位数字，从高向低数，最高两位代表入学年份，随后的两位代表学院编号，随后的一位代表大班号，最低3位代表班内编号。

其中，入学年份取值范围为$[17,22]$，学院编号取值范围为$[01,43]$，大班号取值为$[1,6]$，最低三位没有特殊要求，取值范围为$[001,999]$。

如，$19375030$代表19年入学，在序号为37的学院，第五大班。

有些情况下，学号为2位大写字母+7位数字，SY开头代表学术型硕士研究生，ZY开头 代表专业型硕士研究生，BY开头代表博士研究生。

由于本科生和博士生最多读6年，硕士生最多读4年将被退学，所以硕士生的入学年份取值为$[19,22]$ ，博士生的入学年份取值为$[17,22]$，且硕士和博士研究生的班内编号只有2位，取值范围为$[01,99]$，其他要求相同。

如，$SY2021118$代表硕士研究生，20年入学，21系，第1大班。

**合法的学生学号实例：**

19375030 19375168 BY2215201 SY2021118

* 对教师而言，工号为5位数字，没有特殊限制，取值范围为$[00001,99999]$。

##### 姓，名

姓和名都应该只由字母组成，其中首字母大写，非首字母小写，姓和名的长度都在1个字符到20个字符之间。

##### 邮箱

一个合法的邮箱地址由 `用户名` + `@` + `服务器域名` 组成。

本题要求中，用户名应为长度$\geq1$的字符串，只能由大小写字母，下划线和数字中的一种或几种组 成。

服务器域名的规律为“[N级域名.]...[三级域名.]二级域名.顶级域名”，比如如“qq.com”、 “mp.weixin.qq.com”、“buaa.edu.cn”，"T_T.QAQ.cn"等，域名同样只能由大小写字母，下划线和数字中的一种或几种组成。（即最少有两级域名，最多不限）

##### 密码

密码应该为8-16位，只能由大小写字母，下划线和数字中的一种或几种组成，要求以字母开头。

##### 确认密码

确认密码必须与密码一致才能成功注册。

##### 成功输出

当学工号，姓名，邮箱，密码和确认密码都符合上述要求时，注册成功，系统输出：

```bash
register success
```

##### 失败输出

* 当系统目前有用户登录时，输出

```bash
already logged in
```

（MySCS只允许同时有一位用户处于登录状态，此时不可以执行登录或注册命令）。

* 学号不合法时，输出

```bash
user id illegal
```

* 学号已被注册时，输出

```bash
user id duplication
```

* 姓或名不合法时，输出

```bash
user name illegal
```

* 邮箱不合法时，输出

```bash
email address illegal
```

* 密码不合法时，输出：

```bash
password illegal
```

* 两次密码不一致时，输出：

```bash
passwords inconsistent
```

#### 3.用户登录

##### 成功输出

当当前没有用户登录，学工号已经注册且密码正确，登录成功，

* 如果当前为学生登录，系统输出：

```bash
Hello 名~
```

* 如果当前为教师登录，系统输出：

```bash
Hello Professor 姓~
```

##### 失败输出

* 当系统目前有用户登录时，输出

```bash
already logged in
```

* 学号不合法时，输出

```bash
user id illegal
```

* 学号未被注册时，输出

```bash
user id not exist
```

* 密码错误时，输出

```bash
wrong password
```

#### 3. 用户登出

##### 成功输出

当当前有用户登录，则登出成功，系统输出：

```bash
Bye~
```

##### 失败输出

* 当系统目前未有用户登录时，输出

```bash
not logged in
```

#### 4.打印信息

建议同学们重写Object类的toString()方法完成这项功能。

* 当命令为 `printInfo` 时，按以下格式打印本人个人信息。（教师与学生均可使用）

```bash
Name: 名 姓
ID: xxx
Type: Student/Professor
Email: xxxxxx
```

* 老师有权查看其他老师和学生的个人信息，所以当当前登录用户为教师时，当命令为 `printInfo 学号` 时，按上述格式打印该人个人信息。

##### 失败输出

* 当前未有用户登录，输出

```bash
login first
```

* 当学生调用有参数的 `printInfo` ，输出

```bash
permission denied
```

* 当作为参数的学号不合法，输出

```bash
user id illegal
```

* 当学号对应的用户未注册，输出

```bash
user id not exist
```

## 输出优先级

首先，当输入的命令未定义时，输出

```bash
command '命令' not found
```

例如： 输入： `loggin 19375030 123456`

由于命令 loggin 未定义，所以输出 `command 'loggin' not found`

其次，当输入的命令有定义，但是参数的个数不符合要求时，输出

```bash
arguments illegal
```

输入： `login 19375030 123456 haha` 和 `login 19375030` 都对应着 `arguments illegal`

当**命令有定义，参数个数正确**时，才会输出 `Bye~` 或者 `user id illegal` 等成功或失败输出。

当一句命令存在多种非法情况，按上述顺序只输出最先发生的非法信息。

例如，注册时学号和姓名均非法，按上述顺序，输出 `user id illegal`，而不是`user name illegal`

## HINT

在判断姓名，邮箱和密码等的合法与否时，非常建议利用正则表达式来实现，这是一个非常方便实用常用的技巧，希望同学们趁这个机会学习一二，菜鸟教程的Java正则表达式教程就是个不错的小教程。

例如，想判断`19375030@buaa.edu.cn`是否满足题目中的条件，用一行代码即可：

```java
public class Demo {
    public static void main(String[] args) {
        String st = "19375030@buaa.edu.cn";
        boolean res = st.matches("\\w+@\\w+(\\.\\w+)+");
        System.out.println(res);//true
    }
}
```

同样地，想要快速便捷地判断密码是否合法，姓名是否合法，甚至处理一行输入中的空白符，都可以考虑利用正则表达式这个好工具。

当然，同学们可以用能想到的任何合理方法解决（不合理方法：打表等）。

## 测试样例

```bash
register 19375030 Xinlei Bao 3499475017@qq.com a7ki7kibangbang a7ki7kibangbang
register success
login 19375030 a7ki7kibangbang
Hello Xinlei~
register 10086 Alex Herbert 10086@buaa.edu.cn 123 123
already logged in
logout
Bye~
register 10086 Alex Herbert 10086@buaa.edu.cn a12345678 a12345678
register success
login 10086 a12345678
Hello Professor Herbert~
printInfo
Name: Alex Herbert
ID: 10086
Type: Professor
Email: 10086@buaa.edu.cn
printInfo 19375030
Name: Xinlei Bao
ID: 19375030
Type: Student
Email: 3499475017@qq.com
logout
Bye~
QUIT
----- Good Bye! -----
```
