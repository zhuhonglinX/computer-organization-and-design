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

![2-1](/Users/zhuhonglin/resources/other/computer-organization-and-design/images/2-1.png)



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











