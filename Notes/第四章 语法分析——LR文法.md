[TOC]

# 概述
在LR(k)文法中，L指对输入进行从左到右的扫描，R表示反向构造一个最右推导序列。k表示在做出语法分析决定时向前看k个输入符号。
常用的LR(k)文法包括：
- SLR：简单LR
- LR(1)：规范LR
- LALR：向前看LR（Look ahead）

# 基本概念
## 移动-归约语法分析技术
1)	移入shift：将下一个输入符号移到栈的顶端。
2)	归约reduce
3)	接受accept：宣布语法分析过程成功完成
4)	报错error

冲突：
非LR文法中会存在移入/归约冲突或者归约/归约冲突。
（冲突一定发生在归约时）

# SLR
SLR表驱动分析过程：
1. 构造增广文法
2. 状态内部扩展和状态之间的扩展
3. 构建文法分析表

下面将根据分析过程依次讲述其中的概念。

## 增广文法
>G的增广文法G’是在G中加上新开始符号S’和产生式S’->S而得到的文法。引入这个新的开始产生式的目的是告诉语法分析器何时应该停止语法分析并宣称接受输入符号串。
## 状态内部扩展
状态内部的扩展就是写出项集族的闭包（**CLOSURE**）
首先我们理解**项**的概念：
> 一个文法G的一个LR(0)项是G的一个产生式再加上一个位于它的体中某处的点。
> 比如，A->XYZ产生了四个项：
> - A->  · XYZ
> - A-> X · YZ
> - A-> XY · Z
> - A-> XYZ ·
>
> 产生式A->ε只生成一个项A->·
>
> 然后我们来了解一下如何构造项集的闭包
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113102918144.png)
## 状态之间的扩展
状态之间的扩展就是移点，即**GOTO**函数
> GOTO(I, X)被定义为I中所有形如[A->α · Xβ]的项所对应的项[A-αX·β]的集合的闭包。

比如对集合 I { [E->E·+T] }，GOTO(I, +) = { [E->E+·T] }

## 构建分析表
LR语法分析表由state、ACTION和GOTO三部分组成
### ACTION
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113103933985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)

### 构造SLR语法分析表
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113104323363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
## SLR分析实例
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113104533470.png)
这是龙书上的习题。
我们按照步骤来：
第一步 构造增广文法：
S' -> S
S -> Aa
S -> bAc
S -> dc
S -> bda
A -> d
第二步 构造项集族闭包和状态之间的扩展
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113142206985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
第三步，构建语法分析表
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113110912903.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
可见在表中存在移入/归约冲突，所以该文法不是SLR文法。

# LR(1)文法
若[A→α•Bβ]∈项目集I，则\[B→•γ](B→γ为一产生式)也包含在I中，不妨考虑，把FIRST(β)作为用产生式B→γ归约的搜索符，称为**向前搜索符**，作为归约时查看的符号集合，用以代替SLR(1)分析中的FOLLOW集，把此搜索符号的集合也放在相应项目的后面，这种处理方法即为LR(1)方法。
（SLR(1)和LR(1)的区别在于LR(1)多使用了一个预判信息，即项目后面的符号如A →•e,c中的c，这个预判信息是用first集而非follow集得出的）

## LR(1)项集的闭包
CLOSURE(I)按如下方式构造：
1. 	I的任何项目都属于CLOSURE(I)
2. 	若有项目[A→α•Bβ,a ]属于CLOSURE(I)，B→γ 是文法中的产生式，β∈V*，b∈FIRST(βa)， 则[B→•γ,b]也属于CLOSURE(I)中。
3. 	重复b)直到CLOSURE(I)不再增大为止。

## 构建LR(1)语法分析表

文法分析表由State、Action、GOTO组成
Si表示移入到状态Ii，Ri表示按照第i个产生式进行归约。
如果一个文法的LR(1)分析表中不含多重入口时(或任何一个LR(1)项目集中无移进/归约或归约/归约的冲突,则称该文法为LR(1)文法.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113143324502.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)

## LR(1)分析实例
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113141553915.png)
第一步 构建增广文法
S'->S
S->Aa
S->bAc
S->Bc
S->bBa
A->d
B->d

第二步 构建项集闭包和和状态转换
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113143018852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
第三步 构建语法分析表
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113143101346.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
表中不存在冲突，所以是LR(1)文法

# *LALR文法
## 构造LALR分析表
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113144121644.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://img-blog.csdnimg.cn/201811131441382.png)
LALR(1)文法就是在LR(1)文法的基础上做同心集合并。
如果合并后的分析表不存在冲突，则说明是LALR文法，否则就不是。
## LALR实例
我们还看之前那道题：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113141553915.png)
LALR的DFA有限状态机转换图是和LR(1)文法一样的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113143018852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
在图中，发现I5和I9可以合并。
合并后的项集族为I59:
A->d. , a/c
B->d. , a/c
构建得的新分析表如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113145015718.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
表中存在归约/归约冲突，所以不是LALR文法

# 总结
SLR, LR(1), LALR之间的关系如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181113145153854.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
