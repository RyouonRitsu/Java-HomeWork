---
weight: 202
math: true
---

# MySCS-3

同学们，在上一次的迭代中，我们已经完成了管理端的命令开发，接下来我们需要完成学生端的命令了。

编写好的代码需要使用patpat进行评测，AC后即可上传到云平台以完成本次的实验内容。希望大家认真且保质保量的完成，请勿抄袭，**如有发现抄袭现象将取消迭代作业成绩**。

## 题目背景

软件学院云平台（青春版），即MySCS，是一款更加轻量化的命令行操作的云平台，屏幕前的你拥有完全的自主知识产权，有极大的使用价值和收藏价值。本平台模拟一台操作机上的命令行，所有操作均在本地进行，不支持联网操作，同一时间的使用者有且仅有一个。

MySCS分为管理端（老师端和助教端）和学生端，可以实现传统的scs的大部分功能。本次迭代需要完成的内容为学生端命令的开发，请**直接基于AC后的MySCS-2代码编写**，否则发生的异常情况助教概不负责。

作为你们迭代作业的甲方，助教在这次的迭代中**修改了部分输出格式**，同时也为部分之前的命令**增添了新的功能**，相信作为优秀乙方的你们一定能很好的完成甲方的需求的吧~

## 整体约束

1. 当输入的命令未定义时，输出

   ```bash
   command '命令' not found
   ```

   例如： 输入： `loggin 19375030 123456`

   由于命令 loggin 未定义，所以输出 `command 'loggin' not found`

2. 若以上[功能描述](#功能描述)中有**明确给出输出顺序**，则以它为准
3. 若不满足第二条，则保持原输出顺序不改变
4. 当输入的命令有定义，但是参数的个数不合法要求时，输出

   ```bash
   arguments illegal
   ```

   输入： `login 19375030 123456 haha` 和 `login 19375030` 都对应着 `arguments illegal`

   当**命令有定义，参数个数正确**时，才会输出 `Bye~` 或者 `user id illegal` 等成功或失败输出

   当一句命令存在多种非法情况，按上述顺序只输出最先发生的非法信息

   例如，注册时学号和姓名均非法，按上述顺序，输出 `user id illegal`，而不是`user name illegal`

5. 评测时，请将所有文件放到patpat可执行文件相同路径下。例如：

   ```bash
   MySCS\example
    ├─LAB1.md
    ├─Lesson1.txt
    ├─19376054.task
    ├─T191743.ans
    ├─patpat.exe
    ├─3-学号-姓名
    │  ├─out
    │  └─src
    │     └─Test.java
    └─test
       ├─test.yaml
       └─judge.yaml
   ```

   提交代码时，请保持以上目录结构

   不要使用word，vsc等软件修改文件，可能会导致格式混乱

6. 请注意：PatPat仅支持单个Scanner对象，使用Scanner读入文件可能导致测评结果错误，请采用其他方式读入！

## 总体说明

- **仅提到的需要修改的命令才需要修改**，其余**未提到的命令均保持不变**
- 输出顺序即为优先级
- 命令中的 `[]` 代表可选项，该项可出现，也可空缺
- 命令中的 `...` 表示省略的语义，紧跟的前一项（若存在）可重复出现任意次数
- 命令中的 `|` 代表或的语义
- 命令中的 `()` 代表这是一个整体
- 命令中的 `<` 和 `>` 代表重定向输入/输出，特别地，`>` 表示覆盖，`>>` 表示追加
- 在示例中看到的 `$variable` 和 `${variable}` 表示这是一个变量，需要替换成实际值
- 在示例中看到的 `...` 表示省略的语义，并不是实际输出
- 在示例中看到 `$ 命令` 格式，表示这是你在IDEA的运行窗口中输入的命令，输入内容不应包括 `$` 和紧跟其后的空格

## 命令概览

| 需求 | 命令 | 功能描述 |
| --- | --- | --- |
| 修改 | `listCourse` | [列出所有课程](#列出所有课程) |
| 修改 | `selectCourse 课程编号` | [选择课程](#选择课程) |
| 新增 | `queryStatus` | [查询当前状态](#查询当前状态) |
| 新增 | `queryTeacher` | [查询老师](#查询老师) |
| 新增 | `queryAssistant` | [查询助教](#查询助教) |
| 修改 | `addWare 资料编号 资料路径地址 [[资料编号 资料路径地址]...]` | [添加课程资料](#添加课程资料) |
| 修改 | `removeWare 资料编号` | [移除课程资料](#移除课程资料) |
| 修改 | `listWare` | [列出课程资料](#列出课程资料) |
| 修改 | `addTask 作业编号 作业名称 作业开始时间 作业截至时间` | [添加课程作业](#添加课程作业) |
| 修改 | `removeTask 作业编号` | [移除课程作业](#移除课程作业) |
| 修改 | `listTask` | [列出课程作业](#列出课程作业) |
| 新增 | `downloadFile [保存路径地址] 文件编号 [(> | >>) 路径地址]` | [下载课程文件](#下载课程文件) |
| 新增 | `openFile [路径地址] | [< 路径地址]` | [打开文件](#打开文件) |
| 新增 | `submitTask [作业路径地址] 课程作业编号 [< 作业路径地址]` | [提交课程作业](#提交课程作业) |
| 新增 | `addAnswer [答案路径地址] 课程作业编号 [< 答案路径地址]` | [添加作业答案](#添加作业答案) |
| 新增 | `queryScore 课程作业编号 [学号]` | [查询作业成绩](#查询作业成绩) |

## 功能描述

### 列出所有课程

| 命令 |
| --- |
| `listCourse` |

- 使用对象：**所有人**
- 功能：列出与自己相关的所有课程
- 前置条件：已登录

- 成功输出
  - 老师端
  
    调用时数据不变，仅变更格式：

    ```bash
    total $课程总数 courses
    [1] [ID: $课程编号] [Name: $课程名称] [TeacherNum: $老师人数] [AssistantNum: $助教人数] [StudentNum: $学生人数]
    ...
    ```

    **Example**：

    ```bash
    $ listCourse
    total 2 courses
    [1] [ID: C2021] [Name: oop_spring] [TeacherNum: 1] [AssistantNum: 2] [StudentNum: 200]
    [2] [ID: C2121] [Name: oop_autumn] [TeacherNum: 2] [AssistantNum: 4] [StudentNum: 200]
    ```

    **注意**：**“:”后的空格**，后同，不再提醒。
  
  - 助教端和学生端
  
    **新增**：额外输出课程创建者的信息，调用`addCourse`命令的老师被认定为是该课程的创建者。
  
    **注意**：在助教端，输出仅为助教所参与管理的课程，并**不包含助教所在的课程**。
  
    ```bash
    total $课程总数 courses
    [1] [ID: $课程编号] [Name: $课程名称] [Creator: ${名} ${姓}] [TeacherNum: $老师人数] [AssistantNum: $助教人数] [StudentNum: $学生人数]
    ...
    ```

    **Example**：

    ```bash
    $ listCourse
    total 1 course
    [1] [ID: C2121] [Name: oop_autumn] [Creator: Xueping Shen] [TeacherNum: 2] [AssistantNum: 4] [StudentNum: 200]
    ```

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：
  
    ```bash
    please login first
    ```

- 特殊情况
  - 当用户名下没有课程时，输出：
  
    ```bash
    total 0 course
    ```
  
  - 当用户名下有且仅有一个课程时，输出第一行变更为：
  
    ```bash
    total 1 course
    ```

### 选择课程

| 命令 | 参数1 |
| --- | --- |
| `selectCourse` | `课程编号` |

- 使用对象：**所有人**
- 功能：选择当前需要操作的课程
- 前置条件：已登录
- **更新**：学生可以使用该命令来选择当前需要操作的课程

- 成功输出
  
  ```bash
  select course success
  ```

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：
  
    ```bash
    please login first
    ```
  
  - 当课程号不合法要求时，系统输出：
  
    ```bash
    course id illegal
    ```
  
  - 当课程号不存在或不在自己名下时，系统输出：
  
    ```bash
    course id not exist
    ```

    补充：“不在名下的课程”的定义为不在 `listCourse` 命令的成功输出中的课程。

> Hint：在退出登录时注意清空当前选择课程。

### 查询当前状态

| 命令 |
| --- |
| `queryStatus` |

- 使用对象：**所有人**
- 功能：查询当前用户的状态
- 前置条件：无

- 成功输出
  - 用户未登录时，输出：

    ```bash
    login status: false
    permission: none
    ```

  - 用户已登录但未选择课程时，输出：

    ```bash
    login status: true
    username: ${名} ${姓}
    permission: $权限
    selected course: none
    ```

  - 用户已登录且已选择课程时，输出：

    ```bash
    login status: true
    username: ${名} ${姓}
    permission: $权限
    selected course: [$课程编号: $课程名称]
    ```

  - Example

    ```bash
    $ queryStatus
    login status: false
    permission: none
    $ login 19375030 a7ki7kibangbang
    Hello Xinlei~
    $ queryStatus
    login status: true
    username: Xinlei Bao
    permission: student
    selected course: none
    $ selectCourse C2021
    select course success
    $ queryStatus
    login status: true
    username: Xinlei Bao
    permission: student
    selected course: [C2021: oop_spring]
    ```

  **注意**： `$权限` 为 `teacher` 、 `assistant` 或 `student` 中的一种，显然地， `changeRole` 命令会变更 `$权限` 的状态。

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

### 查询老师

| 命令 |
| --- |
| `queryTeacher` |

- 使用对象：**助教端**、**学生端**
- 功能：查询当前课程的所有老师信息
- 前置条件：已登录、已选择课程

- 成功输出
  
  ```bash
  total $老师总数 teachers
  [1] [Name: ${名} ${姓}] [Email: $邮箱]
  ---
  [2] [Name: ${名} ${姓}] [Email: $邮箱]
  [3] [Name: ${名} ${姓}] [Email: $邮箱]
  ...
  ```

  **注意**：课程创建者总是处在首位，并与其他管理员老师使用 `---` 分隔开，其余管理员老师的顺序按照 `Name` 作为一个整体字符串的字典序排序，同名顺序按照添加的先后顺序输出，**评测保证不会出现同名老师**。

  - Example

    ```bash
    $ selectCourse C2121
    select course success
    $ queryTeacher
    total 2 teachers
    [1] [Name: Xueping Shen] [Email: xueping@buaa.edu.cn]
    ---
    [2] [Name: Xiang Gao] [Email: gaoxiang@buaa.edu.cn]
    ```

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：
  
    ```bash
    please login first
    ```

  - 当系统目前没有用户选择课程时，输出：
  
    ```bash
    please select course first
    ```

- 特殊情况
  当课程中有且仅有一个老师时，输出：

  ```bash
  total 1 teacher
  [1] [Name: ${名} ${姓}] [Email: $邮箱]
  ```
  
### 查询助教

| 命令 |
| --- |
| `queryAssistant` |

- 使用对象：**老师端**、**学生端**
- 功能：查询当前课程的所有助教信息
- 前置条件：已登录、已选择课程

- 成功输出
  
  ```bash
  total $助教总数 assistants
  [1] [Name: ${名} ${姓}] [Email: $邮箱]
  ...
  ```

  **注意**：助教的顺序按照 `Name` 作为一个整体字符串的字典序排序，同名顺序按照添加的先后顺序输出，**评测保证不会出现同名助教**。

  - Example

    ```bash
    $ selectCourse C2121
    select course success
    $ queryAssistant
    total 2 assistants
    [1] [Name: Hongxi Zhou] [Email: 19376054@buaa.edu.cn]
    [2] [Name: Yixiao Li] [Email: 20373252@buaa.edu.cn]
    ```

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：
  
    ```bash
    please login first
    ```

  - 当系统目前没有用户选择课程时，输出：
  
    ```bash
    please select course first
    ```

- 特殊情况
  - 当课程中没有助教时，输出：
  
    ```bash
    total 0 assistant
    ```

  - 当课程中有且仅有一个助教时，输出第一行变更为：

    ```bash
    total 1 assistant
    ```

### 添加课程资料

| 命令 | 参数1 | 参数2 | 可选参数3 | 可选参数4 | ... |
| --- | --- | --- | --- | --- | --- |
| `addWare` | `资料编号` | `资料路径地址` | `资料编号` | `资料路径地址` | `...` |

- 使用对象：老师端、**助教端**
- 功能：管理端可以为课程添加资料，添加的资料会统一存放在当前目录（`.`）下名为 `$课程编号` 文件夹下的 `wares` 文件夹（`./$课程编号/wares/`）中（需要复制路径地址的文件到指定文件夹下），并重命名为 `$资料编号_$资料名称` ，其中资料名称被定义为文件名称（不是路径而是文件名，且包含后缀名，思考用什么方法获得）
- 前置条件：已登录、已选择课程
- **更新**：可一次添加多个资料，助教也可以添加资料，且涉及文件操作，输出格式变更

- 成功输出
  
  ```bash
  add $成功添加的资料总数 wares success
  $添加失败的资料总数 wares failed
  [1] [ID: $失败资料编号] [Path: $失败资料路径地址] [Reason: $失败原因]
  ...
  ```

  - 失败原因包括： `ware id illegal`、`ware id duplication`、`ware name illegal`、`ware file does not exist`、`ware file operation failed`、`unexpected error`。

  - 特别地，第一行及第二行当资料数为0或1时，变更 `wares` 为单数形式，例如：

    ```bash
    add 1 ware success
    0 ware failed
    ```

  - Example

    ```bash
    $ addWare W212101 Lesson1.md W212102 Lesson2.md W212103 abc
    add 2 wares success
    1 ware failed
    [1] [ID: W212103] [Path: abc] [Reason: ware name illegal]
    ```

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：

    ```bash
    please login first
    ```

  - 当系统目前登录用户不是老师或助教时，输出：

    ```bash
    permission denied
    ```

  - 当系统目前没有用户选择课程时，输出：

    ```bash
    please select course first
    ```

> Hint：
>
> 1. 你需要通过路径地址获取文件，然后使用原来的方法判断资料编号与文件名是否合法，并记录失败原因，最后在执行文件操作的时候仍需要try-catch，对捕获的异常进行记录并输出
> 2. 根据失败原因所包括的内容提示你，你需要至少catch课程资料号不合法、课程资料号被注册、课程资料名称不合法、文件不存在、文件复制操作失败和其他未知错误等异常
> 3. 考虑使用 `java.io.File` 来进行文件查看，考虑使用 `java.nio.file.Files` 进行文件操作，或者利用文件流进行操作

### 移除课程资料

此项改动较小，**仅指出不同部分**。

- 使用对象：老师端、**助教端**
- **更新**：移除课程资料的时候会同步删除对应文件，部分输出格式变更
- 失败输出：
  - 当系统目前没有用户登录时，输出：

    ```bash
    please login first
    ```

  - 当系统目前登录用户不是老师或助教时，输出：

    ```bash
    permission denied
    ```

  - 当系统目前没有用户选择课程时，输出：

    ```bash
    please select course first
    ```

  - 当课程资料编号未被注册或不在课程中时，系统输出：

    ```bash
    ware not found
    ```

  - 当删除文件失败时，系统输出：

    ```bash
    delete file failed
    ```

其余输出不变，输出顺序不变

### 列出课程资料

| 命令 |
| --- |
| `listWare` |

- 使用对象：**所有人**
- 功能：列出当前课程的所有资料
- 前置条件：已登录、已选择课程
- **更新**：助教端和学生端可以使用此命令，且输出格式变更

- 成功输出

  ```bash
  total $资料总数 wares
  [1] [ID: $课程资料编号] [Name: $课程资料名称]
  ...
  ```

  - Example

    ```bash
    $ listWare
    total 2 wares
    [1] [ID: W212101] [Name: Lesson1.md]
    [2] [ID: W212102] [Name: Lesson2.md]
    ```

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：

    ```bash
    please login first
    ```
  
  - 当用户没有选择课程时，输出：
  
    ```bash
    please select course first
    ```

- 特殊情况
  - 当课程中没有资料时，输出：
  
    ```bash
    total 0 ware
    ```

  - 当课程中有且仅有一个资料时，输出第一行变更为：

    ```bash
    total 1 ware
    ```

### 添加课程作业

此项改动较小，**仅指出不同部分**。

- **命令更新**：**作业名称**变更为**作业路径地址**，可参考[添加课程资料](#添加课程资料)

- 使用对象：管理端
- 功能：管理端可以为课程添加作业，添加的作业会统一存放在当前目录（`.`）下名为 `$课程编号` 下的 `tasks` 的文件夹（`./$课程编号/tasks/`）中（需要复制路径地址的文件到指定文件夹下），并重命名为 `$作业编号_$作业名称` ，其中作业名称被定义为文件名称（包含后缀名）
- 前置条件：已登录、已选择课程
- **功能更新**：涉及文件操作，部分输出变更

- 失败输出
  - 当系统目前没有用户登录时，输出：

    ```bash
    please login first
    ```

  - 当系统目前登录用户不是老师或助教时，输出：

    ```bash
    permission denied
    ```

  - 当系统目前没有用户选择课程时，输出：

    ```bash
    please select course first
    ```

其余输出不变，输出顺序不变

### 移除课程作业

此项改动较小，**仅指出不同部分**。

- 使用对象：管理端
- **更新**：移除课程作业的时候会同步删除对应文件，部分输出格式变更
- 失败输出：
  - 当系统目前没有用户登录时，输出：

    ```bash
    please login first
    ```

  - 当系统目前登录用户不是老师或助教时，输出：

    ```bash
    permission denied
    ```

  - 当系统目前没有用户选择课程时，输出：

    ```bash
    please select course first
    ```

  - 当课程作业编号未被注册或不在课程中时，系统输出：

    ```bash
    task not found
    ```

  - 当删除文件失败时，系统输出：

    ```bash
    delete file failed
    ```

其余输出不变，输出顺序不变

### 列出课程作业

| 命令 |
| --- |
| `listTask` |

- 使用对象：**所有人**
- 功能：列出当前课程的所有作业
- 前置条件：已登录、已选择课程
- **更新**：助教端和学生端可以使用此命令，且输出格式变更

- 成功输出
  - 助教端和老师端

    ```bash
    total $作业总数 tasks
    [1] [ID: $课程作业编号] [Name: $课程作业名称] [SubmissionStatus: $接收作业份数/$课程总人数] [StartTime: $开始时间] [EndTime: $截止时间]
    ...
    ```

    - Example

      ```bash
      total 2 tasks
      [1] [ID: T212101] [Name: LAB01.md] [SubmissionStatus: 200/200] [StartTime: 2022-09-08-19:00:00] [EndTime: 2022-09-22-18:59:59]
      [2] [ID: T212102] [Name: LAB02.md] [SubmissionStatus: 114/200] [StartTime: 2022-09-15-19:00:00] [EndTime: 2022-09-29-18:59:59]
      ```

  - 学生端
  
    ```bash
    total $作业总数 tasks
    [1] [ID: $课程作业编号] [Name: $课程作业名称] [Status: undone/done] [StartTime: $开始时间] [EndTime: $截止时间]
    ...
    ```

    - Example

      ```bash
      total 2 tasks
      [1] [ID: T212101] [Name: LAB01.md] [Status: done] [StartTime: 2022-09-08-19:00:00] [EndTime: 2022-09-22-18:59:59]
      [2] [ID: T212102] [Name: LAB02.md] [Status: undone] [StartTime: 2022-09-15-19:00:00] [EndTime: 2022-09-29-18:59:59]
      ```

    - 补充：作业已经提交过，则状态为`done`，否则为`undone`，提交命令见[提交课程作业](#提交课程作业)

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：

    ```bash
    please login first
    ```
  
  - 当用户没有选择课程时，输出：
  
    ```bash
    please select course first
    ```

- 特殊情况
  - 当课程中没有作业时，输出：
  
    ```bash
    total 0 task
    ```

  - 当课程中有且仅有一个作业时，输出第一行变更为：

    ```bash
    total 1 task
    ```

### 下载课程文件

| 命令 | 可选参数1 | 参数2 | 可选重定向符号 | 可选重定向路径 |
| --- | --- | --- | --- | --- |
| `downloadFile` | `[保存路径地址]` | `文件编号` | `[> | >>]` | `[路径地址]` |

- 使用对象：**所有人**
- 功能：下载当前课程的指定文件（资料或作业）到指定文件路径（Hint：本质复制/写入或追加写入），并**输出文件内容**
- 前置条件：已登录、已选择课程
- 备注：**评测会保证仅操作文本文件**

- 成功输出
  - 未使用重定向时
  
    ```bash
    $文件内容
    ```

  - 使用重定向时
  
    ```bash
    ```

- 失败输出
  - 使用重定向且成功时

    ```bash
    ```

  - 其他情况
    - 当使用了重定向时缺失重定向文件路径，输出：
  
      ```bash
      please input the path to redirect the file
      ```

    - 当**可选参数1和重定向同时使用**，且**两者路径相同**时，输出：

      ```bash
      input file is output file
      ```

    - 当参数数量不正确时，输出：

      ```bash
      arguments illegal
      ```

    - 当系统目前没有用户登录时，输出：
  
      ```bash
      please login first
      ```
  
    - 当用户没有选择课程时，输出：
  
      ```bash
      please select course first
      ```
  
    - 当文件编号不合法或不存在时，输出：

      ```bash
      file not found
      ```

    - 当文件操作失败时，输出：

      ```bash
      file operation failed
      ```
  
### 打开文件

| 命令 | 可选参数1 | 可选重定向符号 | 可选重定向路径 |
| --- | --- | --- | --- |
| `openFile` | `[路径地址]` | `[<]` | `[路径地址]` |

- 使用对象：**所有人**
- 功能：打开指定文件并输出文件内容，**使用可选参数1同时使用重定向**，**忽略重定向**，下同
- 前置条件：无
- 备注：**评测会保证仅操作文本文件**

- 成功输出

  ```bash
  $文件内容
  ```

- 失败输出
  - 当未使用重定向时缺失文件路径，输出：
  
    ```bash
    please input the path to open the file
    ```

  - 当使用了重定向时缺失重定向文件路径，输出：
  
    ```bash
    please input the path to redirect the file
    ```

  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当文件打开失败时，输出：

    ```bash
    file open failed
    ```

### 提交课程作业

| 命令 | 可选参数1 | 参数2 | 可选重定向符号 | 可选重定向路径 |
| --- | --- | --- | --- | --- |
| `submitTask` | `[作业路径地址]` | `课程作业编号` | `[<]` | `[作业路径地址]` |

- 使用对象：**学生端**
- 功能：提交指定作业并进行评测，作业需要保存到 `./$课程编号/tasks/$课程作业编号/` 文件夹下（需要复制路径地址的文件到指定文件夹下），并重命名为 `$学号.task`，如果作业已经存在，则询问是否覆盖
- 前置条件：已登录、已选择课程
- 简化条件：为了减轻大家的负担，本平台的作业仅为选择题、填空题、判断题，每题的答案可为任意字符，且**不区分大小写**，题号即为行号，答案即为行中的字母；分数的计算按**百分制**，每题**分数均等**，得分**保留到小数点后一位**
- 限制
  你的文件应为如下格式：

  ```bash
  [答案1]
  [答案2]
  ...
  ```

  Example：

  ```bash
  T
  F
  T
  T
  a
  G
  d
  Z
  Y
  114514
  1919810
  69
  996
  007
  have a nice day!
  ~/Desktop/submitTask 1
  ```

- 特殊需求：当助教还未上传答案时，**分数暂填None**，当查询得分时，如果助教已上传答案，则重新评分，否则不变

- 成功输出
  
  ```bash
  submit success
  your score is: $分数
  ```

- 失败输出
  - 当使用了重定向时缺失重定向文件路径，输出：
  
    ```bash
    please input the path to redirect the file
    ```

  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：
  
    ```bash
    please login first
    ```
  
  - 当用户没有选择课程时，输出：
  
    ```bash
    please select course first
    ```

  - 当课程作业编号不合法或不存在时，输出：

    ```bash
    task not found
    ```

  - 当文件操作失败时，输出：

    ```bash
    file operation failed
    ```

- 特殊情况
  - 当作业已经存在时，先询问：

    ```bash
    task already exists, do you want to overwrite it? (y/n)
    ```

  - 对于输入的非 `yY\n` 的字符，认为是 `n`，输出：

    ```bash
    submit canceled
    ```

    否则**进行文件操作**，然后给出成功/文件操作失败输出

### 添加作业答案

| 命令 | 可选参数1 | 参数2 | 可选重定向符号 | 可选重定向路径 |
| --- | --- | --- | --- | --- |
| `addAnswer` | `[答案路径地址]` | `课程作业编号` | `[<]` | `[答案路径地址]` |

- 使用对象：**管理端**
- 功能：为指定作业添加答案，答案需要保存到 `./$课程编号/answer/` 文件夹下（需要复制路径地址的文件到指定文件夹下），并重命名为 `$课程作业编号.ans`，重复添加答案时，覆盖原有答案
- 前置条件：已登录、已选择课程
- 简化条件：同[提交课程作业](#提交课程作业)
- 限制：同[提交课程作业](#提交课程作业)

- 成功输出
  
  ```bash
  add answer success
  ```

  - Example

    ```bash
    $ openFile ans.txt
    B
    B
    C
    $ addAnswer ans.txt T212101
    add answer success
    $ # 这里省略执行了登出命令并登录了所在课程下的学生账号
    $ openFile < 1.md
    B
    A
    C
    $ submitTask T212101 < 1.md
    submit success
    your score is: 66.7
    $ # 这里假定同学经过思考后修改了答案
    $ openFile < 1.md
    B
    B
    C
    $ submitTask T212101 < 1.md
    task already exists, do you want to overwrite it? (y/n)
    y
    submit success
    your score is: 100.0
    ```

- 失败输出
  - 当使用了重定向时缺失重定向文件路径，输出：
  
    ```bash
    please input the path to redirect the file
    ```

  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：
  
    ```bash
    please login first
    ```
  
  - 当用户没有选择课程时，输出：
  
    ```bash
    please select course first
    ```

  - 当课程作业编号不合法或不存在时，输出：

    ```bash
    task not found
    ```

  - 当文件操作失败时，输出：

    ```bash
    file operation failed
    ```

### 查询作业成绩

| 命令 | 参数1 | 可选参数2 |
| --- | --- | --- |
| `queryScore` | `[课程作业编号]` | `[学号]` |

- 使用对象：**所有人**
- 功能：查询作业的成绩，如果**不指定作业编号**，则查询该课程下**所有**作业的成绩；**指定学号功能仅管理端可用**，可以查询指定学号学生的成绩；**同一个作业的成绩仅显示最高分**；顺序为课程作业编号按照字典序升序排列，课程作业编号相同按成绩分数降序排列，None排在最底部，分数相同则按照学号字典序升序排列
- 前置条件：已登录、已选择课程

- 成功输出
  
  ```bash
  total $查询到的结果数量 results
  [1] [ID: $学号] [Name: ${名 姓}] [Task_ID: $课程作业编号] [Score: $成绩]
  ...
  ```

- 失败输出
  - 当参数数量不正确时，输出：

    ```bash
    arguments illegal
    ```

  - 当系统目前没有用户登录时，输出：
  
    ```bash
    please login first
    ```

  - 当用户没有选择课程时，输出：
  
    ```bash
    please select course first
    ```

  - 当学生指定学号查询时，输出：

    ```bash
    permission denied
    ```

  - 当课程作业编号不合法或不存在时，输出：

    ```bash
    task not found
    ```

  - 当学号不合法或不存在时，输出：

    ```bash
    student not found
    ```

- 特殊情况
  - 当查询到的结果数量为0时，输出：

    ```bash
    total 0 result
    ```

  - 当查询到的结果数量为1时，输出第一行变更为：

    ```bash
    total 1 result
    ```
