
# 常用命令速查表

|命令|简写|作用|
|---|---|---|
|run|r|运行程序|
|break|b|设置断点|
|delete|d|删除断点|
|info breakpoints|info b|查看断点|
|next|n|单步执行，不进入函数|
|step|s|单步执行，进入函数|
|continue|c|继续运行|
|finish|无|执行完当前函数|
|until|无|执行到指定位置|
|print|p|查看变量|
|display|无|自动显示变量|
|undisplay|无|取消自动显示|
|info locals|无|查看局部变量|
|info args|无|查看函数参数|
|backtrace|bt|查看调用栈|
|frame|f|切换栈帧|
|list|l|查看源码|
|disassemble|disas|查看汇编|
|info registers|i r|查看寄存器|
|x|无|查看内存|
|nexti|ni|单步执行汇编|
|stepi|si|单步进入汇编|
|info threads|无|查看线程|
|thread|无|切换线程|
|quit|q|退出 GDB|

---

# 推荐学习顺序

建议按照以下顺序学习：

```text
1. 编译程序
       ↓
2. gdb 启动
       ↓
3. break 设置断点
       ↓
4. run 运行程序
       ↓
5. next 单步执行
       ↓
6. step 进入函数
       ↓
7. print 查看变量
       ↓
8. info locals 查看局部变量
       ↓
9. backtrace 查看调用栈
       ↓
10. continue 继续执行
```

---

核心命令记忆口诀

```text
b 断点
r 运行
n 下一行
s 进入函数
c 继续运行
p 查看变量
bt 查看调用栈
q 退出
```

---

# 学习建议

初学 GDB 时，重点掌握以下命令即可：

```gdb
b
r
n
s
c
p
info locals
info args
bt
q
```

熟练掌握后，再学习：

```gdb
条件断点
调用栈
寄存器
内存
汇编调试
多线程调试
```

---
# 一、准备测试程序
````md
文件名：
gdb_demo.cpp
````

完整源码如下：

```cpp
#include <iostream>

using namespace std;

// 两个数相加
int add(int a, int b)
{
    int result = a + b;

    return result;
}

// 计算 1 到 n 的累加和
int sumTo(int n)
{
    int sum = 0;

    for (int i = 1; i <= n; i++)
    {
        sum += i;
    }

    return sum;
}

int main()
{
    // 定义两个变量
    int x = 10;
    int y = 20;

    // 调用 add 函数
    int z = add(x, y);

    // 调用 sumTo 函数
    int total = sumTo(10);

    // 输出结果
    cout << "z = " << z << endl;
    cout << "total = " << total << endl;

    return 0;
}
```

---

# 二、程序正常运行结果

正常运行程序后，输出：

```text
z = 30
total = 55
```

---

# 三、编译程序

## 1. 进入源码所在目录

```bash
cd ~/gdb-test
```

## 2. 使用 GDB 调试信息进行编译

```bash
g++ -g -O0 gdb_demo.cpp -o gdb_demo
```

### 参数说明

#### `-g`

生成 GDB 所需要的调试信息。

如果没有 `-g`，GDB 无法很好地显示：

- 源代码
    
- 变量名称
    
- 函数信息
    
- 行号信息
    

#### `-O0`

关闭编译器优化。

推荐学习 GDB 时使用：

```bash
-O0
```

避免编译器优化导致：

- 变量被优化掉
    
- 执行顺序发生变化
    
- 调试位置与源码不一致
    

#### `-o gdb_demo`

指定生成的可执行文件名称。

最终生成：

```text
gdb_demo
```

---

# 四、检查是否编译成功

执行：

```bash
ls
```

应该看到：

```text
gdb_demo
gdb_demo.cpp
```

说明：

```text
gdb_demo.cpp
```

是源代码文件。

```text
gdb_demo
```

是编译生成的可执行文件。

---

# 五、直接运行程序

执行：

```bash
./gdb_demo
```

输出：

```text
z = 30
total = 55
```

说明程序运行正常。

---

# 六、启动 GDB

使用以下命令启动：

```bash
gdb ./gdb_demo
```

进入 GDB 后，可以看到类似：

```text
GNU gdb ...

(gdb)
```

此时已经进入 GDB 调试环境。

---

# 七、GDB 基础命令

## 1. 查看帮助

```gdb
help
```

查看所有帮助信息。

也可以查看指定命令：

```gdb
help break
```

例如查看：

```text
break
```

命令的使用方法。

---

## 2. 运行程序

```gdb
run
```

或者：

```gdb
r
```

程序会从 `main()` 开始执行。

例如：

```gdb
(gdb) run
```

输出：

```text
z = 30
total = 55
```

程序执行结束后：

```text
[Inferior 1 (process xxxx) exited normally]
```

---

# 八、查看源代码

## 1. 查看当前附近的代码

```gdb
list
```

或者：

```gdb
l
```

例如：

```gdb
(gdb) list
```

可能看到：

```cpp
int main()
{
    int x = 10;
    int y = 20;

    int z = add(x, y);

    int total = sumTo(10);
}
```

---

## 2. 查看指定函数

```gdb
list main
```

查看：

```cpp
main()
```

函数附近的源码。

查看：

```gdb
list add
```

查看 `add()` 函数。

查看：

```gdb
list sumTo
```

查看 `sumTo()` 函数。

---

## 3. 查看指定行号

例如：

```gdb
list 20
```

查看第 20 行附近的代码。

---

# 九、设置断点

断点是 GDB 最核心的功能之一。

程序运行到断点位置时会暂停。

---

## 1. 在 main 函数设置断点

```gdb
break main
```

简写：

```gdb
b main
```

例如：

```gdb
(gdb) b main
```

输出类似：

```text
Breakpoint 1 at 0x...
```

---

## 2. 在指定函数设置断点

例如：

```gdb
break add
```

或者：

```gdb
b add
```

程序执行到：

```cpp
int add(int a, int b)
```

时暂停。

---

## 3. 在指定行设置断点

例如：

```gdb
break 25
```

或者：

```gdb
b 25
```

表示在第 25 行设置断点。

也可以指定文件：

```gdb
break gdb_demo.cpp:25
```

---

# 十、查看断点

执行：

```gdb
info breakpoints
```

或者：

```gdb
info b
```

例如：

```text
Num     Type           Disp Enb Address            What
1       breakpoint     keep y   0x...              main
2       breakpoint     keep y   0x...              add
```

说明当前设置了两个断点。

---

# 十一、删除断点

## 删除指定断点

例如删除编号为 1 的断点：

```gdb
delete 1
```

简写：

```gdb
d 1
```

---

## 删除所有断点

```gdb
delete
```

GDB 会询问：

```text
Delete all breakpoints? (y or n)
```

输入：

```text
y
```

即可。

---

# 十二、禁用和启用断点

## 禁用断点

```gdb
disable 1
```

禁用编号为 1 的断点。

断点不会删除，只是暂时不生效。

---

## 启用断点

```gdb
enable 1
```

重新启用断点。

---

# 十三、开始调试

先在 `main` 设置断点：

```gdb
b main
```

然后运行：

```gdb
run
```

程序会停在：

```cpp
int main()
{
```

或者函数中的第一条可执行语句。

GDB 会显示类似：

```text
Breakpoint 1, main () at gdb_demo.cpp:24
```

---

# 十四、查看当前位置

程序暂停后，可以使用：

```gdb
list
```

查看当前位置附近代码。

也可以使用：

```gdb
frame
```

查看当前执行位置。

---

# 十五、单步执行

GDB 中最常用的单步命令有两个：

```text
next
```

和：

```text
step
```

它们的区别非常重要。

---

# 十六、next：单步执行，不进入函数

命令：

```gdb
next
```

简写：

```gdb
n
```

例如当前代码：

```cpp
int z = add(x, y);
```

执行：

```gdb
next
```

GDB 会直接执行完整个：

```cpp
add(x, y)
```

不会进入：

```cpp
add()
```

函数内部。

适合：

- 不关心函数内部实现
    
- 只想查看当前函数执行过程
    

---

# 十七、step：单步执行，进入函数

命令：

```gdb
step
```

简写：

```gdb
s
```

例如当前代码：

```cpp
int z = add(x, y);
```

执行：

```gdb
step
```

GDB 会进入：

```cpp
int add(int a, int b)
{
```

函数内部。

适合：

- 查看函数内部逻辑
    
- 分析函数执行过程
    

---

# 十八、next 和 step 的区别

假设当前代码：

```cpp
int z = add(x, y);
```

使用：

```gdb
next
```

效果：

```text
main
 │
 │ next
 ▼
直接执行 add()
 │
 ▼
继续 main
```

使用：

```gdb
step
```

效果：

```text
main
 │
 │ step
 ▼
进入 add()
 │
 ▼
执行 add() 内部代码
```

总结：

|命令|简写|是否进入函数|
|---|---|---|
|next|n|否|
|step|s|是|

---

# 十九、查看变量

程序暂停后，可以使用：

```gdb
print 变量名
```

例如：

```gdb
print x
```

或者：

```gdb
p x
```

输出：

```text
$1 = 10
```

查看：

```gdb
p y
```

输出：

```text
$2 = 20
```

---

# 二十、查看函数参数

进入：

```cpp
int add(int a, int b)
```

后：

```gdb
p a
```

输出：

```text
$1 = 10
```

查看：

```gdb
p b
```

输出：

```text
$2 = 20
```

---

# 二十一、查看局部变量

在：

```cpp
int add(int a, int b)
{
    int result = a + b;
}
```

执行到 `result` 定义之后：

```gdb
p result
```

输出：

```text
$3 = 30
```

---

# 二十二、自动显示变量

使用：

```gdb
display x
```

之后每执行一次：

```gdb
next
```

GDB 都会自动显示：

```text
x = 10
```

---

## 查看自动显示列表

```gdb
info display
```

---

## 取消自动显示

例如：

```gdb
undisplay 1
```

取消编号为：

```text
1
```

的自动显示。

---

# 二十三、继续运行

当程序暂停时：

```gdb
continue
```

简写：

```gdb
c
```

程序会继续运行，直到：

- 遇到下一个断点
    
- 程序执行结束
    

例如：

```gdb
c
```

---

# 二十四、进入 add() 函数调试

设置断点：

```gdb
b add
```

然后：

```gdb
run
```

程序会运行到：

```cpp
int add(int a, int b)
```

暂停。

查看参数：

```gdb
p a
```

输出：

```text
10
```

查看：

```gdb
p b
```

输出：

```text
20
```

执行：

```gdb
n
```

程序执行：

```cpp
int result = a + b;
```

然后查看：

```gdb
p result
```

输出：

```text
30
```

---

# 二十五、finish：执行完当前函数

如果当前正在：

```cpp
add()
```

函数内部。

执行：

```gdb
finish
```

GDB 会：

```text
执行完当前函数
↓
返回调用者
↓
暂停
```

例如：

```text
add()
  ↓
finish
  ↓
main()
```

适合：

```text
不想继续逐行调试当前函数
```

---

# 二十六、until：运行到指定位置

例如当前正在循环中：

```cpp
for (int i = 1; i <= n; i++)
{
    sum += i;
}
```

可以使用：

```gdb
until
```

让程序执行到当前循环之后。

也可以：

```gdb
until 15
```

运行到第 15 行。

---

# 二十七、调试 sumTo() 函数

设置断点：

```gdb
b sumTo
```

运行：

```gdb
run
```

程序暂停：

```cpp
int sumTo(int n)
{
    int sum = 0;
```

查看参数：

```gdb
p n
```

输出：

```text
10
```

执行：

```gdb
n
```

然后：

```gdb
p sum
```

输出：

```text
0
```

---

# 二十八、观察循环变量

进入循环后：

```cpp
for (int i = 1; i <= n; i++)
{
    sum += i;
}
```

查看：

```gdb
p i
```

查看当前：

```text
i
```

的值。

查看：

```gdb
p sum
```

查看当前累加结果。

---

# 二十九、使用 display 观察循环

可以设置：

```gdb
display i
```

以及：

```gdb
display sum
```

之后执行：

```gdb
n
```

GDB 会自动显示：

```text
i = 1
sum = 0
```

继续执行：

```text
i = 1
sum = 1
```

继续：

```text
i = 2
sum = 1
```

这样可以非常方便地观察循环执行过程。

---

# 三十、查看调用栈

使用：

```gdb
backtrace
```

简写：

```gdb
bt
```

例如：

```text
#0  add(int, int)
#1  main()
```

表示：

```text
main()
 │
 ▼
add()
```

当前正在：

```text
add()
```

函数内部。

---

# 三十一、查看完整调用栈

```gdb
bt full
```

可以查看更多信息。

包括：

- 函数参数
    
- 局部变量
    

---

# 三十二、切换栈帧

假设：

```gdb
bt
```

显示：

```text
#0 add()
#1 main()
```

当前在：

```text
#0
```

使用：

```gdb
frame 1
```

或者：

```gdb
f 1
```

切换到：

```text
main()
```

函数。

---

# 三十三、查看当前函数参数

使用：

```gdb
info args
```

例如：

```text
a = 10
b = 20
```

---

# 三十四、查看局部变量

使用：

```gdb
info locals
```

例如：

```text
result = 30
```

---

# 三十五、修改变量

GDB 可以直接修改程序变量。

例如：

```gdb
set variable x = 100
```

或者：

```gdb
set x = 100
```

然后：

```gdb
p x
```

输出：

```text
100
```

> 注意：修改变量只影响当前调试过程，不会修改源代码。

---

# 三十六、条件断点

普通断点：

```gdb
b 15
```

无论什么情况都会暂停。

条件断点可以设置：

```gdb
break 15 if i == 5
```

只有：

```cpp
i == 5
```

时才暂停。

---

## 示例

在：

```cpp
for (int i = 1; i <= n; i++)
```

设置：

```gdb
b 15 if i == 5
```

程序循环到：

```text
i = 5
```

时暂停。

---

# 三十七、忽略断点

假设断点编号：

```text
1
```

可以设置：

```gdb
ignore 1 5
```

表示：

```text
前 5 次经过断点时不暂停
```

第 6 次才暂停。

---

# 三十八、查看程序状态

使用：

```gdb
info program
```

可以查看程序当前状态。

例如：

```text
It stopped at breakpoint...
```

---

# 三十九、查看寄存器

使用：

```gdb
info registers
```

可以查看：

```text
rax
rbx
rcx
rdx
rip
rsp
rbp
```

等寄存器。

例如：

```text
rax  0x...
rbx  0x...
rip  0x...
rsp  0x...
```

---

# 四十、查看特定寄存器

例如：

```gdb
p $rax
```

查看：

```text
rax
```

寄存器。

查看：

```gdb
p $rip
```

查看当前指令地址。

---

# 四十一、查看内存

基本格式：

```gdb
x/NFU 地址
```

其中：

```text
N
```

表示查看数量。

```text
F
```

表示显示格式。

```text
U
```

表示数据单位。

---

## 示例

查看当前栈：

```gdb
x/10x $rsp
```

表示：

```text
查看 $rsp 开始的 10 个数据
以十六进制显示
```

---

## 常用格式

### 十六进制

```gdb
x/x 地址
```

### 十进制

```gdb
x/d 地址
```

### 字符

```gdb
x/c 地址
```

### 字符串

```gdb
x/s 地址
```

---

# 四十二、查看汇编代码

查看当前函数：

```gdb
disassemble
```

查看指定函数：

```gdb
disassemble main
```

或者：

```gdb
disassemble add
```

---

# 四十三、查看源代码和汇编混合模式

使用：

```gdb
disassemble /m main
```

可以看到：

```text
源代码
+
汇编代码
```

例如：

```text
int x = 10;
=> mov ...
```

这对于学习：

- C++
    
- 汇编
    
- 编译原理
    
- 逆向分析
    

非常有帮助。

---

# 四十四、查看当前执行指令

使用：

```gdb
x/i $pc
```

或者：

```gdb
x/i $rip
```

可以查看当前正在执行的汇编指令。

---

# 四十五、单步执行汇编

## 单步执行一条机器指令

```gdb
nexti
```

简写：

```gdb
ni
```

---

## 进入汇编函数

```gdb
stepi
```

简写：

```gdb
si
```

区别：

|命令|作用|
|---|---|
|next|源代码级单步，不进入函数|
|step|源代码级单步，进入函数|
|nexti|汇编级单步，不进入函数|
|stepi|汇编级单步，进入函数|

---

# 四十六、查看线程

多线程程序中：

```gdb
info threads
```

可以查看所有线程。

例如：

```text
* 1 Thread ...
  2 Thread ...
```

其中：

```text
*
```

表示当前线程。

---

# 四十七、切换线程

例如：

```gdb
thread 2
```

切换到：

```text
线程 2
```

---

# 四十八、退出 GDB

使用：

```gdb
quit
```

简写：

```gdb
q
```

如果程序正在运行，会提示：

```text
A debugging session is active.

Quit anyway? (y or n)
```

输入：

```text
y
```

退出。

---

# 四十九、完整调试流程示例

启动：

```bash
gdb ./gdb_demo
```

---

设置断点：

```gdb
b main
```

运行：

```gdb
run
```

查看代码：

```gdb
list
```

执行：

```gdb
next
```

查看变量：

```gdb
p x
```

继续：

```gdb
n
```

查看：

```gdb
p y
```

---

遇到：

```cpp
int z = add(x, y);
```

使用：

```gdb
step
```

进入：

```cpp
add()
```

查看参数：

```gdb
info args
```

查看局部变量：

```gdb
info locals
```

单步执行：

```gdb
n
```

查看：

```gdb
p result
```

执行：

```gdb
finish
```

返回：

```cpp
main()
```

---

继续：

```gdb
n
```

进入：

```cpp
sumTo(10)
```

查看：

```gdb
info args
```

设置自动显示：

```gdb
display i
display sum
```

然后：

```gdb
n
```

观察：

```text
i
sum
```

的变化。

---


# 总结

GDB 调试的核心流程可以理解为：

```text
启动程序
   ↓
设置断点
   ↓
运行程序
   ↓
程序暂停
   ↓
查看变量
   ↓
单步执行
   ↓
进入函数
   ↓
查看调用栈
   ↓
继续执行
```

最常用的几个命令：

```text
b → 设置断点
r → 运行程序
n → 下一步
s → 进入函数
p → 查看变量
c → 继续运行
bt → 查看调用栈
q → 退出
```

掌握这些命令后，已经可以完成大部分基础 C/C++ 程序的 GDB 调试工作。