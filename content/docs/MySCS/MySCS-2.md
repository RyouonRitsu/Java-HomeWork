---
weight: 202
math: true
---

# MySCS -2

同学们，我们用了两周时间完成了MySCS的部分功能，已经实现了老师端和学生端的登录注册。

我们每两周会增加一次新的需求，MySCS-2需要大家在MySCS-1的基础上完成管理端的基础命令，MySCS-3需要大家在2的基础上添加学生操作以及其他功能等。（挖坑

编写好的代码需要大家用patpat评测，所有测试点都通过以后即可上传到云平台完成本次实验。希望大家认真完成哦！请勿抄袭，抄袭会被查到的（盯

## 题目背景

软件学院云平台（青春版），即MySCS，是一款更加轻量化的命令行操作的云平台，屏幕前的你拥有完全的自主知识产权，有极大的使用价值和收藏价值。

MySCS分为管理端（老师端和助教端）和学生端，可以实现传统的scs的大部分功能。本此实验将完成管理端的基础操作，需要基于MySCS-1编写（建议备份一下MySCS-1，然后拷贝一下来搭MySCS-2）。

## 命令概览

| 命令行                                               | 功能                         | 输出                                                         |
| ---------------------------------------------------- | ---------------------------- | ------------------------------------------------------------ |
| addCourse 课程号 课程名称                            | 添加课程（老师端）           | add course success                                           |
| removeCourse 课程号                                  | 移除课程（老师端）           | remove course success                                        |
| listCourse                                           | 列出课程（老师端）           | [ID:课程编号] [Name:课程名称] [TeacherNum:老师人数] [AssistantNum:助教人数] [StudentNum:学生人数] |
| selectCourse 课程号                                  | 选择课程（管理端）           | select course success                                        |
| addAdmin 学工号 学工号 ...                           | 添加课程管理端成员（老师端） | add admin success                                            |
| removeAdmin 学工号                                   | 移除课程管理端成员（老师端） | remove admin success                                         |
| listAdmin                                            | 列出课程管理端成员（管理端） | [ID:学工号] [Name:姓 名] [Type:用户身份(Professor/Assistant)] [Email:电子邮箱] |
| changeRole                                           | 有助教身份的学生切换身份     | change into Assistant success<br>change into Student success |
| addWare 资料编号 资料名称                            | 添加课程资料（老师端）       | add ware success                                             |
| removeWare 资料编号                                  | 移除课程资料（老师端）       | remove ware success                                          |
| listWare                                             | 列出课程资料（管理端）       | [ID:课程资料编号] [Name:课程资料名称]                        |
| addTask 作业编号  作业名称 作业开始时间 作业截至时间 | 添加课程作业（管理端）       | add task success                                             |
| removeTask 作业编号                                  | 移除课程作业（管理端）       | remove task success                                          |
| listTask                                             | 列出课程作业（管理端）       | [ID:课程作业编号] [Name:课程作业名称] [ReceiveNum:接收作业份数] [StartTime:开始时间] [EndTime:截 止时间] |
| addStudent 学工号 学工号 ...                         | 添加课程学生（管理端）       | add student success                                          |
| removeStudent 学工号                                 | 移除课程学生（管理端）       | remove student success                                       |
| listStudent                                          | 列出课程学生（管理端）       | [ID:学工号] [Name:姓 名] [Email:电子邮箱]                    |

## 功能描述

### 0.功能说明

对于以下每个命令在无特殊说明的情况下，只有**使用对象**能够成功使用，非**使用对象**调用时均优先返回`permission denied`，其中部分命令在后续会支持学生端的使用，但在本次迭代中无需考虑。

### 1.添加课程

`addCourse 课程号 课程名称`

##### 使用对象：老师端

当老师成功登录MySCS时即可添加课程，每个老师所属课程相对独立。

老师添加课程时，需要提供课程号，课程名称。

##### 课程号

- 课程号共有5位，是一个由C开头，剩下四位都是数字的字符串，其中数字的前两位为课程开设年份，取值范围为$[17,22]$，低两位没有特殊要求，取值范围为$[01,99]$。

##### 合法的课程号实例

C2102 C2293 C1721

##### 课程名称

- 课程名称是由字母大小写、数字和下划线组成的字符串，长度在6个字符到16个字符之间。

##### 合法的课程名称实例

aaaw a_1ea 12004 ___

##### 成功输出

- 当课程号，课程名称都合法时，添加课程成功，系统输出：

```bash
add course success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师时，输出：

```bash
permission denied
```

- 当课程号不合法时，输出：

```bash
course id illegal
```

- 当课程号被注册时，输出：

```bash
course id duplication
```

- 当课程名称不合法时，输出

```bash
course name illegal
```

### 2.移除课程

`removeCourse 课程号`

##### 使用对象：老师端

当老师成功登录MySCS时即可移除课程，每个老师所属课程相对独立。

老师移除课程时，需要提供课程号。

##### 成功输出

- 当课程号合法要求且在自己的名下存在时，移除课程成功，系统输出：

```bash
remove course success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师时，输出：

```bash
permission denied
```

- 当课程号不合法要求时，系统输出：

```bash
course id illegal
```

- 当课程号不存在或不在自己名下时，系统输出：

```bash
course id not exist
```

### 3.列出课程

`listCourse`

##### 使用对象：老师端（注意，因为目前没有做学生端相关内容，需要预留接口嗷，助教端的课程列表和学生端会在后续添加）

当老师成功登录MySCS时即可列出课程列表，每个老师所属课程相对独立。

##### 成功输出

- 当老师调用此命令时，成功输出课程列表，即输出老师名下所有课程，每个课程及属性对应一行：

顺序由课程编号数字从小到大排列

> Hint：可以考虑使用 TreeMap 来存储课程实例。

```bash
[ID:课程编号] [Name:课程名称] [TeacherNum:老师人数] [AssistantNum:助教人数] [StudentNum:学生人数]
```

##### 成功输出实例

[ID:C2021] [Name:oop_spring] [TeacherNum:1] [AssistantNum:2] [StudentNum:200]

[ID:C2121] [Name:oop_autumn] [TeacherNum:2] [AssitantNum:4] [StudentNum:200]

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师时，输出：

```bash
permission denied
```

- 当老师名下没有课程时，输出：

```bash
course not exist
```

### 4.选择课程

`selectCourse 课程号`

##### 使用对象：管理端（注意，因为目前没有做学生端相关内容，需要预留接口嗷）

当老师或助教成功登录MySCS时即可选择课程，用以做课程之下的对应操作。

##### 成功输出

- 当老师或助教调用此命令，课程号合法要求且课程在自己名下时，系统输出：

```bash
select course success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当课程号不合法要求时，系统输出：

```bash
course id illegal
```

- 当课程号不存在或不在自己名下时，系统输出：

```bash
course id not exist
```

> Hint：在退出登录时注意清空当前选择课程。

### 5.添加课程管理端成员

`addAdmin 学工号 学工号 ...`

##### 使用对象：老师端

当老师成功登录MySCS时并且选中课程时即可添加管理端成员，可以添加老师和助教。

我们期望模拟一次性添加多个管理端成员，为了给后面文件导入挖坑（

##### 正确输入实例

addAdmin 10001 20373252 19377054 19375030 19377110 10086

##### 成功输出

如果参数学工号已经在课程内，可以视为正确参数

- 当老师调用此命令，所有学工号合法要求的话，系统输出：

```bash
add admin success
```

> Hint：注意不要重复添加管理端成员。

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当参数学工号出现不合法（以第一个出错的参数为准）的情况时，系统不接受这条指令的所有参数，系统输出：

```bash
user id illegal
```

- 当参数学工号出现未被注册（以第一个出错的参数为准）的情况时，系统不接受这条指令的所有参数，系统输出：

```bash
user id not exist
```

### 6.移除课程管理端成员

`removeAdmin 学工号`

##### 使用对象：老师端

当老师成功登录MySCS并且选中课程时即可移除管理端成员。

##### 成功输出

- 当老师登录，选择了课程，且参数都合法时，系统输出：

```bash
remove admin success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当参数学工号不合法时，系统输出：

```bash
user id illegal
```

- 当参数学工号未被注册或不在课程管理端成员中时，系统输出：

```bash
user id not exist
```

### 7.列出课程管理端成员

`listAdmin`

##### 使用对象：管理端

当老师或助教成功登录MySCS并且选中课程时可以列出管理端成员列表。

##### 成功输出

- 当老师或助教成功登录MySCS，并且想要查看课程管理端成员时，成功输出管理端成员列表，每个用户及属性对应一行：

顺序为学工号数字从小到大排列

```bash
[ID:学工号] [Name:姓 名] [Type:用户身份(Professor/Assistant)] [Email:电子邮箱]
```

##### 成功输出实例

[ID:2037252] [Name:Fl Rover] [Type:Assistant] [Email:`20373252@buaa.edu.cn`]

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

### 8.切换助教端和学生端

`changeRole`

##### 使用对象：助教端和学生端

当有助教身份的学生成功登录MySCS时，默认在学生端，使用此指令可以在助教端和学生端之间切换。

注意，设计成可以切换的目的是复用界面功能，但是功能返回的结果可能不同。比如助教如果在助教端，使用listCourse的指令会返回自己担任助教的课程，但是如果助教在学生端，就会显示自己所学的课程。

##### 成功输出

- 当有助教身份的学生成功登录MySCS时，使用此指令可以在助教端和学生端之间切换
- 切换成助教成功时输出：

```bash
change into Assistant success
```

- 切换成学生成功时输出：

```bash
change into Student success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是有助教身份的学生或是老师时，输出：

```bash
permission denied
```

### 9.添加课程资料

`addWare 资料编号 资料名称`

##### 使用对象：老师端

当老师成功登录MySCS并且选中课程时即可添加课程资料，一次可以上传一个课程资料。

##### 课程资料编号

- 课程号共有7位，是一个由W开头，剩下六位都是数字的字符串，其中数字的前四位为课程号的后四位，低两位没有特殊要求，取值范围为$[01,99]$。

##### 合法的课程资料编号实例

W210201 W191743 W182930

##### 课程资料名称

- 课程资料名称是由字母大小写、数字、下划线（仅出现在点的左边）和点（有且仅有一个点，且不在字符串首尾）组成的字符串，长度在6个字符到16个字符之间。

##### 合法的课程资料名称示例

`aaaw.jpg` `a_1ea.png` `12004___.abc`

##### 不合法的课程资料名称示例

`aaaw.j_pg`  `a_1ea.` `abc`

##### 成功输出

- 当老师调用此命令，资料编号合法的话，系统输出：

```bash
add ware success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当课程资料号不合法时，输出：

```bash
ware id illegal
```

- 当课程资料号被注册时，输出：

```bash
ware id duplication
```

- 当课程资料名称不合法时，输出：

```bash
ware name illegal
```

### 10.移除课程资料

`removeWare 资料编号`

##### 使用对象：老师端

当老师成功登录MySCS并且选中课程时即可移除课程资料。

老师移除课程资料时，需要提供课程资料号。

##### 成功输出

- 当课程资料号合法且在自己的名下存在时，移除课程资料成功，系统输出：

```bash
remove ware success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当课程资料号不合法时，系统输出：

```bash
ware id illegal
```

- 当课程资料号未被注册或不在课程中时，系统输出：

```bash
ware id not exist
```

### 11.列出课程资料

`listWare`

##### 使用对象：管理端（注意，因为目前没有做学生端相关内容，需要预留接口嗷）

当老师或助教成功登录MySCS并且选中课程时可以列出课程资料列表。

##### 成功输出

- 当老师和助教成功登录MySCS，并且想要查看课程资料时，成功输出课程资料列表，每个课程资料及属性对应一行：

顺序由课程资料编号数字从小到大排列

```bash
[ID:课程资料编号] [Name:课程资料名称]
```

##### 成功输出实例

[ID:W193701] [Name:Test1.pdf]

[ID:W193702] [Name:Test2.pdf]

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

### 12.添加课程作业

`addTask 作业编号  作业名称 作业开始时间 作业截至时间`

##### 使用对象：管理端

当老师或助教成功登录MySCS并且选中课程时即可添加课程作业，一次可以上传一个课程作业。

##### 课程作业编号

- 课程号共有7位，是一个由T开头，剩下六位都是数字的字符串，其中数字的前四位为课程号的后四位，低两位没有特殊要求，取值范围为$[01,99]$。

##### 合法的课程作业编号实例

T210201 T191743 T182930

##### 课程作业名称

- 课程作业名称是由字母大小写、数字、下划线（仅出现在点的左边）和点（有且仅有一个点，且不在字符串首尾）组成的字符串，长度在6个字符到16个字符之间。

##### 合法的课程作业名称示例

`aaaw.jpg` `a_1ea.png` `12004___.abc`

##### 不合法的课程作业名称示例

`aaaw.j_pg`  `a_1ea.` `abc`

##### 时间

- 时间格式为xxxx-xx-xx-xx:xx:xx，分别对应年、月、日、时、分、秒，不够补前导零，需要满足真实时间（闰年、12个月等基本日期格式要求，年份范围为$[1900,9999]$），注意截至时间需要在开始时间之后

##### 时间实例

2022-02-02-00-03-19

##### 成功输出

- 当老师或助教调用此命令，作业编号合法要求的话，系统输出：

```bash
add task success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当课程作业号不合法时，输出：

```bash
task id illegal
```

- 当课程作业号被注册时，输出：

```bash
task id duplication
```

- 课程作业名称不合法时，输出：

```bash
task name illegal
```

- 课程作业时间不合法时，输出：

```bash
task time illegal
```

### 13.移除课程作业

`removeTask 作业编号`

##### 使用对象：管理端

当老师或助教成功登录MySCS并且选中课程时即可移除课程作业。

老师或助教移除课程作业时，需要提供课程作业号。

##### 成功输出

- 当课程作业号合法要求且在自己的名下存在时，移除课程作业成功，系统输出：

```bash
remove task success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当课程作业号不合法时，系统输出：

```bash
task id illegal
```

- 当课程作业号未被注册或不在课程中时，系统输出：

```bash
task id not exist
```

### 14.列出课程作业

`listTask`

##### 使用对象：管理端（注意，因为目前没有做学生端相关内容，需要预留接口嗷）

当老师或助教成功登录MySCS并且选中课程时可以列出课程作业列表。

##### 成功输出

- 当老师和助教成功登录MySCS，并且想要查看课程作业时，成功输出课程作业列表，每个课程作业及属性对应一行：

顺序由课程作业编号数字从小到大排列

```bash
[ID:课程作业编号] [Name:课程作业名称] [ReceiveNum:接收作业份数] [StartTime:开始时间] [EndTime:截止时间]
```

##### 成功输出实例

[ID:T193701] [Name:Test1.pdf] [ReceiveNum:100] [StartTime:2022-09-17-00:00:01] [EndTime:2022-09-18-00:00:01]

[ID:T193702] [Name:Test2.pdf] [ReceiveNum:50] [StartTime:2022-09-17-00:00:01] [EndTime:2022-09-18-00:00:01]

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

### 15.添加课程学生

`addStudent 学工号 学工号 ...`

##### 使用对象：管理端

当老师或助教成功登录MySCS时并且选中课程时即可添加学生，可以添加课程学生。

我们期望模拟一次性添加多个课程学生，为了给后面文件导入挖坑（

##### 正确输入实例

addStudent 20373252 19377054 19375030 19377110

##### 成功输出

如果参数学工号已经在课程内，可以视为正确参数

- 当老师或助教调用此命令，学工号合法要求的话，系统输出：

```bash
add student success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当参数学工号出现不合法（以第一个出错的参数为准）的情况时，系统不接受这条指令的所有参数，系统输出：

```bash
user id illegal
```

- 当参数学工号出现未被注册（以第一个出错的参数为准）的情况时，系统不接受这条指令的所有参数，系统输出：

```bash
user id not exist
```

- 当参数学工号出现老师的学工号（以第一个出错的参数为准）时，系统不接受这条指令的所有参数，系统输出：

```bash
I'm professor rather than student!
```

### 16.移除课程学生

`removeStudent 学工号`

##### 使用对象：管理端

当老师或助教成功登录MySCS并且选中课程时即可移除学生。

##### 成功输出

- 当老师或助教登录，选择了课程，且参数都合法时，系统输出：

```bash
remove student success
```

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

- 当参数学工号不合法时，系统输出：

```bash
user id illegal
```

- 当参数学工号未被注册或未在课程中时，系统输出：

```bash
user id not exist
```

### 17.列出课程学生

`listStudent`

##### 使用对象：管理端

当老师或助教成功登录MySCS并且选中课程时可以列出学生列表。

##### 成功输出

- 当老师或助教成功登录MySCS，并且想要查看课程学生时，成功输出学生列表，每个用户及属性对应一行：

顺序为学工号数字从小到大排列

```bash
[ID:学工号] [Name:姓 名] [Email:电子邮箱]
```

##### 成功输出实例

[ID:2037252] [Name:Fl Rover] [Email:`20373252@buaa.edu.cn`]

##### 失败输出

- 当系统目前没有用户登录时，输出：

```bash
not logged in
```

- 当系统目前登录用户不是老师或助教时，输出：

```bash
permission denied
```

- 当用户没有选择课程时，输出：

```bash
no course selected
```

## 输出优先级

首先，当输入的命令未定义时，输出

```bash
command '命令' not found
```

例如： 输入： `loggin 19375030 123456`

由于命令 loggin 未定义，所以输出 `command 'loggin' not found`

其次，当输入的命令有定义，但是参数的个数不合法要求时，输出

```bash
arguments illegal
```

输入： `login 19375030 123456 haha` 和 `login 19375030` 都对应着 `arguments illegal`

当**命令有定义，参数个数正确**时，才会输出 `Bye~` 或者 `user id illegal` 等成功或失败输出。

当一句命令存在多种非法情况，按上述顺序只输出最先发生的非法信息。

例如，注册时学号和姓名均非法，按上述顺序，输出 `user id illegal`，而不是`user name illegal`

## 测试样例

```bash
register 20373252 Yixiao Li 20373252@buaa.edu.cn x12345678 x12345678
register success
register 19377054 Hongxi Zhou 19377054@buaa.edu.cn x12345678 x12345678
register success
register 10001 Xueping Shen 10001@buaa.edu.cn x12345678 x12345678
register success
login 10001 x12345678
Hello Professor Shen~
addCourse C2101 oop_autumn
add course success
listCourse
[ID:C2101] [Name:oop_autumn] [TeacherNum:1] [AssistantNum:0] [StudentNum:0]
selectCourse C2101
select course success
addAdmin 20373252
listAdmin
[ID:10001] [Name:Shen Xueping] [Type:Professor] [Email:10001@buaa.edu.cn]
[ID:20373252] [Name:Li Yixiao] [Type:Assistant] [Email:20373252@buaa.edu.cn]
addTask T210101 autumn_task1.txt 2020-02-29-00:00:00 2024-02-29-00:00:00
add task success
listTask
[ID:T210101] [Name:autumn_task1] [ReceiveNum:0] [StartTime:2020-02-29-00:00:00] [EndTime:2024-02-29-00:00:00]
addWare W210101 autumn_ware1.txt
add ware success
listWare
[ID:W210101] [Name:autumn_ware1.txt]
addStudent 19377054
add student success
listStudent
[ID:19377054] [Name:Zhou Hongxi] [Email:19377054@buaa.edu.cn]
logout
Bye~
login 20373252 x12345678
Hello Yixiao~
selectCourse C2101
select course success
listAdmin
[ID:10001] [Name:Shen Xueping] [Type:Professor] [Email:10001@buaa.edu.cn]
[ID:20373252] [Name:Li Yixiao] [Type:Assistant] [Email:20373252@buaa.edu.cn]
listTask
[ID:T210101] [Name:autumn_task1] [ReceiveNum:0] [StartTime:2020-02-29-00:00:00] [EndTime:2024-02-29-00:00:00]
listWare
[ID:W210101] [Name:autumn_ware1.txt]
listStudent
[ID:19377054] [Name:Zhou Hongxi] [Email:19377054@buaa.edu.cn]
logout
Bye~
QUIT
----- Good Bye! -----
```
