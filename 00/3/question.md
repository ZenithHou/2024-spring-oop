# 简化的文本编辑器

## 题目描述

有了STL的帮助，我们可以做出很多东西。这次让我们来做一个简单的文本编辑器`TextEditor`。它的行为和一般的文本编辑器基本一致，如windows自带的记事本、vscode等。如有困惑，可在这些编辑器上尝试以作参考，但**一切请以题目描述为准**。

### `TextEditor` 的基本构成

- 文本：被编辑的对象。文本可以被加载到`TextEditor`内部进行操作，也可以由`TextEditor`输出。每个字符的位置由行号和列号组成：`(row, col)`，一切从0开始编号。

  - 对于一个𝑛*n*行的文件，𝑟𝑜𝑤∈[0,𝑛)row∈[0,*n*)（注意是左开右闭）；
  - 对于有 𝑘*k* 个字符的一行，𝑐𝑜𝑙∈[0,𝑘]col∈[0,*k*]（注意两边都是闭区间）。𝑐𝑜𝑙=0col=0 表示第一个字符的位置，𝑐𝑜𝑙=𝑘col=*k* 表示该行最后一个换行符的位置。

  例如：

  ```none
  abc
  hello
  ```

  (0,0)(0,0) 为**字符a的位置**，(1,5)(1,5) 为o之后**换行符的位置**。

- 光标：指示正在进行编辑的位置，也由行号和列号确定，即`(row, col)`，代表此时光标在`(row, col)`位置的字符**之前**。例如：

  ```none
  abc
  h*ello
  ```

  光标位置为 (1,1)(1,1)。

### `TextEditor `需要实现的功能

- 创建编辑器：

  - 构造函数（`TextEditor`）：创建编辑器。初始状态下，文本为空（即没有任何文本，打印的时候表现为一个空行），光标位置为(0,0)(0,0)（即光标在这个空行的开头），处于非选中状态。

- 操作光标：

  - 移动光标（`MOVE`）：接受一个位置`dest`，移动光标至该位置（你可以认为先移动行，再移动列）。如果该位置超出了现有数据的范围，则将光标移动至离`dest`最近的边界。例如：

    ```none
    //初始
    *abc
    hello
    // MOVE 1 4
    abc
    hell*o
    // MOVE 5 5
    abc
    hello*
    // MOVE -5 10
    abc*
    hello
    // MOVE 5 2
    abc
    he*llo
    ```

- 编辑文本：

  - 写入（`WRITE`）：接受一个字符串`data`，在光标处写入该字符串，写入完成后，光标移动至写入内容末尾。例如：

    ```none
    // 初始
    *abc
    // WRITE hello
    hello*abc
    ```

  - 换行（`NEWLINE`）：在光标所在行下方新插入一行，将光标所在行光标之后的字符移动至新行。换行后，光标位于新一行开头。例如：

    ```none
    // 初始
    abc*
    // NEWLINE
    abc
    *
    ```

  - 删除（`DELETE`）：删除光标位置前的一个字符，光标位置不变。光标位于行开头时，将会删除上一行的换行符，并将当前行与上一行合并；而若光标位于 (0,0)(0,0)，则**不会有任何操作**。例如：

    ```none
    // 初始
    a*bc
    // DELETE
    *bc
    ```

    ```none
    // 初始
    ab
    c
    *d
    // DELETE
    ab
    c*d
    ```

  - 撤销（`UNDO`）：撤销最近一次**编辑文本**的操作，操作完成后**文本和光标**都变为最近一次**编辑文本**操作前的状态。注意：可以多次使用 `UNDO`，撤销多步操作，因此你可能要记录文本编辑器全部的历史状态。例如：

    ```none
    // 初始
    *abc
    // WRITE hello
    hello*abc
    // MOVE 0 2
    he*lloabc
    // UNDO
    *abc
    ```

- 查看文字

  - 截屏（`SCREEN`）：将目前文本编辑器处理得到的文本逐行输出至标准输出。用`*`标出光标的位置。

    输出格式：先输出 `### SCREENSHOT BEGIN ###`，然后逐行输出当前编辑器中的文本。最后输出 `### SCREENSHOT END ###`。

    例如：

    光标在(0,2)(0,2)处：

    ```none
    ### SCREENSHOT BEGIN ###
    ab*c
    hello
    ### SCREENSHOT END ###
    ```

    初始状态应该输出：

    ```none
    ### SCREENSHOT BEGIN ###
    *
    ### SCREENSHOT END ###
    ```

## 输入输出格式

输入的第一行包含一个正整数 𝑚m ，表示操作数量；

接下来输入𝑚m行，每行先输入一个字符串𝑜𝑝op，为上述功能的其中之一：

- 对于光标移动操作 `MOVE`：再输入两个正整数𝑟𝑜𝑤row、𝑐𝑜𝑙col，代表光标移动的位置，保证光标的位置合法；
- 对于写入操作 `WRITE`：再输入一个字符串 𝑡𝑒𝑥𝑡text，代表写入的字符串，保证该字符串不为空，不包含换行符且不包含`*`；
- 对于其他操作：没有额外的输入。

对于每个截屏操作，按上文所述将目前文本编辑器的文本输出。

## 数据规模与约定

- 测试点1-2：仅存在创建编辑器、`MOVE`、`WRITE`、`SCREEN` 的操作。
- 测试点3-4：仅存在创建编辑器、`MOVE`、`WRITE`、`DELETE`、`SCREEN` 的操作。
- 测试点5-6：仅存在创建编辑器、`MOVE`、`WRITE`、`NEWLINE`、`DELETE`、 `SCREEN` 的操作。
- 测试点7-10：存在所有操作，综合测试。
- 对100%的数据，𝑚<=200m<=200，每次新增的字符串长度不超过20。
- 保证 `WRITE` 操作的输入为数字、大写、小写字母的组合。
- 本题不要求算法的时间、空间复杂度。朴素的实现便可以通过评测。

## 样例

见[下发文件](https://oj.cs.tsinghua.edu.cn/staticdata/2021.WrexCSZlIXotB7Qz.pub/Ex2AZi4euYnhTrJf.download.zip/download.zip)中的 `exp1.in` ~ `exp4.in` 和 `exp1.out` ~ `exp4.out`

### 要求

1. 不能修改`main.cpp`和`Makefile`，即使你修改了，也会被覆盖掉 :(
2. 下载文件提供了一些已有代码供你参考，但你可以自由选择是否使用。这些文件里还有一些帮助性的说明。 请进一步编写`editor.h`、`editor.cpp`。
3. 文件下载：[下载地址](https://oj.cs.tsinghua.edu.cn/staticdata/2021.WrexCSZlIXotB7Qz.pub/Ex2AZi4euYnhTrJf.download.zip/download.zip)

### 提交格式

请将 `editor.h`、`editor.cpp` 打包成一个 zip 格式的压缩包并上传。**注意：你的文件应该在压缩包的根目录下，而不是压缩包的一个子文件夹下，换而言之，解压你提交的压缩包后，应该直接得到一系列 cpp 文件、h 文件等代码文件，而不是一个包含它们的文件夹。**评测时，OJ会将提供的文件贴入你的目录下进行编译并执行。