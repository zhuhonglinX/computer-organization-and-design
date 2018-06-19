# Instructions: Language of the Computer

指令：计算机的语言



## 2.1 Introduction

为了控制计算机的硬件，我们必须会说计算机听得懂的语言，这种语言我们叫做**指令**。

我们选用的指令集来自 MIPS 技术，这是 1980 年代提出的一种指令集技术。除此之外，目前也有其他热门的指令集，比如 ARM 系列， Intel x86 系列。

虽然有很多的指令集，但是不同的指令集之间是很相似的，因为今天的计算机总是有差不多的硬件技术，底层的设计原则也一致。

## 2.2 Operations of the Computer Hardware

每一台计算机都必须会基本的算术。比如 MIPS 的汇编语法

``` assembly
add a, b, c
```

MIPS 硬性规定，必须是一个操作符后跟三个变量。看下图 MIPS 语法介绍

![2-1](./images/2-1.png)



## 2.3 Operands of the Computer Hardware

计算机硬件的操作数

MIPS 中，一般是 32 个寄存器，每个寄存器存储 32 bit 的数据。我们把 32 bit 的数据，叫做一个字（word）。

#### Memory Operands

这里介绍了两个指令：lw 和 sw。分别表示从内存中载入数据到寄存器（load word），从寄存器将数据存入内存（store words）。

首先需要说明的是，我们的例子全部使用的是 MIPS 32 位机器，其次，所有的计算操作必须依靠寄存器完成，这也就是为什么需要 lw 和 sw 的原因。

现在假设存在一个数组 A，100 个元素，每个元素占一个字（四个字节）。

比如需要完成这个操作：g = h + A[8];

```assembly
lw $t0, 32($s3) 		# Temporary reg $t0 gets A[8]
add $s1, $s2, $t0 		# g = h + A[8]
```

Q & A：

1. t0，s1，s2，s3 是什么

   答：MIPS 内置的 32 个寄存器之中的三个，s2 保存的是 h， s1 保存计算结果。s3 保存的是数组 A 的起始地址。

2. 为什么是 32($s3)

   答：这里的 32，单位是字节（Byte），内存中，基本的单位一定是字节。可以看到单位的转换：1 word = 4 Bytes = 32 bit，所以这个例子里，A[8]，表示从**基准地址**开始偏移 8 个字，也就是 32 个字节。

> 补充：
>
> 这里可以看到，我们的寄存器可以容纳 32 bit 的内容，而这里的寄存器 $s3 保存的是内存的地址，所以一个 32 位的寄存器，最多只能表示出 4G 个不同的地址，因此，一个 MIPS 32 最大支持 4GB 的内存

同理，看看 sw 的应用，比如完成这个操作：A[12] = h + A[8]

```assembly
lw 	$t0, 32($s3)		# Temporary reg $t0 gets A[8]
add $t0, $s2, $t0		# Temporary reg $t0 gets h + A[8]
sw 	$t0, 48($s3)		# Stores h + A[8] back into A[12]
```

为什么我们要多此一举，不能直接操作内存计算么，MIPS 这么规定的理由就是寄存器的存取更快，计算更快完成。



#### Constant or Immediate Operands

常数操作，也就是立即数操作，如下

```assembly
addi $s3, $s3, 4	# $s3 = $s3 + 4
```

addi 是一个新指令，专门用来做立即数的加法。

这里特别注意，MIPS 的一个寄存器，名字叫做 $zero，这个寄存器的值始终是零。为什么要弄这一个寄存器？

我们可以想到，计算机需要做很多 move 操作，比如把这个数据移到另一个寄存器上，这时并没有一个专门的移动操作，而是用 add 代替，这时候add zero 再赋值就很顺利了。而且为了更加的迅速，直接专门造了个寄存器，永远等于 0。



## 2.4 Signed and Unsigned Numbers

介绍了一下补码。

计算机需要同时保有正数和负数两种形式，比如内存的地址就是正数，而数字计算会涉及负数。比如 C 语言，会在编码层面就做出区分，如 int 和 unsigned int。



## 2.5 Representing Instructions in the Computer

MIPS 指令格式：

![2-2](./images/2-2.png)

![2-3](./images/2-3.png)

前一种叫 R-Type，后一种叫做 I-Type。

R-Type 用于寄存器的计算，如

```assembly
add $t0, $s1, $s2
```

在 MIPS 里， t0 编号是 8，s1 和 s2 分别是17、18。

所以二进制的指令会变成：

![2-4](./images/2-4.png)

![2-5](./images/2-5.png)

I-Type 涉及立即数或者地址的运算

![2-6](/Users/zhuhonglin/resources/other/computer-organization-and-design/images/2-6.png)

如上图给出的指令格式表。

n.a. 表示这一项不用表示出来。

> 加法和减法的 op 是一样的，我们用 funct 的不同表示加法和减法。



所有的指令最后都是用数字来表示。



## 2.6 Logical Operations

逻辑操作

左移、右移、与、或、非。

首先介绍左移和右移，在 MIPS 中，左移右移叫做 shift left logical (sll) 和 shift right logical (srl)。

```assembly
sll $t2, $s0, 4		# reg $t2 = reg $s0 << 4 bits
```

> 左移一位就是乘 2

接着是与或非，比如与

```assembly
and $t0, $t1, $t2	# reg $t0 = reg $t1 & reg $t2
```





## 2.7 Instructions for Making Decisions

我们将计算机区别于普通计算器的原因在于，计算机不仅可以完成计算，还可以进行判断（做决定）。计算机可以依据不同的输入数据，运行不同的指令。MIPS 支持两种指令，完成 if 语句的操作。

```assembly
beq register1, register2, L1
bne register1, register2, L1
```

beq 指令中，如果 r1 等于 r2，则跳转到 L1 标签处的指令开始执行。

bne 指令中，如果 r1 不等于 r2，则跳转到 L1 标签处的指令开始执行。

比如要完成下列代码

```c
if (i == j) 
    f = g + h; 
else 
    f = g – h;
```

```assembly
    bne $s3, $s4, Else	# go to Else if i ≠ j
    add $s0, $s1, $s2	# f = g + h (skipped if i ≠ j)
    j Exit				# go to Exit
Else: 
	sub $s0,$s1,$s2 
Exit:
```

除了相等和不相等的情况，还常常有不等关系，MIPS 使用一个叫做 set on less than 的指令解决

``` assembly
slt $t0, $s3, $s4 	# $t0 = 1 if $s3 < $s4
```

如果 s3 小于 s4，则 t0 赋值为 1，否则为 0。

MIPS 使用 slt，slti（立即数 slt），beq，bne 构建了所有的判断指令。

#### loops

循环指令

```c
while (save[i] == k) 
    i += 1;
```

```assembly
Loop: 
    sll $t1, $s3, 2			# Temp reg $t1 = i * 4
    add $t1, $t1, $s6		# $t1 = address of save[i]
    lw $t0, 0($t1)			# Temp reg $t0 = save[i]
    bne $t0, $s5, Exit		# go to Exit if save[i] ≠ k
    addi $s3, $s3, 1		# i = i + 1
    j Loop
Exit:
```



## 2.8 Supporting Procedures in Computer Hardware

过程或者函数是构建计算机程序的一个工具。

过程的好处就是可以让程序员可以专注于任务的某一个部分，然后使用参数变量串联这个过程和程序的其他部分。

> 过程是实现软件抽象的一种方式（我只能简单理解这句话）

执行一个过程必须遵循以下 6 步

1. 把参数放到固定的地址，以便过程可以得到参数
2. 把控制权交给这个过程
3. 过程获得所需的空间资源
4. 执行过程
5. 把结果保存到指定位置，以便之后的程序可以得到结果
6. 交还控制权，谁给的控制就换给谁



MIPS 借助两个相关的指令和一个特殊寄存器完成上述流程。

```assembly
jal ProcedureAddress
jr $ra
```

使用 jal 跳转到指定的过程处，当然在跳转之前，需要先记录一下目前的位置，内存中，也就是地址，保存在 ra 这个寄存器里。在过程处理完成之后，使用 jr 跳转到 ra 记录的地址处开始执行后续指令。

那么这里的关键就是计算机需要知道目前指令运行到哪个位置了，计算机中有一个专用的寄存器 PC，记录目前指令的地址，所以实际上，jal 指令会把 PC + 4 的值保存到 ra 寄存器中（因为 MIPS 指令是四个字节的，PC + 4 就是保存下一条指令位置的意思）。



#### Nested Procedures

这里主要的意思是，如果多次过程跳转，会遇到一个大问题，就是寄存器的内容会被覆盖，比如源程序调用了过程 A，A 又调用的 B，那么例如 ra 寄存器就被覆盖了，没办法跳回去。

解决办法是使用 **栈**。说白了就是把寄存器的值全部保存到内存的空间里，然后执行完了，再把内存里保存的内容回复到寄存器上，从而解决覆盖的问题，这个内存空间使用栈的结构来组织。

#### Allocating Space for New Data on the Stack

栈不仅存放寄存器的值，也存放一下 local 变量和自定的结构体。

#### Allocating Space for New Data on the Heap

堆是另一片可以使用的存储空间，主要提供了动态扩展数据结构的能力。



## 2.9 Communicating with People

介绍了一下 ASCII 码

众所周知，单个字符组合起来称为一个字符串，在计算机中有三种方式表示字符串：

1. 字符串的第一个位置给出字符串的长度
2. 伴随变量给出长度（比如结构体中）
3. 字符串的最后一个位置特殊字符，标记字符串结束

 其中 c 使用的第三种做法，字符串的结尾为 0 。java 用了第一种方法。



## 2.10 MIPS Addressing for 32-bit Immediates and Addresses

MIPS 寻址

通常 MIPS 的指令长度为 32 bit，所以一般使用16 bit 的立即数和地址，但也有一些时候，我们需要使用大立即数和地址，比如32位的立即数。下面介绍一个通用的解决方案。

32位的立即数如何载入寄存器：

```assembly
# 需要载入的立即数 0000 0000 0011 1101 0000 1001 0000 0000
lui $s0, 61				# 61 decimal = 0000 0000 0011 1101 binary
ori $s0, $s0, 2304
```

MIPS 的做法很简单，分部载入，先载入高16位，在计算入低16位，用到一个指令：load upper immediate

#### Addressing in Branches and Jumps

条件跳转指令和跳转指令

注意的是，首先条件分支跳转指令的最后 16 bit 表示跳转的指令的地址，但 MIPS 考虑到 16 位可以表示的地址太小了，于是使用了 PC 相对寻址（PC-relative addressing），因为大部分条件跳转的目标指令都会在当前指令的附近，所以以 PC 为跳转的基准比较好。即 PC 中的地址加上最后 16 bit 的地址才是最终指令的地址。

同时，由于 MIPS 指令都是四个字节长，所以 MIPS 中，一个字（word）表示四个字节，那么为了最大化可以表示的地址，最后 16 bit 的地址偏移单位是字，而不是字节。（我想到这是不是对齐的原因所在？）

同理，跳转指令也是以字为单位的。但是跳转指令并没有简单的加上 PC，而是使用了一种拼接的方式，这桌寻址方式称为伪直接寻址（Pseudodirect addressing），由于 Jump 指令的后 26 位表示地址，而 PC 为 32 位，所以 MIPS 把 PC 高 4 位和 Jump 的 26 位拼接，形成地址。



#### MIPS Addressing Mode Summary

寻址模式：

1. 立即数寻址：操作数是常数，包含在指令本身内部。
2. 寄存器寻址：操作数是寄存器。
3. 位移寻址：操作数在内存中，通过寄存器和立即数的和计算出目标地址。
4. PC 相对寻址：如上的条件跳转指令（conditional branch）使用。
5. 伪直接寻址：如上的跳转指令（Jump）



书中提到，现在的微处理器包括如今的 MIPS 都有 64 位的扩展。



#### Decoding Machine Language

这一节讲怎么通过 32 位的二进制还原出 MIPS 的汇编源码。

简单来说就是一步步查表比对。





## 2.11 Parallelism and Instructions: Synchronization

并行的指令

为了支持同步操作，MIPS 设置了 load linked 和 store conditional 两个指令，用于对数据读写的原子操作。设置同步指令的目的是希望操作系统级别的程序可以提供完整的库供上层使用。

原子操作的主要逻辑：在读取内存的数据到寄存器中时，加一个锁，直到准备写回到内存中时，解除这个锁定状态。

MIPS 的两条指令，其中 load linked 在读取数据时，会记录数据的地址，当有别的 load linked 指令读取相同地址的信息时，也会记录。但当 store conditional 写回数据的时候，就会判断是不是之前有很多 load，如果没有就成功写入，如果有，就说明数个处理器同时对数据进行处理了，为了不造成数据错误，会放弃这一次写回操作。



## 2.12 Translating and Starting a Program

























