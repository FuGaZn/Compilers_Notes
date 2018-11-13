[TOC]

## 概述
语法分析器是编译器的核心，语法分析器从词法分析器获得一个由词法单元组成的串，并验证这个串可以由源语言的文法生成。
语法分析器大体上可以分为三种类型：通用的、自顶向下的和自底向上的。
语法分析器的输入总是按照从左向右的方式被扫描，每次扫描一个符号。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2018111218535483.png)
自顶向下文法典型为LL(1)文法，自下而上文法典型为LR文法。其中LR文法包括SLR、LR(1)、LALR(1)文法。
各文法之间的关系。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181112190029752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
下面将依次讲解各文法

## LL(1)文法
### LL(1)文法的判定
判断文法G是否是LL(1)文法，有两种判断方法，一是根据以下定义：
一个文法G是LL(1)的，当且仅当G的任意两个不同的产生式A->α|β满足下面条件：
1) 不存在终结符a使得α和β都能够推导出以a开头的串。
2)	α和β最多只有一个可以推导出空串。
3)	如果β=>ε，那么α就不能推导出任何以Follow(A)中某个终结符开头的串。

即：First(α)和First(β)不相交。如果β=>ε，那么First(α)和Follow(A)不相交。
<br>

另一种方法就是构造出预测分析表。

LL(1)文法的表驱动分析过程：
1. 预处理，包括消除左递归和提取左公因子
2. 写出First集和Follow集
3. 构造LL PPT(Predictive Parsing Table)
   <br>


### 消除左递归
消除立即左递归的算法：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2018111219370388.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
混合左递归的消除原则：
- 对于嵌套环，由内而外消除
- 对于前后多个环，那么从左向右消除。
- 消除的结果中开始符不能变。

### 提取左公因子
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181112193905846.png)

### First集合
定义：First(α)被定义为可从α推导得到的串的首符号的集合。其中α是任意的文法符号串。

First(X)的计算规则：
1)	如果X是一个终结符，那么First(X) = X
2)	如果X是一个非终结符，X的格式如X-> aB（a是终结符），那么把a加入到First(X)集合中。
3)	如果X是非终结符，且其形如X->Y1Y2…Yk（Yi皆是非终结符）。那么从左向右查看所有Yi，把First(Yi)加入到First(X)中，如果First(Yi)包括ε，则再把First(Yi+1)加入到First(X)中，如此重复，直到下一个Y的First不包含ε为止。
4)	如果X->ε是一个产生式，那么把ε加入到First(X)中。

### Follow集合
定义：Follow(A)：follow(A)是产生A的产生式中紧跟着A的后面的字符串。
Follow集合的计算规则：
1)	将\$加入到Follow(S)中，S是开始符号，而\$表示输入右端的结束标记。
2)	如果A的右边是终结符，那么把这个终结符加入到Follow(A)中
3)	对于产生式A->αBβ，把First(β) - {ε}加入到Follow(B)中
如果β为ε（即产生式A->αB），或者First(β)包含ε，则把Follow(A)的所有符号加入到Follow(B)中

### 预测分析表的构造
构造方法：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2018111219504196.png)
### 表驱动推导实例
下面我们将根据龙书上的一个实例来加深对以上概念的印象。
一个指明了运算符的结合性和优先级的文法：
文法G:
1. E->E+T|T
   1\. T->T* F|F
   2\. F->(E) | id

**第一步，我们消除左递归。**
根据“由内而外，从左到右”的顺序。
1. 产生式2不存在左递归。
2. 产生式1存在左递归，需要消除：
   令T->FT', T' -> * FT' | ε
3. 产生式0存在左递归，需要消除;
   令E-> TE', E' -> + TE' | ε

综上，消除左递归处理后的文法如下：
文法G`:
0\. E -> TE'
1\. E' -> + TE' | ε
2\. T -> FT'
3\. T' -> * FT' | ε
4\. F -> (E) | id

**第二步，我们提取左公因子。**
文法G`中不存在左公因子，所以不需要提取。

**第三步，写出First和Follow集合。**
First(E) = First(T) = First(F) = {(, id}
First(E') = {+, ε}
First(T') = {*, ε}

Follow(E) = Follow(E') = {), \$}
**解析**：E是开始符，所以Follow(E)包含ε，根据产生式F->(E)可得，Follow(E)包含右括号。E'只出现在E产生式的右边尾部，所以Follow(E')必然和Follow(E)相同。

Follow(T) = Follow(T') = {+, ), \$}
**解析**：在产生式E' -> + TE' 中，E'在T的后面，所以Follow(T)包含了First(E') -{ε} 中的符号，即符号+
又因为First{E'}中包含ε，所以要把把Follow(E)的所有符号加入到Follow(T)中，即符号)和ε。所以Follow(T) = {+, ), \$}
因为T'只出现在T产生式的右边尾部，所以Follow(T')必然和Follow(T)相同。

Follow(F) = {+, \*\, ), \$}
**解析**：根据产生式T -> FT'，Follow(F)包含了First(T') - {ε} 中的符号，即符号\*\
因为First{T'}中包含ε，所以要把把Follow(T)的所有符号加入到Follow(F)中，即符号+、)和\$。所以Follow(T) = {+, *,  ), \$}

**第四步，构建预测分析表**
我们先来把构造算法再看一遍。
对产生式A->α：
1） 对于First(α)中的每个终结符号α，将A->α加入到M[A, α]中。
2） 如果ε在First(α)中，那么对于Follow(A)中的每个终结符号b，将A->α加入到M[A, b]中。如果ε在First(α)中，且\$在Follow(A)中，也将A->α加入到M[A, \$]中。

在本题中，已知的终结符号包括：i、+、*、(、)、\$。非终结符包括：E、E'、T、T'、F

对E->TE'，First(TE') =  {(, id}，所以要将产生式写入M[E, (]和M[E,id]中。
对E' -> + TE'，First(+TE') = {+}，所以将产生式写入M{E', +]中
对E' -> ε，First(ε) = {ε}，所以对Follow(E')中的符号)和ε，要将产生式写入[E', ε]和[E', )]中
对T -> FT'，First(FT') = {(, id}，所以要将产生式写入M[T, (]和M[T,id]中。
剩下略。

所以，最后得到的分析表如下

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181112202234545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvbGxvd2VyX0pD,size_16,color_FFFFFF,t_70)
可以得出结论，文法G`是LL(1)文法。而文法G存在等价的LL(1)文法
//最终只能证明G'是LL(1)文法，而不能说G就是LL文法（因为G存在左递归）
