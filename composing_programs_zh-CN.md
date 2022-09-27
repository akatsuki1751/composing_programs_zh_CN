# Composing Programs 翻译

> 以此感谢UCB的 John DeNero老师与CS61A 课程组
>
> 本书翻译的Github：https://github.com/kobayashilin1/composing_programs_zh_CN.git
>
> 附上学习CS61A 的RULES：
>
> A Few General Rules
>
> -  Whatever the assignment, start now!（做assignment不要拖延，立即行动）
> -  "Yes, that's really all there is, Don't fight the problem."（找到解决问题的方法，所有问题都有解决办法！）
> -  Practice is important. Don't just assume you can do it, do it!（亲手实践，不要感觉自己会就真的会了，Don't procrastinate! ）
> -  Always feel free to ask for help.（多请教，多提问，不要期待自己能够理解一切）
> -  DBC（Don't be clueless and be curious! 要试图尽量尝试理解，不要粗枝大叶）
> -  RTFM（阅读文档！）
> -  Have fun（要感觉到有趣！）
>
> *我第一次做翻译，是为了实践费曼学习法，再加之CS61A 的名声如雷灌耳，这次终于下定决心，打算自己一边学习，一边翻译下此书，若发现问题或者想进行探讨，请发送邮件到 yuzu1005@qq.com*

# Chapter 1: Building Abstractions with Functions

## Lecture 2 : Functions

### Elements of Programming（程序的基本要素）

编程语言不仅仅是指示计算机执行任务的一种手段。 该语言还可以作为一个框架，我们在其中组织我们对计算过程的想法。 程序也用于在编程社区的成员之间交流这些想法。 因此，程序必须是为人类的阅读而编写的，然后恰巧能被机器执行而已。

当我们描述编程语言的时候，我们实际应该注意的是，编程语言是一种将简单思想组合成复杂思想的工具，所有的编程语言都有如下的机制：

- 基本表达式和语句（primitive expression and statement），是所有编程语言都有的最基本的程序构建单元，
- 组合方法（means of combination），复杂的元素（compound elements）都是由简单的元素组合而成，还有
- 抽象方法（means of abstraction），复杂的元素可以被命名，并且可以被当作整体进行操作。

编程中的两种基本结构：数据（data）和函数（function），（不久后我们将会探索得知，它们并不是那么泾渭分明。）不正式地说，数据是我们想要操作的对象，函数是我们操作数据的方法与规则。因此，任何有效的编程语言都应该能够描述基本数据（类型）和基本函数，并且对函数、数据有组合、抽象这两种方法。

#### Expressions（表达式）

> 每种表达式由两部分组成：
>
> - 语法（Syntax）
> - 语义（Semantics）：
>   - 类型检查规则（静态语义）：计算出类型，或者终止，并给出错误信息。Type-checking rules（staatic semantics）: produce a type, or fail with an error.
>   - 求值规则（动态语义）：计算出值或exception 或死循环。Evaluation rules（dynamic semantics）: produce a value or infinite loop.
>
> **值与表达式的关系：**
>
> 值是一种表达式，值不需要再进一步进行求值。
>
> <img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220913180536611.png" alt="image-20220913180536611" style="zoom:80%;" />
>
> 该部分概念节选自 *CS3110 :  Data Structures and Functional Programming*

在上一节中试用了完整的Python解释器之后，我们现在重新开始，逐个元素有条不紊地开发Python语言。如果这些例子看起来过于简单，请耐心等待——更令人振奋的东西还在后面呢。

我们以**基本表达式（primitive expressions）**作为开始，python 的一种基本表达式就是**数值（number）**，更准确地说，你键入的表达式表示为以 10 为基数的数字组成。

```python
>>> 42
42
```

表示数字的表达式可以与数学运算符组合以形成一个复合表达式，解释器将对这个复合表达式进行求值：

```python
>>> -1 - -1
0
>>> 1/2 + 1/4 + 1/8 + 1/16 + 1/32 + 1/64 + 1/128
0.9921875
```

这些数学表达式使用**中缀表示法（Infix notation）**，其中**操作符（operator）**（如+、-、*或/）出现在**操作数（operand）**（数字）之间。Python 包含了许多构成复合表达式的方法。我们不会尝试立即列举所有的表达式，而是在开发过程中引入新的表达式形式，以及它们支持的语言特性。

#### Call Expressions（调用表达式）

最重要的复合表达式是**调用表达式（Call expression）**，它将函数运用于某些参数。回想一下代数学中函数的数学概念：函数就是从一些输入参数到输出值的映射。如 `max` 函数是取一堆输入值里面最大的那个数值，作为结果输出。Python 中函数应用的方式和传统的数学相同。

```python
>>>max(7, 9.1)
9.1
```

调用表达式是拥有**子表达式**的，如：上述函数中的 `max` 函数，以及其中的参数，都是子表达式：

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220901124143848.png" alt="image-20220901124143848" style="zoom:80%;" />

其中，运算符指定一个函数（此处为 `max` ）。当这个调用表达式被用来运算时，我们说函数 `max`  是用参数 7.5 和 9.5 调用的，并返回一个值 9.5。此外，**调用表达式**中参数的顺序也很重要，如：

```python
>>>pow(100, 2)
10000
>>>pow(2, 100)
1267650600228229401496703205376
```

**函数符号的三个优点**

与中缀符号的数学符号相比，函数符号有三个主要优点：

- 首先，函数可以接收任何数量的参数，从而不会产生歧义（ambiguity），因为函数名总在参数之前。

```python
>>> max(1, -2, 3, -4)
1
```

- 其次，函数直接扩展为**嵌套表达式（Nested expression）**，而被嵌入使用的函数本身也是复合表达式，而在嵌套表达式中，嵌套的结构在括号中是完全明确的，这一点与复合中缀表达式不同。

  注意：原则上慎用多级嵌套，虽然对Python 解释器来说运算是没问题的，但是会降低代码可读性，给自己和阅读此代码的其他人造成困扰（Remain interpretable by yourself）。

```python
>>> max(min(1, -2), min(pow(3, 5), -4))
-2
```

- 第三点，数学表达式记号太多，有的也太难打了！但是这些问题都能被函数调用表达式（Call expression）统合，所以数学表达式造成的繁杂问题也就不存在了。在Python 中允许使用基本的数学中缀符号（如：+，-，*，/等等），**但是，任何数学运算符都能表达为一个带名字的函数（如`mul`代表乘法运算，`add` 代表加法运算）**。

#### Importing Library Functions（导入库函数）

Python 定义了非常多的函数，包括上一节中提到的运算符函数，但它们的所有名称并不是默认可用的。而是将这些作为函数库，需要使用的时候再导入。如`math` 函数库：

```python
>>> from math import sqrt
>>> sqrt(256)
16.0
```

如`operator`函数库：

```python
>>> from operator import add, sub, mul
>>> add(14, 28)	# +
42
>>> sub(100, mul(7, add(8, 4))) # -, *, +
16
```

导入语句指定模块名称（例如，`operator`或`math`），然后列出要导入的模块的命名属性（例如，`sqrt`）。导入函数后，就可以多次调用。

查询Python的官方说明文档：https://docs.python.org/zh-cn/3/library/index.html

#### Names and the Environment（命名和环境）

编程语言的一个关键，是它提供了使用名称来引用**计算对象**的方法。如果一个值被赋予了一个名字，我们说这个名字**绑定（bind）**到这个值上。

在 Python 中，我们可以使用**赋值语句**建立新的绑定，该语句包含 = **左侧的名称**和**右侧的值**：

```python
>>> radius = 10
>>> radius
10
>>> 2 * radius
20
```

也可以通过`import` 进行绑定：

```python
>>> from math import pi
>>> pi * 71 / 223
1.0002380197528042
```

`=` 在Python（和其他语言中） 中被称作赋值运算符。赋值是最简单的抽象方法，因为它允许我们使用名称就引用一个复合运算的结果。如上面计算的面积，用这种方式，通过逐步构建复杂度不断增加的计算对象，来构建复杂的程序。

将名称绑定到值，并稍后通过名称检索这些值的可能性，意味着解释器必须维护某种”记忆“（内存）来跟踪名称、值和绑定关系。这种”记忆“称为**环境（environment）**。

名称也可以绑定到函数。 例如，名称 `max` 绑定到我们一直使用的 `max` 函数。 与数字不同，函数难以呈现为文本（are tricky to render as text），因此当被要求描述函数时，Python 会打印一个识别描述信息：

```python
>>> max
<built-in function max>
```

也可以使用赋值语句给新名称绑定已有函数：

```python
>>> f = max
>>> f
<built-in function max>
>>> f(2, 3, 4)
4
```

并且连续的赋值语句可以将名称重新绑定到新值。

```python
>>> f = 2
>>> f
2
```

在Python 中，一般把名称叫做**变量名**，或者直接称作**变量**。因为在程序执行的过程中，它可以绑定不同的值。当一个名称和一个新值绑定之后，就不再和先前的值绑定了，甚至可以将内置名称绑定新值：

```python
# 再此之前，max 是一个求给定数中最大值的函数<built-in function max>
>>> max = 5
>>> max
5
```

在执行赋值语句的时候，在将绑定更改为左侧名称之前，Python 会将`=` 右侧先进行运算。因此在右侧的表达式中可以引用一个名称，即使它是赋值语句操作的名称也可以：

```python
>>> x = 2
>>> x = x + 1
>>> x
3
```

也可以在一个赋值语句中进行多个绑定操作，此时`=` 左侧的名称和右侧的表达式需要使用逗号隔开：

```python
>>> area, circumference = pi * radius * radius, 2 * pi * radius
>>> area
314.1592653589793
>>> circumference
62.83185307179586
```

和单个的赋值相同，对于多重赋值，`=` 右侧的所有表达式在绑定之前，都会先完成计算。作为此规则的一种结果，交换绑定到两个名称的值可以在单个语句中执行。（其它语言也有类似的语言特性，如JavaScript 中的析构赋值等。）

#### Evaluating Nested Expressions（嵌套表达式的求值）

本章的一个目标，就是将有关过程性求值的各种问题隔离出来。解释器本身是按照以下流程运行的：

1. 求出运算符和操作数的子表达式的值，然后
2. 将作为运算符子表达式的值的函数当作实际参数，这里的实际参数也就是也是其它子表达式（运算对象）的值。

即使是这条最简单的规则，也显示出计算过程中具有普遍性的重要问题：

首先，由上面的第一步可以看到，要完成一个调用表达式（Call expression）的求值，我们首先必须完成其它调用表达式的求值。

因此，求值操作本质上是**递归（recursively）**的，也就是说，它在执行计算这个操作中，需要调用规则本身。

例如进行如下的求值运算：

```python
>>> sub(pow(2, add(1, 10)), pow(2, 5))
2016
```

上述嵌套表达式的求值，需要使用四次求值运算规则。如果我们画出每次的求值，可以得到如下的层次结构图：

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220901161643088.png" alt="image-20220901161643088" style="zoom:67%;" />

这个结构图被称作**表达式树（Expression tree）**，在计算机科学中，树都是自顶向下生长的，树中每个点的对象称为**节点（node）**，在这种情况下，它们是带有相应值的表达式。

对根上的表达式（即顶部的完整表达式）进行求值，第一步，需要对其子表达式进行求值。

叶子表达式（即没有分支的节点），又称作终端节点，表示的是运算符或者数值。

内部节点有两部分：应用于求值过程的调用表达式，以及该表达式的结果。

从树的角度上去看求值过程，可以设想那些运算对象的值向上穿行，从终端节点开始，而后在越来愈高的层次中组合起来。（这种”值向上穿行“的求值形式，我们称之为：树形积累）

进一步的观察告诉我们，反复运用第一步，总可以把我们带到求值中的某一点，在这里遇到的不是调用表达式（嵌套表达式）而是基本表达式。如数值（如`2`）、名称（如`add`），处理这些基础情况的的方式如下规定：

- 数的值就是它们表现出的数值
- 名称计算得到的值，为当前环境中与该名称关联的值

请注意环境（environment）表达符号含义中的重要作用。在Python 中，如果我们不确定表达式具体运行的环境，如：`x` 的意义（甚至是`add` 的意义），单论表达式的值是没有意义的，如：

```python
>>> add(x, 1)
```

环境提供了进行求值运算生效的上下文，这在我们理解程序执行方面起着重要作用。

此求值过程不足以对所有Python 代码生效，而只是对调用表达式（call expression，又称嵌套表达式）、数值、名称生效。如：它不对赋值语句生效，如：

```python
>>> x = 3
```

不会返回一个值，也不会计算某实参传入的函数的值，因为该语句的作用是名称绑定到某值，而不是别的什么作用。**一般来说，语句只是执行，而不会进行运算。（In general, statements are not evaluated but executed）**；语句不会新产生值，而是让某些改变发生。每种类型的**表达式**或**语句**都有自己的**计算**或**执行**过程。

一个古板的注释：当我们说：”将一个数字计算为数值，“，意思是：Python 解释器将数字进行求值，计算结果为数值，赋予编程语言意义的正是解释器。鉴于解释器是一个始终行为如一的固定程序，我们可以说数字（和表达式）本身在 Python 程序的上下文中计算为数值。

####  The Non-Pure Print Function（非纯的Print 函数）

通过如下的文字描述，我们可区分两种函数类型。

**纯函数（Pure functions）**，即传入一些参数（实参）然后返回结果（输入的一些参数被传入函数中，运算得到的结果）的函数。如内置函数：

```python
>>> abs(-2):
2
```

可以描述为一个接受输入，然后产生输出的小型机器。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220901183033378.png" alt="image-20220901183033378" style="zoom:80%;" />

函数`abs` 是纯函数。纯函数只是返回一个运算结果，除此之外不会有任何其他的作用。此外，当使用相同的参数多次调用时候，纯函数必须返回相同的值（即不会对原传入参数值进行更改。）

**非纯函数（Non-pure functions）** ，除了返回值之外，应用非纯函数还会产生**副作用**，副作用会改变当前解释器或者计算机的状态。常见的副作用是除了返回值之外，生成了额外的输出，如使用`print` 函数：

```python
>>> print(1, 2, 3)
1 2 3
```

在这些示例中，虽然`print` 和`abs` 函数看起来相似，但是它们根本的运作方式就是不同的。`print` 的返回值通常是`None`，在Python 中表示”什么也没有“的特殊值。交互式 Python 解释器不会自动打印值 `None` 值。这里，函数本身正在打印出的结果，就是被调用的副作用。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220901185201986.png" alt="image-20220901185201986" style="zoom:80%;" />

如下为调用 `print` 的嵌套表达式，注明了函数的非纯字符。

```python
>>> print(print(1), print(2))
1
2
None None
```

如果觉得上面的输出结果不可思议，我建议你画一画表达式树（Expression Tree），就能搞清楚为什么表达式运算输出结果这么奇怪了。

要留意`print` 函数，因为它会返回 `None`，这意味着它不能用在赋值语句中用作表达式。

```python
>>> two = print(2)
2
>>> print(two)
None
```

纯函数是受到严格约束的，它并不会随着时间产生副作用或者改变行为。施加这些约束会产生巨大的益处。

**首先**，首先，纯函数可以更可靠地组合成复合调用表达式（嵌套表达式）。我们可以在上面的非纯函数示例中看到 ，操作数表达式在使用`print`时不会返回可用的结果。

**第二点**，纯函数更易于测试。传入的许多参数都会各自产生相同的返回值，这些返回值可以被用来与期待得到的返回值进行比较。关于测试的更多内容将在本章的后续部分进行探讨。

**第三点**，第 4 章会说明，当进行并发编程的时候，纯函数是至关重要的，并发程序可以同时计算多个调用表达式。

相比之下，第 2 章研究了一系列非纯函数，并描述了它们的用途。

基于上述原因，在本章的其余部分，我们将重点关注创建和使用纯函数。`print` 函数仅用于我们可以看到计算的中间结果。

### Defining New Functions（定义新函数）

我们已经在 Python 中明确了一些必然出现在所有强大的编程语言中的要素：

1. 数值是原始的内置数据，和算术运算是函数。

2. 复合函数提供了一种组合操作的方法。

3. 将名称绑带到值，提供了一种有限的抽象方法。

现在我们将学习**函数定义**，这是一种更加强大的抽象技术。通过它可以将名称绑定到复合操作（compond operation）上，然后可以将复合操作称为一个单元进行引用。

我们从学习如何表达”平方“的概念开始。我们可能会说，“要进行一个数的平方运算，就要让它自己和自己相乘。”这在Python中表示为：

```python
>>> def square(x):
        return mul(x, x)
```

由此定义了一个新函数，名字为`square`。这个用户定义的函数不是内建于编译器中的。它表示一个数与自己相乘的复合运算。函数定义中的 `x`  被称作**形式参数（Formal parameter）**，它为要乘的东西提供了一个名称。定义创建这个用户定义函数，并将此函数与名称 `square` 关联了起来。

**如何定义一个函数**。函数定义包含了一个`def` 语句，这个语句中由一个`<name>` 和一个逗号分隔的形式参数列表组成`<formal parameters>`，后面还有一个 `return` 语句，被称作函数体，它由一个return 表达式组成 `<return expression>` ，每调用此函数时候，函数体都会被执行。

```python
def <name>(<formal parameters>):
    return <return expression>
```

第二行的缩进是必须的，大多数程序员使用四个空格作为缩进。return 表达式不会立即计算；它被存储为新定义的函数的一部分，并且仅在最终应用该函数时才进行计算。

已经完成了`square` 函数的定义，我们可以使用调用表达式（Call expression）来使用它：

```python
>>> square(21)
441
>>> square(add(2, 5))
49
>>> square(square(3))
81
```

我们也可以使用 `square` 作为其它函数的基本结构（参数）。例如，我们可以定义一个函数 `sum_square` ，给定任意两个数值作为该函数的实际参数，然后返回这两个数的平方和：

```python
>>> def sum_squares(x, y):
        return add(square(x), square(y))
>>> sum_squares(3, 4)
25
```

用户定义函数和内置函数的使用方法一样。事实上，从 `sum_squares` 的定义中，我们无法判断 `square` 是内置在解释器中、从模块中导入还是由用户定义的。

`def` 语句和赋值语句都可以将名称绑定给值，并且会导致已有的绑定丢失。例如：如下的 `g` 引用了一个没有实参的函数，然后 `g` 又被绑定给了一个值，最后又被绑定给了一个需要两个实参的（与第一次）不同的函数。

```python
>>> def g():
        return 1
>>> g()
1
>>> g = 2
>>> g
2
>>> def g(h, i):
        return h + i
>>> g(1, 2)
3
```

#### Environments（环境）

我们的 Python 子集现在已经足够复杂了，以至于程序的含义还不是很不明显。如果形参与内置函数同名会怎么样？两个函数可以共用名称而不造成混乱吗？为了解答这些问题，我们必须更加细致地描述环境（environments）。

表达式运算求值所在的环境由一系列**帧（frame）**构成，它可以被描述为一些”盒子“。每一帧里面包含了一些**绑定（binding）**，每个绑定中都是名称与其对应的值。只有一个**全局（global）**帧（下面的地球符号代表全局帧），（它包含了所有内建函数的名称和绑定，）赋值和导入语句会将信息添加到当前环境的第一帧。到目前为止，我们的环境只包含全局帧。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB.gif" alt="动画" style="zoom:80%;" />

此**环境关系图（environment diagram）**显示了当前环境的绑定信息，以及名称绑定到的值。演示使用的是名为Online Python Tutor 的工具，地址：[Online Python Tutor](https://pythontutor.com/composingprograms.html#mode=edit)

函数也会在环境关系图中展示出来。 `import` 语句将名称绑定到内置函数。`def` 语句将名称绑定到由定义创建的用户定义函数。导入 `mul` 并定义 `square` 后生成的环境如下所示：

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB1.gif" alt="动画1" style="zoom:80%;" />

每一行的函数都是以 `func` 开头，后面接函数名称和形式参数（形参）。如 `mul` 这样的内置函数没有形参，而是使用 `...` 作为替代。要注意函数名称是重复的，一个是在帧中，另一个是函数自身的一部分。（这种重复是有意为之，因为许多不同的名字可能会引用相同的函数。）出现在函数中的名称称为**固定名称（Intrinsic name）**。帧中的名称是**绑定名称（Bound name）**。这两者是不同的：因为许多不同的名字可能会引用相同的函数，但是该函数本身只有一个固定名称。

帧中绑定到函数的名称是在求值期间使用的名称。而函数的固定名称在求值期间并没发挥作用。点击下面示例的”Forward“ 按钮，可以看到，一旦名称 `max` 绑定了数值 `3`，那么它就再也不能被当作先前的函数使用了。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB2.gif" alt="动画2" style="zoom:80%;" />



错误信息`TypeError: 'int' object is not callable` 表示名称 `max` （当前绑定到了数值3）是一个整数，而非函数。因此，它不能用作调用表达式（call expression）中的运算符。

**函数的签名**。函数的不同之处在于它们允许使用的参数数量。为了跟踪这些需求，我们以显示函数名及其形参的方式绘制每个函数。用户自定义函数 `square` 只取 `x`；提供更多或更少的参数将导致错误。对函数形式参数的描述称为函数的签名。

函数 `max` 可以使用任意数量的参数。（在帧中）显示为`max(...)`。但是无论使用多少参数，所有内置函数都显示为 `<name>(...)` ，因为这些原始函数并没有被显式定义过。

#### Calling User-Defined Functions（调用用户定义的函数）

要对运算符为用户定义函数的调用表达式求值，Python解释器（计算时）会遵循一个计算过程。与任何调用表达式一样，解释器计算运算符和操作数表达式，然后将命名了的函数应用于结果参数。

应用用户定义函数会引入第二个**局部帧（Local frame）**，该帧只能被该函数访问。将用户定义的函数应用于一些参数:

1. 在一个新的局部帧中，将参数绑定到函数的形参名称上。
2. 在当前帧的开头以及全局帧的末尾求出函数体。

函数体求值所在的环境由两个帧组成：第一个是局部帧，包含形参绑定（信息），之后是全局帧，包含其它所有东西。每个函数实体都有自己的独立局部帧。

为了详细说明示例，下面描述了相同示例的环境关系图的几个步骤。

在执行第一个导入语句之后，只有名称 `mul` 被绑定到全局帧中：

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB3.gif" alt="动画3" style="zoom:80%;" />

首先，执行函数 `square` 的定义语句。请注意，整个 `def` 语句是在一个步骤中处理的。在调用该函数之前（而不是在定义函数时），该函数的主体不会执行。

接下来，使用参数 -2 调用 `square` 函数，因此使用绑定到值 -2 的形参 `x` 创建一个新帧。

然后，在当前环境中查找名称 `x`，该环境由所示的两个帧组成。在这两种情况下，`x` 的计算结果都是 -2，因此 `square` 函数返回值为 4。

`square` 帧中的”return value“不是名称绑定；相反，它表明了是创建帧的函数调用了返回的值。

即便在这个简单的示例中，也使用了两种不同的环境。顶级表达式 `square(-2)` 在全局环境中求值，而返回表达式 `mul(x, x)` 在调用 `square` 时创建的环境中求值。`x` 和 `mul` 都绑定在这个环境中，但是在不同的帧里面。

在环境中，帧的顺序会影响通过在表达式中查找名称返回的值。我们之前说过，在当前环境中，名称被处理为与该名称关联的值。我们现在可以描述的更精确一点：

- **名称求值**（Name evaluation）为当前环境中，最先找到该名称的帧中，所绑定到该名称的值。

我们的对环境的概念性的框架，名称和函数，共同构建了一个**求值模型**（model of evaluation）；对于这个模型，尽管其中一些机制还尚不明确（如：绑定是怎么具体实施的），但我们的模型确实精准又正确地描述了解释器是怎么求对调用表达式（Call Expression）进行求值的。在第三章，我们会知晓这个模型是怎样作为实现编程语言解释器蓝图的。

####  Example: Calling a User-Defined Function（示例：调用用户定义的函数）

让我们再次思考两个简单的函数定义，并说明为用户定义的函数计算调用表达式的过程。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB4.gif" alt="动画4" style="zoom:80%;" />



首先，Python 对名称`sum_squares` 进行求值，此函数绑定了全局帧的一个用户定义函数。原始数值表达式5和12的值与它们所表示的数字相同。

然后，Python应用 `sum_squares` ，它引入了一个局部帧，将 `x` 绑定到5，将 `y` 绑定到了12。

`sum_squares` 的函数体包含了如下的调用表达式：

```text
  add     (  square(x)  ,  square(y)  )
________     _________     _________
operator     operand 0     operand 1
```

这三个子表达式都在当前的环境中进行求值，该环境从标记为 `sum_squares` 的帧开始。运算符子表达式 `add` 是在全局帧中找到的名字。在进行加法运算之前，必须依次计算两个操作数子表达式。两个操作数都在当前环境中从标有 `sum_squares` 的帧开始计算。

在 `Operand 0` 中，`square` 在全局帧中命名了一个用户自定义函数，同时 `x` 在本地帧中命名了数字5，Python 通过引入另一个局部帧将 `square` 运用给了5，该局部帧中x绑定到了5。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220903200924792.png" alt="image-20220903200924792" style="zoom:80%;" />

使用该环境，表达式`mul(5, 5)` 的求值结果就是25。

然后我们的求值过程轮到了`Operand 1`，这里面 `y` 的值是12。Python 又一次对 `square` 的函数体进行了求值，这一次引入了另一个局部帧，将 `y` 绑定为12，因此`Operand 1` 求值结果为144.

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220903201456756.png" alt="image-20220903201456756" style="zoom:80%;" />

最后，对实参25和144调用加法，得到了`sum_squares` 的最终返回值：169。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220903201734895.png" alt="image-20220903201734895" style="zoom:80%;" />

这个例子展示了迄今为止我们发展出来的基本思想。名称是绑定到值的，这些值分布在许多独立的局部帧中，以及包含在共享名称的单个全局帧中。每调用一次函数，就会创建一个新的局部帧，即使是同一个函数被调用两次亦是如此。

这些机制的存在都是为了确保在程序执行期间，名称在正确的时间解析为正确的值。这个例子说明了为什么我们的模型需要这样的的复杂性。三个局部帧中都包含一个名称 `x` 的绑定，但是这个名称在不同的帧中被绑定到不同的值。而局部帧分离了这些名称。

#### Local Names（局部名称）

函数实现的细节之一是：实现者为函数的形式参数选择名称，不应该影响函数的行为。因此，如下的（两个）函数的行为相同：

```python
>>> def square(x):
        return mul(x, x)
>>> def square(y):
        return mul(y, y)
```

这个原则 -- 也就是函数应不依赖于编写者选择的参数名称 -- 对编程语言来说具有重要的结果。最简单的结果就是函数参数名称应保留在函数体的局部范围中。

如果参数不位于相应函数的局部范围中， `square` 的参数`x`可能和`sum_squares`中的参数 `x` 产生混乱。严格来说，这并不是问题所在：不同局部帧中的`x`的绑定是不相关的。我们的计算模型具有严谨的设计来确保这种独立性。

我们认为局部名称的作用域被限制在定义它的用户定义函数的函数体中。当一个名称不再被访问，它就离开了作用域。此作用域的行为并不是我们模型的什么新东西；它是环境作用所带来的结果。

#### Choosing Names（选择名称）

名称的可互换性并不意味着形式参数名称就根本不重要了。与之相反，精心选择的函数和参数名称对可读性来说至关重要！

如下的规范节选自[Style Guide for Python Code](http://www.python.org/dev/peps/pep-0008)，此规范可作为所有Python （非离经叛道的）程序员的参考指南。一些共享的约定使社区开发成员之间的交流变得更容易。作为遵循本约定的“副作用”，你会发现你的代码在内部会变得更加一致。

1. 函数名称应该小写，以下划线分隔。提倡使用描述性的名称。
2. 函数名称通常反映解释器向参数应用的操作（例如`print`、`add`、`square`），或者结果（例如`max`、`abs`、`sum`）。
3. 参数名称应小写，以下划线分隔。提倡单个词的名称。
4. 参数名称应该反映参数在函数中的作用，并不仅仅是满足的值的类型。
5. 当作用非常明确时，单个字母的参数名称可以接受，但是永远不要使用`l`（小写的`L`）和`O`（大写的`o`），或者`I`（大写的`i`）来避免和数字混淆。

这些指南有很多例外，即使在 Python 标准库中也是如此。与英语语言的词汇一样，Python 继承了各种贡献者的词汇，结果并不总是一致的。

#### Function as Abstractions（函数作为抽象）

虽然 `sum_squares` 非常简单，但它展示了用户定义函数最强大的特性。函数 `sum_squares` 根据函数 `square` 定义，但仅依赖于 `square` 在其输入参数和输出值之间定义的关系。我们可以不考虑如何求一个数的平方而直接写出 `sum_squares` 。如何计算平方的细节可以隐藏，稍后再考虑。实际上，就`sum_squares` 而言， `square` 并不是一个特定的函数体，而是一个**函数的抽象**，一个所谓的函数抽象。在这个抽象层次上，任何能计算平方的函数都是等价的。

因此，只考虑返回值的话，如下的的两个求平方的函数是没有什么区别的。每个都是接受一个数字参数，并产生该数字的平方作为（返回）值。

```python
>>> def square(x):
        return mul(x, x)
>>> def square(x):
        return mul(x, x-1) + x
```

换句话说，函数定义应该能够隐藏细节。函数的用户可能不是自己编写的函数，而是从另一个程序员那里获得的“黑盒”。程序员不需要知道函数是如何实现的才能使用它。Python库有这个特性。许多开发人员使用这里定义的函数，但很少有人查看它们具体的实现。（事实上，许多 Python 库的实现并不完全用 Python 编写，而是 C++ 等语言。）

**函数抽象的各个方面**。要掌握函数抽象的使用，考虑它的三个核心属性通常是有用的。函数的**域（domain）**是函数能够取到的参数的集合。函数的 **范围（range）** 是函数能够返回值的集合。函数的 **意图（intent）** 是函数进行求值时候的输入与输出（包括输出可能产生的副作用）之间的关系。通过函数的域、范围、意图来理解函数抽象，对在复杂的程序中正确使用它们至关重要。

例如，我们用来实现 `sum_squares` 的任何 `square` 函数都应该具有以下属性：

- 域（domain）是任意实数。
- 范围（range）是非负实数。
- 意图（intent）为输出参数是输入参数的平方。

> 翻译注：上面的domain，range，intent 这三个词，我没有找到很好的与之对应的中文词汇，求赐教！

#### Operators（运算符）

数学运算符(如+和-)提供了组合方法的第一个例子，但我们还没有为包含这些运算符的表达式定义求值过程。

带有中缀运算符的Python表达式每个都有自己的求值过程，但您通常可以将它们视为调用表达式的简写。当你看到

```python
>>> 2 + 3
5
```

的时候，可以简单认为它是

```python
>>> add(2, 3)
5
```

的快捷方式。

中缀表示法可以嵌套，就像调用表达式一样。 Python 应用了运算符优先级的正常数学规则，它规定了如何解释具有多个运算符的复合表达式。

```python
>>> 2 + 3 * 4 + 5
19
```

运算结果与如下相同

```python
>>> add(add(2, mul(3, 4)), 5)
19
```

**调用表达式（Call Expression）**中的嵌套比运算符版本更明确，但也更难阅读。 Python 还允许使用圆括号对子表达式进行分组，以覆盖正常的优先规则，或者使表达式的嵌套结构更加明确。

```python
>>> (2 + 3) * (4 + 5)
45
```

运算结果与如下相同

```python
>>> mul(add(2, 3), add(4, 5))
45
```

在除法方面，Python 提供了两个中缀运算符：/ 和 //。前者是正常除法，即使除数（Divisor）将被除数（Dividend）均分，它也会产生一个**浮点数**或十进制值。

```python
>>> 5 / 4
1.25
>>> 8 / 4
2.0
```

另一方面， // 运算符将结果向下舍入为整数：

```python
>>> 5 // 4
1
>>> -5 // 4
-2
```

这两个运算符是 `truediv` 和 `floordiv` 函数的简写。

```python
>>> from operator import truediv, floordiv
>>> truediv(5, 4)
1.25
>>> floordiv(5, 4)
1
```

你在自己的程序中可以随意使用中缀运算符和圆括号，在简单的数学表达式中，Python 惯用（数学）运算符而不是调用表达式。

### Designing Functions（设计函数）

函数是所有程序(无论大小)的基本组成部分，它是我们用编程语言表达计算过程的主要媒介。到目前为止，我们已经讨论了函数的形式性质以及如何应用它们。现在我们转向如何创建一个好的函数的话题。从根本上说，好的函数的特性都强调了函数是抽象的概念。

- 每个函数都应该只有一个功能，该功能可以 用一个简短的名称来定义，使用一行文本来进行标识。顺序执行多个任务的函数应该被拆分在多个函数中。
- 不要重复劳动（Don't repeat yourself，DRY）是软件工程的中心法则。所谓的DRY原则规定多个代码段不应该描述重复的逻辑。反之，逻辑应该只实现一次，指定一个名称，并且多次使用。如果你发现自己在复制粘贴一段代码，你可能发现了一个使用函数抽象的机会。
- 函数应该定义得通用一些，准确来说，平方并不是在 Python 库中，因为它是`pow`函数的一个特例，这个函数计算任何数的任何次方。

这些指导方针提高了代码的可读性，减少了错误的数量，并且通常会使编写的代码总量最小化。将复杂的任务分解成简明的函数是一项需要经验才能掌握的技能。幸运的是，Python提供了一些特性来支持你的工作。

#### Documentation（文档字符串）

函数定义通常包括描述函数的文档，称为**文档字符串（docstring）**，它必须与函数体一起缩进。文档字符串通常用三引号括起来。第一行用一行描述了函数的工作。后面的行可以描述参数并阐明函数的行为:

```python
>>> def pressure(v, t, n):
        """Compute the pressure in pascals of an ideal gas.

        Applies the ideal gas law: http://en.wikipedia.org/wiki/Ideal_gas_law

        v -- volume of gas, in cubic meters
        t -- absolute temperature in degrees kelvin
        n -- particles of gas
        """
        k = 1.38e-23  # Boltzmann's constant
        return n * k * t / v
```

当您使用函数名称作为参数调用帮助时，您会看到它的文档字符串（键入 `q` 退出 Python 帮助）。

```python
>>> help(pressure)
```

在编写Python程序时，除了最简单的函数外，所有函数都要包含文档字符串。记住，代码只写一次，但经常要读很多次。Python文档包括[Docstring Guideline](http://www.python.org/dev/peps/pep-0257/)，用于维护不同Python项目之间的一致性。

**注释。**Python 中的注释可以附加到 `#` 符号之后的行尾。 例如，上面的注释 `Boltzmann's constant` 描述了 `k` 。 这些注释不会出现在 Python 的 `help` 中，解释器会忽略它们。 注释是因人类而存在的。

#### Default Argument Values（默认参数值）

定义通用函数的一个结果是引入了额外的参数（The introduction of additional aeguments）。具有许多参数的函数可能难以调用且难以阅读。

在 Python 中，我们可以为函数的参数提供默认值。调用该函数时，具有默认值的参数是可选的。如果未提供它们，则默认值将绑定到形式参数名称。例如，如果应用程序通常计算一摩尔粒子的压力，则可以提供此值作为默认值：

```python
>>> def pressure(v, t, n=6.022e23):
        """Compute the pressure in pascals of an ideal gas.

        v -- volume of gas, in cubic meters
        t -- absolute temperature in degrees kelvin
        n -- particles of gas (default: one mole)
        """
        k = 1.38e-23  # Boltzmann's constant
        return n * k * t / v
```

`=` 符号在此示例中表示两种不同的含义，具体取决于使用它的上下文。 在 `def` 语句头中，`=` 不执行赋值，而是指示调用 `pressure` 函数时使用的默认值。 相比之下，函数体中对 `k` 的赋值语句将名称 `k` 绑定到玻尔兹曼常数的近似值。

```python
>>> pressure(1, 273.15)
2269.974834
>>> pressure(1, 273.15, 3 * 6.022e23)
6809.924502
```

压力函数被定义为接受三个参数，但在上面的第一个调用表达式中只提供了两个。在这种情况下，`n` 的值取自 `def` 语句默认值。如果提供了第三个参数，则忽略默认值。

作为一条准则，函数体中使用的大多数数据值应该表示为命名参数的默认值，以便于检查它们，并可以由函数调用者更改。一些不变的值，如基本常数 `k` ，可以在函数体或全局帧中绑定。

## Lecture 3 : Control

### Control（控制）

我们现在可以定义的函数的表达能力是非常有限的，因为我们还没有引入一种方法来进行比较，并根据比较的结果执行不同的操作。控制语句（Control Statement）将赋予我们这种能力。它们是基于逻辑比较结果控制程序执行流的语句。

语句从根本上不同于我们迄今为止研究过的表达式。 它们没有值。 执行控制语句不是进行某些计算，而是决定解释器下一步应该做什么。

#### Statements（语句）

到目前为止，我们主要考虑了如何对表达式求值。但是，我们已经看到了三种语句:赋值语句、`def` 语句和 `return` 语句。这些Python代码行本身并不是表达式，尽管它们都包含表达式作为组件。

**语句不会被求值，而会被执行**。每条语句都描述了解释器状态的一些更改，执行一条语句将应用这些更改。正如我们看到的 `return` 语句和赋值语句，执行语句可能涉及对其中包含的子表达式求值。

**表达式（Expressions）**也可以作为语句执行，在这种情况下，它们会被求值，但它们的值会被丢弃。执行纯函数没有副作用，但执行非纯函数会产生效果作为函数调用的结果。

思考这个例子，

```python
>>> def square(x):
        mul(x, x) # Watch out! This call doesn't return a value.
```

此示例是有效的 Python 语句，但可能不会太符合预期的效果。 函数体由一个表达式组成。 表达式本身是有效的语句，但语句的效果是调用了 `mul` 函数，结果却被丢弃了。 如果你想对表达式的结果做一些操作，需要这样做：使用赋值语句来储存它，或者使用`return`语句将它返回：

```python
>>> def square(x):
        return mul(x, x)
```

有时，当调用像 `print` 这样的非纯函数时，拥有一个函数体是表达式的函数确实有意义。

```python
>>> def print_square(x):
        print(square(x))
```

在最高层级上，Python解释器的工作是执行由语句组成的程序。然而，许多有趣的计算工作来自对表达式进行求值。语句（Statement）控制了程序中不同表达式（expression）之间的关系以及对表达式执行结果的处理。

#### Compound Statements（复合语句）

一般来说，Python代码是一个语句序列。**简单语句（Simple statement）**是一行不以冒号结尾的语句。复合语句之所以被称为复合语句，是因为它由其他语句（简单语句和复合语句）组成。复合语句通常跨越多行，以一行开头，以冒号结束，它标识着语句的类型。同时，一个头部和一组缩进的语句一起被称为**子句（Clause）**。复合语句由一个或多个子句组成：

```python
<header>:
    <statement>
    <statement>
    ...
<separating header>:
    <statement>
    <statement>
    ...
...
```

我们可以这样理解我们已经见到的语句：

- 表达式、返回语句和赋值语句都是简单语句。
- `def`语句是复合语句。`def` **头部（header）**之后的**语句组（suite）**定义了函数体。

每种头部的专门化求值规则规定了何时以及是否执行其组中的语句。我们说是**头部（header）控制着它的语句组**。例如，在 `def` 语句的情况下，我们看到返回表达式并不是立即求值，而是存储起来以供以后使用，以便在最终在被调用的时候求值。

现在我们也能够理解多行的程序了。

- 要执行一系列语句，请先执行第一条语句。如果该语句没有重定向控制，则继续执行语句序列的其余部分（如果还有的话）。

这个定义揭示了递归定义**序列（sequence）**的基本结构**：一个序列可以拆分为它的第一个元素和其余元素。语句序列的“剩余”部分也是一个语句序列。（a sequence can be decomposed into its first element and the rest of its elements. The "rest" of a sequence of statements is itself a sequence of statements! ）**所以我们可以递归应用这个执行规则。这种”序列作为递归数据结构“的观念，会在后面的章节中再次出现。

该规则的重要结果是，语句按顺序执行，但由于重定向控制，可能永远无法到达后面的语句。

**实践指南：**在缩进语句组时，所有行必须以相同数量以及相同方式缩进（使用空格，而非Tab）。任何缩进的变动都会导致错误。

#### Defining Functions II: Local Assignment（定义函数II：局部赋值）

最初，我们声明用户定义函数的函数体仅由一个 `return` 语句和一个返回表达式组成。事实上，函数可以定义一个不止单个表达式的操作序列。

无论用户定义的函数何时被调用，定义中的子句序列在局部环境内执行。`return`语句会重定向控制，每当执行第一个 `return` 语句时，函数应用程序的进程就会终止，`return`表达式的值就是所应用函数的返回值。

赋值语句可以出现在函数体中。例如，这个函数返回两个数量之间的绝对差值，作为第一个数量的百分比，使用两步计算:

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB5.gif" alt="动画5" style="zoom:80%;" />

赋值语句的作用是将名称绑定到当前环境**第一帧**中的值。因此，函数体中的赋值语句不会影响全局帧。函数只能操作其局部环境这一事实对于创建模块化程序至关重要，在模块化程序中，纯函数只能通过它们的取值和返回值进行交互。

当然，`percent_difference` 函数可以写成单个表达式，如下所示，但返回表达式就会更复杂一些。

```python
>>> def percent_difference(x, y):
        return 100 * abs(x-y) / x
>>> percent_difference(40, 50)
25.0
```

到目前为止，局部赋值还没有增加函数定义的表达能力。当与其他控制语句结合使用时，它才能做到增加函数定义的表达能力。此外，**局部赋值（Local assignment）**这一操作，通过给中间量分配名称，在阐明复杂表达式的含义方面也起到了关键作用。

#### Conditional Statements（条件语句）

Python 有一个计算绝对值的内置函数。

```python
abs(-2)
2
```

我们可以自己实现这个函数，但是我们暂时没有方法定义一个具有比较和选择（功能）的函数。我们想表达如果 `x` 是正数，`abs(x)` 返回 `x`。此外，如果 `x` 为 `0`，`abs(x)` 返回 `0`。否则，`abs(x)` 返回 `-x`。在 Python 中，我们可以用条件语句来表达这种选择。

```python
def absolute_value(x):
  	"""Compute abs(x)"""
    if x > 0:
      	return x
    if x == 0:
      	return 0
    if x < 0:
      	return -x
result = absolute_value(-2)
```

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB6.gif" alt="动画6" style="zoom:80%;" />

`absolute_value` 函数的实现引发了几个重要的问题：

**条件语句（Conditional Statements）**。Python 中的条件语句由一系列的头部（headers）和语句组（suits）构成：一个必需的 `if` 子句，一个可选的 `elif` 子句序列，最后是一个可选的 `else` 子句：

```python
if <expression>:
    <suite>
elif <expression>:
    <suite>
else:
    <suite>
```

当执行一个条件语句时候，会按顺序处理每个子句，处理过程如下

1. 对头部进行求值。
2. 如果头部的值为真，就执行语句组。否则，就跳过条件语句中的所有后续子句。

如果到达 `else` 子句（仅当所有 `if` 和 `elif` 表达式计算为假值时才会发生），则执行其语句组。

**布尔上下文**。上面的执行过程提到了“一个假值”和“一个真值”。条件块头部语句中的表达式被称为布尔上下文：它们的真值对流程控制很重要，但在其他情况下它们的值不会被赋值或返回。

Python 有好几个假值，包括`0，None` 和布尔值`False`。除此之外，其他所有值都为真值。在第二章，我们会知道每种内置数据类型都有真值和假值。

**布尔值**。Python 有两个布尔值，称为 `True` 和`False`。布尔值表示逻辑表达式中的真值。内置比较操作 `>、<、>=、<=、==、!=` 返回这些值。

```python
>>> 4 < 2
False
>>> 5 >= 5
True
```

第二个例子读作“5 大于或等于 5”，与 `operator` 模块中的函数 `ge` 对应。

```python
>>> 0 == -0
True
```

最后一个示例读取“0 等于 -0”，对应于 `operator` 模块中的 `eq` 函数。请注意，Python 将赋值 (=) 与相等比较 (==) 区分开来，这是许多编程语言共享的约定。

**布尔运算符**。Python 中还内置了三个基本的逻辑运算符：

```python
>>> True and False
False
>>> True or False
True
>>> not False
True
```

逻辑表达式有相应的求值过程。这些过程利用了这样一个事实，即逻辑表达式的真值有时可以在不计算其所有子表达式的情况下确定，这种特性称为**短路（short-circuiting）**。

要求值 `<left> and <right>`：

1. 对子表达式 `<left>` 求值。
2. 如果结果是假值 `v`，则（整个）表达式的计算结果为 `v`。
3. 否则，表达式的计算结果为子表达式 `<right>` 的值。

要求值`<left> or <right>`：

1. 对子表达式 `<left>` 求值。
2. 如果结果是真值 `v`，则（整个）表达式的计算结果为 `v`。
3. 否则，表达式的计算结果为子表达式 `<right>` 的值。

要求值 `not <exp>`：

1. 对`<exp>` 进行求值，如果结果为false，则（整个表达式）结果为 `True`，否则为 `False` 。

这些值、规则和运算符为我们提供了一种组合比较结果的方法。执行比较并返回布尔值的函数通常以 `is` 开头，后不跟下划线（如： `isfinite`, `isdigit`, `isinstance`, 等）。

#### Iteration（迭代）

除了选择执行哪些语句之外，还使用控制语句来表示重复。如果我们编写的每一行代码只执行一次，那么编程将是一项非常低效的工作。只有通过反复执行语句，我们才能释放计算机的全部潜力。我们已经看到了一种形式的重复：一个函数可以应用多次，尽管它只定义一次。迭代控制结构是多次执行相同语句的另一种机制。

思考下斐波那契数列，其中每个数都是前两个数的和：

```text
0, 1, 1, 2, 3, 5, 8, 13, 21, ...
```

每个值都是通过重复应用“前两个数之和”规则构造的。第一个和第二个固定为 0 和 1。例如，第八个斐波那契数是 13。

我们可以使用 `while` 语句来枚举第 `n` 个斐波那契数。因此，我们需要跟踪创建了多少个值（`k`），以及第 k 个值（`curr`）和它的上一个值（`pred`），像这样：

```python
def fib(n):
  	"""Compute the nth Fibonacci number, for n >= 2"""
    pred, curr = 0, 1		# Fibonacci numbers 1 and 2
    k = 2
    while k < n:
      	pred, curr = curr, pred + curr
        k = k + 1
    return k

result = fib(8)
```

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB7.gif" alt="动画7" style="zoom:80%;" />

请记住，逗号可以分隔赋值语句中的多个名称和值。该行：

```python
pred, curr = curr, pred + curr
```

具有将名称 `pred` 重新绑定到 `curr` 的值的效果，同时将名称 `curr` 重新绑定到 `pred + curr` 的值。 `=` 右侧的所有表达式都在重新绑定之前都要先进行求值。

事件的顺序——即在更新左边的任何绑定之前求出`=`右边的所有内容——对于该函数的正确性至关重要。

`while` 语句也是由一个头部和语句组构成：

```python
while <expression>:
  	<suite>
```

执行一个 `while` 语句：

1. 对头部的表达式求值。
2. 如果求值结果为真值，就执行语句组，然后返回到步骤1。

在第 2 步中，在头表达式再次计算之前会执行整个 `while` 子句。

为了防止 `while` 子句的语句组无限次执行，语句组应始终在每次传递中更改某些绑定。

不终止的 while 语句称为无限循环。按 `<Control>-C` 可以强制 Python 终止循环。

#### Testing（测试）

一个函数的测试（Testing）是验证该函数是否符合预期行为的一种行为。我们的函数现在已经足够复杂了，以至于需要测试我们的实现。

测试是一种系统地执行这种验证的机制。测试通常采用另一个函数的形式，其中包含对被测试函数的一个或多个示例调用。然后根据预期结果验证返回的值。不像大多数通用的函数，测试涉及到挑选特殊的参数值，并使用它来验证调用。测试也可作为文档：它们展示了如何调用函数，以及什么参数值是合理的。

**断言（Assertions）**。程序员使用 `assert` 语句来验证预期，如正在测试的函数的输出。 `assert` 语句在布尔上下文中只有一个表达式，后面是带引号的一行文本（单引号或双引号都行，但要保持一致），如果表达式计算为假值，就将显示该行文本。

```python
>>> assert fib(8) == 13, 'The 8th Fibonacci number should be 13'
```

当被断言的表达式计算为真值时，执行断言语句无效。当它是一个假值时，`assert` 会产生一个停止执行的错误。

```python
>>> def fib_test():
        assert fib(2) == 1, 'The 2nd Fibonacci number should be 1'
        assert fib(3) == 1, 'The 3rd Fibonacci number should be 1'
        assert fib(50) == 7778742049, 'Error at the 50th Fibonacci number'
```

当在文件中而不是直接在解释器中编写 Python 时，测试通常写在同一个文件或后缀为 `_test.py` 的相邻文件中。

**文档测试（Doctest）**。Python 提供了一种方便的方法，可以将简单的测试直接放在函数的文档字符串中。 文档字符串的第一行应该包含对函数的单行描述，然后是一个空行。 后面可能会详细描述参数和行为。 此外，文档字符串可能包含一个调用该函数的示例交互式会话：

```python
>>> def sum_naturals(n):
        """Return the sum of the first n natural numbers.

        >>> sum_naturals(10)
        55
        >>> sum_naturals(100)
        5050
        """
        total, k = 0, 1
        while k <= n:
            total, k = total + k, k + 1
        return total
```

之后，可以通过[doctest模块](http://docs.python.org/py3k/library/doctest.html)验证交互。`globals` 函数会返回全局环境的表示（representation），解释器需要用它来计算表达式。

```python
>>> from doctest import testmod
>>> testmod()
TestResults(failed=0, attempted=2)
```

仅仅为了验证一个函数的文档测试结果，我们使用一个名为 `run_docstring_examples` 的 `doctest` 函数。（不幸的是）这个函数调用起来有点复杂。

它的第一个参数是要测试的函数。第二个为 `globals()` 表达式的结果，这是一个返回全局环境的内置函数。第三个参数 `True` 表示我们想要“详细（verbose）”的输出结果：运行的所有测试的目录。

```python
>>> from doctest import run_docstring_examples
>>> run_docstring_examples(sum_naturals, globals(), True)
Finding tests in NoName
Trying:
    sum_naturals(10)
Expecting:
    55
ok
Trying:
    sum_naturals(100)
Expecting:
    5050
ok
```

当函数的返回值与预期结果不匹配时，`run_docstring_examples` 函数会将此问题报告为测试失败。

在文件中编写 Python 时，可以通过使用 `doctest` 命令行选项启动 Python 来运行文件中的所有 doctest：

```bash
python3 -m doctest <python_source_file>
```

有效测试的关键是在实现新功能后立即编写（和运行）测试。甚至在实现之前编写一些测试也是一个很好的实践，以便在脑海中有一些示例输入和输出。应用于单个函数的测试称为**单元测试（unit test）**。详尽的单元测试是优秀程序设计的一个标志。

## Lecture 4 : Higher-Order Functions & Lecture 5 : Environments & Lecture 9 : Function Examples

### Higher-Order Functions（高阶函数）

我们已知晓，函数是一种抽象方法，它进行了与参数特定值无关的复合操作。如在 `square` 中：

```python
>>> def square(x):
        return x * x
```

我们并不是在讨论特定参数的平方，而是在讨论一种得到任意数平方的方法。当然，我们也可以在不定义这个函数的情况下，通过编写如下的表达式来求平方：

```python
>>> 3 * 3
9
>>> 5 * 5
25
```

并且永远不会显式提及 `square`。这种做法足以满足简单的计算，例如计算平方等，但对于更复杂的示例（例如 `abs` 或 `fib`），这样做会变得很困难。通常，缺少函数定义会对我们非常不利，它会强迫我们始终工作在特定操作的层级上，这在语言中非常原始（这个例子中是乘法），而不是高级操作。我们应该从强大的编程语言索取的东西之一，是通过将名称赋为常用模式来构建抽象的能力，以及之后直接使用抽象的能力。函数提供了这种能力。

我们将会在下个例子中看到，代码中会反复出现一些常见的编程模式，但使用一些不同函数来实现。这些模式也可以被抽象和给予名称。

为了将某些通用模式表示为命名概念，我们需要构造可以接受其他函数作为参数，或返回函数作为值的函数。操作函数的函数称为**高阶函数（higher-order function）**。本节将展示高阶函数如何充当强大的抽象机制，从而极大地提高语言的表达能力。

#### Functions as Arguments（函数作为参数）

思考下面三个都是求和的函数。第一个是 `sum_naturals` ，作用为计算到 `n` 为止所有自然数的和：

```python
>>> def sum_naturals(n):
        total, k = 0, 1
        while k <= n:
            total, k = total + k, k + 1
        return total
      
>>> sum_naturals(100)
5050
```

第二个，`sum_cubes`，作用为计算到 `n` 为止，所有自然数的立方和：

```python
>>> def sum_cubes(n):
        total, k = 0, 1
        while k <= n:
            total, k = total + pow(k, 3), k + 1
        return total
      
>>> sum_cubes(100)
25502500
```

第三个，`pi_sum`，计算如下级数式子的和：
$$
\frac{8}{1·3}+\frac{8}{5·7}+\frac{8}{9·11}+...
$$

```python
>>> def pi_sum(n):
        total, k = 0, 1
        while k <= n:
            total, k = total + 8 / ((4*k-3) * (4*k-1)), k + 1
        return total
      
>>> pi_sum(100)
3.1365926848388144
```

这三个函数显然有一个共同的基本模式。它们大部分是相同的，不同的只有名称和用来相加的 `k`。我们可以通过填充同一模板中的插槽（slot）来生成每个函数：

```python
def <name>(n):
    total, k = 0, 1
    while k <= n:
        total, k = total + <term>(k), k + 1
    return total
```

这种通用模式（common pattern）的存在，表明了一种有用的抽象等待着我们把它“提取”出来。这三个函数的每一个都是求式子的和。作为程序的设计者，我们希望我们的语言足够强大，以便我们编写函数来表达求和的概念，而不仅仅是计算特定和的函数。我们可以在 Python 中使用上面所示的通用模板，并且把插槽变成形式参数来轻易完成它：

在下面的示例中，`summation` 函数将上限 `n` 和计算第 `k` 项的函数作为两个参数来使用（ `summation` takes as its two arguments the upper bound `n` together with the function `term` that computes the kth term）。我们就可以像使用别的函数一样来使用 `summation` 函数了。花一些时间来逐步查看这个示例，并且注意 `cube` 是怎样绑定到局部名称 `term` 的，以保证 `1*1*1 + 2*2*2 + 3*3*3 = 36` 的计算结果是正确的。在这个示例中，不需要的帧会被删除以节省空间。

```python
def summation(n, term):
  	total, k = 0, 1
    while k <= n:
      	total, k = total + term(k), k + 1
    return total
def cube(x):
  	return x*x*x
def sum_cubes(n):
  	return summation(n, cube)
  
result = cum_cubes(3)
```

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/%E5%8A%A8%E7%94%BB8.gif" alt="动画8" style="zoom:80%;" />

使用返回参数的 `identity` 函数，我们可以使用完全相同的 `summation` 函数进行自然数的求和。

```python
>>> def summation(n, term):
        total, k = 0, 1
        while k <= n:
            total, k = total + term(k), k + 1
        return total
>>> def identity(x):
        return x
>>> def sum_naturals(n):
        return summation(n, identity)
>>> sum_naturals(10)
55
```

`summation` 函数也可以直接被调用，而不用为特定序列定义另一个函数。

```python
>>> summation(10, square)
385
```

我们还可以使用 `summation`，通过定义一个名为 `pi_term` 的计算每一项的函数，最终来定义 `pi_sum` 这个函数。传入参数 `1e6`（1 * 10 ^6^ 的缩写），来得到圆周率的近似值。

```python
>>> def pi_term(x):
        return 8 / ((4*x-3) * (4*x-1))
>>> def pi_sum(n):
        return summation(n, pi_term)

>>> pi_sum(1e6)
3.141592153589902
```



####  Functions as General Methods（函数作为一般方法）

我们引入了用户定义函数作为数值运算的抽象模式，以使它们独立于特定数字。有了高阶函数，我们会见识到一种更强大的抽象：:一些函数表示通用的计算方法，独立于它们调用的特定函数。（ some functions express general methods of computation, independent of the particular functions they call.）

尽管对函数的含义进行了这种概念上的扩展，但我们关于如何计算调用表达式的环境模型可以优雅地扩展到高阶函数的情况，而无需更改。当将用户定义函数应用于某些实参时，形参被绑定到一个新的局部帧中这些实参的值（可能是函数）上。

思考下面的示例，它实现了迭代改进的一般方法，并且可以用于计算[黄金分割比例](http://www.geom.uiuc.edu/~demo5337/s97b/art.htm)。黄金比例，通常称为“phi”，是一个接近 1.6 的数字，经常出现在自然、艺术和建筑中。

**迭代改进算法（iterative improvement algorithm）** 是以一个方程的解的 `guess` （猜测值）开始的（An iterative improvement algorithm begins with a `guess` of a solution to an equation.）。它反复调用一个 `update` 函数来改进该猜测值，并应用一个  `close` 函数来比较，检查当前的 `guess` 值是否“足够接近”正确值。

```python
>>> def improve(update, close, guess=1):
        while not close(guess):
            guess = update(guess)
        return guess
```

这里的 `improve` 函数是一个重复求取精度的一般表达式。此函数没有指定要解决什么问题：这些细节留给作为参数传入的 `update` 和 `close` 函数去指定。

黄金分割比例的最注明的性质为，它可以通过重复的使用任何正数的倒数加1来计算，并且它本身比自己的平方小1。于是我们可以将此性质表述为如下的 `improve` 函数，用以改进。

```python
>>> def golden_update(guess):
        return 1/guess + 1
  
>>> def square_close_to_successor(guess):
        return approx_eq(guess * guess, guess + 1)
```

如上，我们引入了对 `approx_eq` 函数的调用，如果它的参数大致相等，那么就会返回 `True`。为了实现 `approx_eq` 函数，我们可以将两个数字之间的差的绝对值与一个小的容差值（tolerance value）进行比较。

```python
>>> def approx_eq(x, y, tolerance=1e-15):
        return abs(x - y) < tolerance
```

使用参数 `golden_update` 和 `square_close_to_successor` 调用 `improve` 函数会计算出黄金分割比例的有限近似值。

```python
>>> improve(golden_update, square_close_to_successor)
1.6180339887498951
```

通过跟踪求值的步骤，我们可以得出这个值是怎样被计算出来的。首先，绑定了（三个）变量 `update` ， `close` 和 `guess` 的函数构建了一个局部帧，在 `improve` 函数的函数体中，名称 `close` 绑定到了（传入参数）`square_to_successor`，在 `guess` 的初始值上被调用。跟踪其余步骤可以查看计算黄金分割比例的计算过程。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220906154201123.png" alt="image-20220906154201123" style="zoom:80%;" />

这个例子说明了计算机科学中的两个相关的大概念。第一，命名和函数可以允许我们抽象出大量的复杂度。虽然每个函数的定义都很简单，但由求值过程启动的计算过程却相当的复杂。第二，基于事实，我们拥有了非常通用的求值过程，小的组件可以组合成复杂的过程。知晓程序的解释过程使我们验证和检查我们创建（程序）的过程。

与往常一样，我们新的通用方法 `improve` 需要测试来检查其正确性。黄金分割比例可以提供这样的测试，因为它也有一个精确的**闭式解（closed-form solution）**，我们可以将其与这个迭代结果进行比较。

> 翻译注：什么是闭式解？
>
> 又称作解析解（Analytic expression），是可以用解析表达式来表达的解。 在数学上，如果一个方程或者方程组存在的某些解，是由有限次常见运算的组合给出的形式，则称该方程存在解析解。二次方程的根就是一个解析解的典型例子。在低年级数学的教学当中，解析解也被称为**公式解**。
>
> *节选自维基百科：https://zh.wikipedia.org/wiki/%E8%A7%A3%E6%9E%90%E8%A7%A3*

```python
>>> from math import sqrt
>>> phi = 1/2 + sqrt(5)/2
>>> def improve_test():
        approx_phi = improve(golden_update, square_close_to_successor)
        assert approx_eq(phi, approx_phi), 'phi differs from its approximation'
      
>>> improve_test()
```

对于这个测试，什么都没输出就是最好的消息：这意味着在 `assert` 语句成功执行后，`improve_test` 返回了`None` 。

#### Defining Functions III: Nested Definitions（定义函数III：嵌套定义）

上面的示例演示了将函数作为参数传递的能力如何显着增强了我们的编程语言的表达能力。每个通用的概念或方程都能映射为自己的小型函数，这一方式的一个负面效果是全局帧会被小型函数弄乱。另一个问题是我们限制于特定函数的签名：`improve` 函数的 `update` 参数必须且只能接受一个参数。嵌套函数（nested functions）的定义解决了这些问题，但是这需要我们增强我们的模型。

让我们思考一个新问题：计算一个数的平方根。在编程语言中，平方根一般简写为 `sqrt` 。重复应用以下函数进行（值的）更新，会收敛到 `a` 的平方根：

```python
>>> def average(x, y):
        return (x + y)/2
  
>>> def sqrt_update(x, a):
        return average(x, a/x)
```

这个带有两个参数的更新函数，与 `improve` 函数不兼容（它需要两个参数，而不是一个），且它只提供一个更新值，而我们真正关心的是通过重复更新来取平方根。这些问题的解决方案是把函数放到其他定义的函数体中。

```python
>>> def sqrt(a):
        def sqrt_update(x):
            return average(x, a/x)
        def sqrt_close(x):
            return approx_eq(x * x, a)
        return improve(sqrt_update, sqrt_close)
```

和局部赋值一样，局部 `def` 语句只影响当前的局部帧。这些函数只在求根时起作用。与我们的求值过程一致，在调用 `sqrt` 函数之前，这些局部的 `def` 语句都不会进行求值。

**词法作用域（Lexical scope）**。局部定义的函数还可以访问它们定义的作用域中的名称绑定。在这个示例中， `sqrt_update` 同样指向了 `a`，这里的名称 `a` 是包裹着 `sqrt_update` 函数的外层函数 `sqrt` 的形式参数。这种在嵌套函数中共享名称的规则叫做**词法作用域**。准确来说，内部的函数可以访问定义它们的环境中（而不是调用所在位置）的名称。

我们需要对环境模型进行两个扩展来启用词法作用域。

1. 每个用户定义的函数有一个父环境：也就是定义该函数的环境。
2. 当用户定义的函数被调用时，其局部帧就会拓展它的父环境。

在 `sqrt` 函数之前，所有函数都是在全局环境中定义的，因此它们都有相同的父环境：全局环境。相反，当Python计算 `sqrt` 的前两个子句（clauses）时，它会创建与本地环境相关联的函数。调用结果：

```python
>>> sqrt(256)
16.0
```

环境首先为 `sqrt` 添加了一个局部帧，并对`sqrt_update` 和 `sqrt_close` 的 `def` 语句进行了运算。

```python
def average(x, y):
    return (x + y)/2

def improve(update, close, guess=1):
    while not close(guess):
        guess = update(guess)
    return guess

def approx_eq(x, y, tolerance=1e-3):
    return abs(x - y) < tolerance

def sqrt(a):
    def sqrt_update(x):
        return average(x, a/x)
    def sqrt_close(x):
        return approx_eq(x * x, a)
    return improve(sqrt_update, sqrt_close)

result = sqrt(256)
```

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220906202417258.png" alt="image-20220906202417258" style="zoom:80%;" />

每个函数值都有一个新的父级环境（即类似于 `[parent=Global]` 标注）的注释，我们将从现在开始将其添加到环境关系图中。函数值的**父函数（parent）**是定义该函数的环境的第一帧。在全局环境中定义的函数没有父环境注释。当调用用户定义函数时，创建的帧与该函数具有相同的父函数。

随后，名称 `sqrt_update` 解析为这个新定义的函数，该函数作为参数传给函数 `improve` 。在 `improve` 函数的函数体中，我们必须以猜测的初始值 `x` 为1来调用 `update` 函数（绑定到 `sqrt_update` ）。这个最后的程序调用为 `sqrt_update` 创建了一个环境，它以一个仅包含 `x` 的本地帧开始，但父帧 `sqrt` 仍然包含 `a` 的绑定。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220906203821307.png" alt="image-20220906203821307" style="zoom:80%;" />

这个求值过程中最终的部分，是将 `sqrt_update` 的父环境变成了通过调用 `sqrt_update` 创建的（局部）帧。此帧用`[parent=f1]` 进行注释。

**扩展环境（Extended Environments）**。一个环境由一长串帧构成，并且总是以全局帧结束。在举 `sqrt` 这个例子之前，环境最多只包含两种帧：局部帧和全局帧。通过调用在其他函数中定义的函数，使用嵌套的 `def` 语句，我们可以创建更长的（帧）链。调用 `sqrt_update` 的环境由三个帧组成：局部帧 `sqrt_update` 、定义`sqrt_update` 的 `sqrt` 帧（标记为 `f1`）和全局帧。

`sqrt_update` 函数体中的返回表达式可以通过遵循这一帧链来解析 `a` 的值。查找名称会找到当前环境中绑定到该名称的第一个值。Python 首先在 `sqrt_update` 帧中进行检查 —— 不存在 `a` ，然后又到 `sqrt_update`  的父帧 `f1` 中进行检查，发现 `a` 被绑定到了256。

因此，我们发现了 Python 中词法作用域的两个关键优势。

- 局部函数的名称不会影响定义它的函数的外部名称，因为局部函数的名称将绑定在定义它的当前局部环境中，而不是全局环境中。
- 局部函数可以访问外层函数的环境。这是因为局部函数的函数体的求值环境扩展于定义它的求值环境。

这里的 `sqrt_update` 函数自带了一些数据：`a` 在定义它的环境中引用的值，因为它以这种方式“封装”信息，所以局部定义的函数通常被称为**闭包（closures）**。

####  Functions as Returned Values（函数作为返回值）

程序可以通过创建返回值本身就是函数的函数以获得更多的表现力。带有词法作用域的编程语言的一个重要特性就是，局部定义函数在它们返回时仍旧持有所关联的环境。下面的例子展示了这一特性的作用。

一旦定义了许多简单的函数，函数**组合（composition）**就成为编程语言中的一种自然的组合方法。也就是说，给定两个函数 `f(x)` 和 `g(x)`，我们可能想要定义 `h(x) = f(g(x))` 。我们可以使用我们现有的工具来定义函数组合：

```python
>>> def compose1(f, g):
        def h(x):
            return f(g(x))
        return h
```

这个例子的环境关系图显示了即使存在冲突的名称时候， `f` 和 `g` 是如何被正确解析的。

```python
def square(x):
    return x * x

def successor(x):
    return x + 1

def compose1(f, g):
    def h(x):
        return f(g(x))
    return h

def f(x):
    """Never called."""
    return -x

square_successor = compose1(square, successor)
result = square_successor(12)
```

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220908174911571.png" alt="image-20220908174911571" style="zoom:80%;" />

`compose1` 中的1表示这个组合的函数只有一个参数。这个命名惯例不是解释器强制要求的；1只是函数名的一部分而已。

在这一点上，我们开始观察我们花费努力精确定义计算环境模型的好处：即获得不需要修改环境模型就可以解释返回函数的能力。（At this point, we begin to observe the benefits of our effort to define precisely the environment model of computation. No modification to our environment model is required to explain our ability to return functions in this way.）

#### Example : Newton's Mehod（示例：牛顿法）

这个扩展的示例展示了函数返回值和局部定义如何协同工作，以简洁地表达一般的思想。我们将实现一种广泛用于机器学习、科学计算、硬件设计和优化的算法。

**牛顿法（Newton's method）**是一种经典的迭代方法，用于查找返回值为 0 的数学函数的参数。这些值（参数）称为函数的**零点（Zeros）**。找到函数的零点通常等同于解决了其他一些有趣的问题，例如求平方根。

在我们继续之前，有一个激励人心的评论说到：我们很容易想当然地认为我们知道如何计算平方根。不只是Python，你的手机、网页浏览器或袖珍计算器都可以为你做到这一点。然而，学习计算机科学的一部分是理解这样的量如如何进行计算的，此处介绍的一般方法适用于求解 Python 内置方程之外的一大类方程。

牛顿法是一种迭代改进算法：它会对所有**可微（differentiable）**函数的零点的猜测值进行改进，这意味着它可以在任意点用直线进行近似处理。牛顿的方法遵循这些线性近似（Linear approximations）来找到函数零点。

> 翻译注：
>
> 牛顿法的视频介绍：https://www.bilibili.com/video/BV1Nt411T7HT?vd_source=a98d485f6a1d9dd779d8dd56de5549f6
>
> 
>
> 牛顿法求根公式，下标 n 一般为改进的次数：
> $$
> x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}
> $$
> 这个公式很好推，使用点斜式就能很轻松推出来。

试想一条穿过点 $$(x, f(x))$$ 的直线与函数 $$f(x)$$ 在该点拥有相同的斜率。这样的直线我们称之为**切线（tangent）**，它的斜率我们称之为 $$f$$ 在 $$x$$ 处的**导数（derivative）**。

这条直线的斜率是函数值变化量与函数参数变化量的比值，所以，按照 $$f(x)$$ 除以这个斜率来平移 $$x$$ ，就会得到切线到达 $$0$$ 时的 $$x$$ 值。（Hence, translating $$x$$ by $$f(x)$$ divided by the slope will give the argument value at which this tangent line touches 0.）

![newton](https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/newton.png)

下面的 `newtown_update` 改进算法函数就表示切线函数值等于 0 时的（x 值）的计算过程。 函数用 $$f$$ 表示，函数的导数用 $$df$$ 表示：

```python
>>> def newton_update(f, df):
        def update(x):
            return x - f(x) / df(x)
        return update
```

 最终，我们可以就能够根据改进算法 `newton_update`  定义 `find_root` 函数。并比较 `f(x)` 是否接近 `0` 。

```python
>>> def find_zero(f, df):
        def near_zero(x):
            return approx_eq(f(x), 0)
        return improve(newton_update(f, df), near_zero)
```

**求根（Computing Roots）**。使用牛顿法，我们可以算出 $$n$$ 次根。比如，$$x·x·x·x...x = a$$，$$x$$ 相乘 $$n$$ 次， 那么 $$a$$ 的 $$n$$ 次根是 $$x$$ 。

- $$64$$ 的平方（二次方）根书是 $$8$$，因为 $$8·8 = 64$$。
- $$64$$ 的立方（三次方）根是 $$4$$ ，因为 $$4·4·4 = 64$$ 。
- $$64$$ 的六次方根是 $$2$$ ，因为$$2·2·2·2·2·2 = 64$$ 。

我们可以使用牛顿法，根据下面的发下来求根：

- $$64$$ 的平方根（写作 $$\sqrt{64}$$） 是 $$x^2 - 64 = 0$$ 时 $$x$$ 的值。
- 一般地，$$a$$ 的 $$n$$ 次方根（写作 $$\sqrt[n]{64}$$）是 $$x^n - a = 0$$ 时 $$x$$ 的值。

如果我们能过找到上面等式的一个零点，我们就能计算出其 $$n$$ 次根。通过绘制 $$n$$ 等于 2、3 和 6 以及 $$a$$ 等于 64 的曲线，我们可以直观地看到这个关系。

![curves](https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/curves.png)

我们首先通过定义函数 `f` 和它的导数 `df` 来实现函数 `squre_root` 。我们使用微积分中的已有知识： $$f(x)=x^2 + a$$ 的导函数为 $$df(x) = 2x$$。

```python
>>> def square_root_newton(a):
        def f(x):
            return x * x - a
        def df(x):
            return 2 * x
        return find_zero(f, df)
      
>>> square_root_newton(64)
8.0
```

推广到 $$n$$ 阶，我们可以得出：$$f(x) = x^n - a$$ 与它的导函数 $$f(x) = n·x^{n-1}$$ 。

```python
>>> def power(x, n):
        """Return x * x * x * ... * x for x repeated n times."""
        product, k = 1, 0
        while k < n:
            product, k = product * x, k + 1
        return product
>>> def nth_root_of_a(n, a):
        def f(x):
            return power(x, n) - a
        def df(x):
            return n * power(x, n-1)
        return find_zero(f, df)
>>> nth_root_of_a(2, 64)
8.0
>>> nth_root_of_a(3, 64)
4.0
>>> nth_root_of_a(6, 64)
2.0
```

所有这些计算中的近似误差都可以通过将 `approx_eq` 中的公差更改为更小的数字来减小。

当你使用牛顿法时，要注意它并不总是收敛（converge）的。`improve` 的初始猜测必须足够接近零，并且必须满足关于函数的各种条件。尽管有这个缺点，但牛顿法是求解可微方程的一种强大的通用计算方法。现代计算机技术中的对数和大整数除法的快速算法，采用了该方法的变体。

#### Curring（柯里化）

我们可以使用高阶函数将一个接受多个参数的函数转换为一个函数链（a chain of functions），每个函数接受一个参数。更具体地说，给定一个函数 `f(x, y)` ，我们可以定义一个函数 `g` ，使得 `g(x)(y)`  等价于 `f(x, y)`。z在这里，`g` 是一个高阶函数，它接受单个参数 `x` 并返回另一个接受单个参数 `y`的函数。这种转换称为**柯里化（Curring）**。

例如，我们可以定义 pow 函数的柯里化版本：

```python
>>> def curried_pow(x):
        def h(y):
            return pow(x, y)
        return h
>>> curried_pow(2)(3)
8
```

一些编程语言，例如 Haskell，只允许使用单个参数的函数，因此程序员必须对所有多参数过程进行柯里化。在 Python 等更通用的语言中，当我们需要一个只接受单个参数的函数时，柯里化就很有用。例如，`map` 模式（*map* pattern）就可以将单参数函数应用于一串值。在下个章节中，我们将看到更通用的 `map` 模式的示例，但现在，我们可以在函数中实现该模式：

```python
>>> def map_to_range(start, end, f):
        while start < end:
            print(f(start))
            start = start + 1
```

我们可以使用 `map_to_range` 和 `curried_pow` 来计算 2 的前十次方，而不是专门编写一个函数来这样做：

```python
>>> map_to_range(0, 10, curried_pow(2))
1
2
4
8
16
32
64
128
256
512
```

我们可以类似地使用相同的两个函数来计算其他数字的幂。 柯里化允许我们这样做，而无需为我们希望计算其幂的每个数字编写特定的函数。

在上面的例子中，我们手动对 `pow` 函数进行了柯里化变换，得到了 `curried_pow` 。相反，我们可以定义函数来自动化柯里化，以及逆柯里化变换（*uncurrying* transformation）：

```python
>>> def curry2(f):
        """Return a curried version of the given two-argument function."""
        def g(x):
            def h(y):
                return f(x, y)
            return h
        return g
>>> def uncurry2(g):
        """Return a two-argument version of the given curried function."""
        def f(x, y):
            return g(x)(y)
        return f
>>> pow_curried = curry2(pow)
>>> pow_curried(2)(5)
32
>>> map_to_range(0, 10, pow_curried(2))
1
2
4
8
16
32
64
128
256
512
```

curry2 函数接受一个双参数函数 `f` 并返回一个单参数函数 `g`。当 `g`应用于参数 `x` 时，它返回一个单参数函数 `h` 。当 `h` 应用于 `y` 时，它调用 `f(x, y)`。因此，`curry2(f)(x)(y)` 等价于 `f(x, y)` 。`uncurry2` 函数反转了柯里化变换，因此 `uncurry2(curry2(f))` 等价于 `f` 。

```python
>>> uncurry2(pow_curried)(2, 5)
32
```

#### Lambda Expressions（Lambda 表达式）

到目前为止，每当我们想要定义一个新函数时，我们都需要给它一个名字。 但是对于其他类型的表达式，我们不需要将中间值与名称相关联。 也就是说，我们可以计算 `a * b + c * d` 而不必命名子表达式 `a*b` 或 `c*d` 或完整的表达式。 在 Python 中，我们可以使用 lambda 表达式动态创建函数，该表达式计算为未命名的函数。 一个 lambda 表达式的计算结果是一个函数，它有一个作为主体的返回表达式。 并且不允许使用赋值和控制语句。

```python
>>> def compose1(f, g):
        return lambda x: f(g(x))
```

我们可以使用如下构造的对应的英文句子来理解 lambda 表达式的结构：

```python
     lambda            x            :          f(g(x))
"A function that    takes x    and returns     f(g(x))"
```

lambda 表达式的结果称为 lambda 函数。它没有固有名称（因此 Python 打印 `<lambda>` 作为名称），但除此之外它的行为与任何其他函数一模一样。

```python
>>> s = lambda x: x * x
>>> s
<function <lambda> at 0xf3f490>
>>> s(12)
144
```

在环境图中，lambda 表达式的结果也是一个函数，以希腊字母 λ （lambda）命名。我们的 `compose` 示例可以用 lambda 表达式表达出来：

```python
def compose1(f, g):
    return lambda x: f(g(x))

f = compose1(lambda x: x * x,
             lambda y: y + 1)

result = f(12)
```

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220909015232945.png" alt="image-20220909015232945" style="zoom:80%;" />

一些程序员发现，从lambda表达式中使用未命名的函数更短、更直接。然而，尽管复合lambda表达式很简洁，但它是出了名的难以辨认。下面的定义是正确的，但是许多程序员很难快速理解它。

```python
>>> compose1 = lambda f,g: lambda x: f(g(x))
```

一般来说，Python 风格更喜欢显式 `def` 语句而不是lambda 表达式，但在需要一个简单函数作为参数或返回值的情况下允许使用它们。

这样的规则只是指导方针；你可以随心所欲地编程。然而，在编写程序时，要考虑到有一天可能会阅读你的程序的受众。当你的程序容易理解时，你就帮了那些人一个忙。

术语lambda 是一个历史上的偶然事件，它源于书面数学表示法的不兼容性和早期类型设置系统的约束。

> 使用lambda 引入过程/函数似乎有点反常。这种符号可以追溯到阿朗佐·丘奇（Alonzo Church），他在20世纪30年代开始使用”帽子“符号；他把平方函数写成 “ŷ. y × y” 。但是沮丧的是，印刷工人将帽子移到参数的左边，并将其改为大写的lambda : “Λy. y × y”；从这里，大写的lambda 变为小写，就是现在我们看到样式了，在数学书中是“λy. y×y”。在Lisp 中是`(lambda (y) (* y y))`。
>
> —Peter Norvig (norvig.com/lispy2.html)

尽管有着不同寻常的词源，lambda 表达式和对应的函数应用程序形式语言lambda 微积分，都是远远超出 Python 编程社区共享的基本计算机科学概念。我们将在第 3 章中研究解释器的设计时重新讨论这个问题。

#### Abstractions and First-Class Functions（抽象和一等函数）

在本节开始时，我们注意到用户定义函数是一种至关重要的抽象机制，因为它们允许我们将一般的计算方法表示为编程语言中的显式元素。现在我们已经看到了高阶函数如何允许我们操作这些通用方法来创建进一步的抽象。

作为程序员，我们应该留意识别程序中的底层抽象，并在它们的基础上进行（进一步的）抽象构建，并将它们一般化，以创建更强大的抽象。这并不是说我们应该总是以最抽象的方式编写程序；专业的程序员知道如何选择适合的抽象级别。但重要的是能够从这些抽象的角度来进行思考，这样我们就能在新的上下文中使用它们了。高阶函数的意义在于，它使我们能够在编程语言中显式地将这些抽象表示为元素，这样就可以像处理其他计算元素一样来处理它们了。

一般而言，编程语言对可操作计算元素的处理方式施加了限制。限制最少的元素被称为一等元素。一等元素的一些 “权利和特权” 是：

1. 它们可以绑定到名称。
2. 它们可以作为参数向函数传递。
3. 它们可以作为函数的返回值返回。
4. 它们可以包含在数据结构中。

Python 授予函数完全的一等地位，由此带来的表达能力的提升是巨大的。

#### Function Decorators（函数装饰器）

Python 提供了特殊的语法来应用高阶函数作为执行 `def` 语句的一部分，称为**装饰器（decorator）**。最常见的例子是用来追踪：

```python
>>> def trace(fn):
        def wrapped(x):
            print('-> ', fn, '(', x, ')')
             
        return wrapped
>>> @trace
    def triple(x):
        return 3 * x
>>> triple(12)
->  <function triple at 0x102a39848> ( 12 )
36
```

在这个示例中，定义了一个高阶函数 `trace` ，该函数返回一个函数，这个被返回的函数在对其参数的调用之前使用输出参数的 `print` 语句。`triple` 的 `def` 语句有一个注解（annotation） `@trace` ，它影响了 `def` 的执行规则。像通常一样，函数`triple`被创建了，然而，名称 `triple` 并不与被创建的函数绑定。名称 `triple` 被绑定到新定义的 `triple` 函数调用 `trace` 的返回函数值上。在代码中，这个装饰器等价于：

```python
>>> def triple(x):
        return 3 * x
  
>>> triple = trace(triple)
```

在与本文相关的项目中，装饰器用于跟踪，以及在从命令行运行程序时选择要调用的函数。

**专家的“小灶”。（Extra for experts）**装饰符号 `@` 也可以跟在调用表达式之后。首先计算 `@` 后面的表达式（就像上面的名称 `trace` 一样），然后是 `def` 语句，最后将装饰器表达式的计算结果应用到新定义的函数，并将结果绑定到 `def` 语句中的名称。对装饰器的使用感兴趣的同学，参见Ariel Ortiz 编写的此文：[A short tutorial on decorators](https://towardsdatascience.com/the-simplest-tutorial-for-python-decorator-dadbf8f20b0f)

> 原文链接http://programmingbits.pythonblogs.com/27_programmingbits/archive/50_function_decorators.html，已经失效。

## Lecture 6 : Recursion & Lecture 7 : Tree Recursion

### 递归函数（Resursive Functions） 

如果函数体直接或间接调用函数本身，则称为递归函数。也就是说，在执行递归函数体的过程中，可能需要再次应用该函数。递归函数在 Python 中不使用任何特殊的语法，但确实需要一些努力才能理解和创建递归函数。

我们将从一个示例问题开始：编写一个对自然数自身的所有数字求和的函数。在设计递归函数时，我们寻找将问题分解为更简单问题的方法。在本例中，操作符 `%` 和 `//` 可用于将数字分为两部分：其最后一位和除最后一位以外的所有其他数字。

```python
>>> 18117 % 10
7
>>> 18117 // 10
1811
```

18117 的所有数字求和为： `1+8+1+1+7 = 18` 。就像我们可以把数分开一样，我们可以把这个和分成最后一位`7` 和除最后一位以外的所有数字的和 `1+8+1+1 = 11` 。这种分离给我们提供了一个算法：要对一个数 `n` 的所有数字求和，将其最后一个数字 `n % 10` 与 `n // 10` 的数字之和相加。有一种特殊情况：如果数只有一个数字，那么它的数字之和就是它自己。该算法可以实现为递归函数。

```python
>>> def sum_digits(n):
        """Return the sum of the digits of positive integer n."""
        if n < 10:
            return n
        else:
            all_but_last, last = n // 10, n % 10
            return sum_digits(all_but_last) + last
```

`sum_digits` 的这个定义是完整且正确的，尽管 `sum_digits` 函数是在它自己的函数体中调用的。一个数的数字和的问题可以分为两个步骤：把除最后一位外的所有数相加，然后把最后一位相加。这两个步骤都比原来的问题简单。这个函数是递归的，因为第一步和原来的问题是一样的。也就是说，`sum_digits` 自身正是实现 `sum_digits` 所需要的函数。

```python
>>> sum_digits(9)
9
>>> sum_digits(18117)
18
>>> sum_digits(9437184)
36
>>> sum_digits(11408855402054064613470328848384)
126
```

利用我们的计算环境模型，我们可以准确地理解这个递归函数是如何成功应用的。这里不需要添加新的规则。

<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220913123057931.png" alt="image-20220913123057931" style="zoom:80%;" />

在执行 `def` 语句时，名称 `sum_digits` 被绑定到一个新函数，但该函数的主体还没有执行。因此，`sum_digits` 的**循环特性（circular nature）**还不是问题。然后，在 738 上调用 `sum_digits` ：

1. 为 `sum_digits` 创建一个`n` 绑定为738的局部帧，并在以该帧开始的环境中执行 `sum_digits` 的函数主体。
2. 因为 738 不小于 10，所以执行第4行上开始的赋值语句，将 738 拆分成 73 和 8。
3. 在下面的返回语句中，`sum_digits` 在当前环境中的 `all_but_last` 值为 73 处被调用。
4. 为 `sum_digits` 创建另一个局部帧，这次 `n` 绑定到 73 。`sum_digits` 的函数主体再次在以此帧开始的新环境中执行。
5. 因为 73 也不小于 10，所以 73 被分为 7 和 3，并在 7 处调用 `sum_digits`，即 `all_but_last` 在此帧中的值。
6. 为 `sum_digits` 创建第三个局部帧，其中 `n` 绑定到7。
7. 在此帧开始的环境中，`n < 10`值为真，因此返回7。
8. 在第二个局部帧中，返回值 7 与 `last` 的值 3 相加，返回10。
9. 在第一个局部帧中，返回值 10 与 `last` 的值 8 相加，返回18。

尽管这个递归函数具有循环特性，但它正确地应用了两次，且每次都使用了不同的参数。此外，第二次应用此程序的对象是一个比第一次更简单的数字求和问题。生产调用 `sum_digits(18117)` 的环境关系图，查看每次对 `sum_digits` 的连续调用情况得知，每次调用该函数的参数都比上一次小，直到最终为单个数字为止（停止调用`sum_digits` ）。

这个例子还说明了具有简单函数体的函数如何通过使用递归，从而演化出复杂的计算过程。

#### 递归函数剖析（The Anatomy of Recursive Functions）

在许多递归函数的主体中可以找到一个常见的模式。主体以一个**基础条件（base case）**开始，这是一个条件语句，为最容易处理的输入定义函数的行为。在 `sum_digits` 的情况下，基础条件是任何单位数参数，我们只是返回该参数。有些递归函数有多个基础条件。

然后在基础条件（base case）之后进行一个或多个递归调用。递归调用总是具有特定的特性：他们简化了原始问题。递归函数通过逐步简化问题来表达计算过程。例如，对 7 的数字求和比对 73 的数字求和简单，而对 73 的数字求和又比对 738 的数字求和更简单。因此，对于每个后续调用，剩下要做的工作就更少了。

递归函数解决问题的方法通常不同于我们之前使用的迭代方法。思考计算 `n` 的阶乘的函数 `fact` ，当 `n` 为 4 时，计算得： $$ 4! = 4 · 3 · 2 · 1 = 24 $$  。

 使用 `while` 语句的实现为通过将每个不超过 `n` 的正整数进行累乘，从而得到总数。

```python
>>> def fact_iter(n):
        total, k = 1, 1
        while k <= n:
            total, k = total * k, k + 1
        return total
>>> fact_iter(4)
24
```

另一方面，递归函数 `fact(n)` 的实现可以用 `fact(n-1)` 表示。这个 `fact(n-1)` 就是一个更简单的问题了。该递归最简单的基础条件（base case）是 `fact(1)` ，也就是 1 了。

```python
def fact(n):
    if n == 1:
        return 1
    else:
        return n * fact(n-1)

fact(4)
```



<img src="https://yuzu-personal01.oss-cn-shenzhen.aliyuncs.com/img/image-20220913201631413.png" alt="image-20220913201631413" style="zoom: 67%;" />

这两个阶乘函数（实现逻辑）在概念上有所不同。迭代函数通过在每一项中连续相乘，来构造从基础条件 1 到最终总数 `n` 的结果。另一方面，递归函数直接从最终项 `n` 和更简单的问题 `fact(n-1)` 的结果来构造出最终结果。

当递归通过 `fact` 函数的连续应用，逐步“展开”为越来越简单的问题实例时。最终结果将从基础条件开始构建。递归通过将参数 1 传递给 `fact` 来结束递归；每次调用的结果取决于下一次调用，直到达到基础条件。

这个递归函数的正确性很容易从数学函数阶乘的标准定义中验证：

$$(n-1)! = (n-1)·(n-1)...1$$

$$n! = n·(n-1)·(n-2)...1$$

$$n! = n · (n-1)!$$

虽然可以使用我们的计算模型展开递归，但将递归调用（recursive calls）看作函数抽象通常会让思维更清晰一点。也就是说，我们不用在意 `fact(n-1)` 在 `fact` 的函数体中是怎么实现的；我们只需要相信它能计算 `n-1` 的阶乘就好了。将递归调用看作一种函数抽象（这一思想），就是历来所谓的**“递归的信仰之跃”（recursive leap of faith）**。我们根据函数自身来定义一个函数，在验证函数的正确性时，只是相信在更简单的情况下，函数t哦同样能正常工作就行。在这个示例中，我们相信函数 `fact(n-1)` 能够正确计算  `(n-1)!` ；如果假设成立，我们只需要检查 `n!` 是否计算正确。这样，验证递归函数的正确性就是一种归纳法的证明形式。

函数 `fact_iter` 和 `fact` 也不同，因为前者必须引入两个额外的名称，`total` 和 `k`，这在递归实现中是不需要的。一般来说，迭代函数必须保持在整个计算过程中变化的局部状态。在迭代中的任何时候，该状态都表征了已完成计算的结果和剩余的待计算的量。例如，当 `k = 3` ， `total = 2`时，仍然有两项需要处理，即 3 和 4 。另一方面，`fact` 的特征是它的单一参数 `n` 。计算的状态完全包含在环境的结构中，它的返回值扮演 `total` 的角色，并将`n` 绑定到不同帧中的不同值，而不是显式地跟踪 `k` 。

递归函数利用计算调用表达式的规则将名称绑定到值，通常避免了在迭代期间正确分配本地名称的麻烦。由于这个原因，递归函数更能容易正确进行定义。然而，学习识别由递归函数演化而来的计算过程确实也需要实践。

#### 互递归（Mutual Recursion）

当递归过程被划分到两个相互调用的函数中时，这两个函数被称作是**互递归的（mutually recursive）**。例如，思考以下非负整数的偶数和奇数定义：

- 如果一个数比一个奇数大1，那它就是偶数
- 如果一个数比一个偶数大1，那它就是奇数
- 0 是偶数

使用这个定义，我们可以实现一个互递归函数来确定一个数字是偶数还是奇数：

```python
def is_even(n):
    if n == 0:
        return True
    else:
        return is_odd(n-1)

def is_odd(n):
    if n == 0:
        return False
    else:
        return is_even(n-1)

result = is_even(4)
# Ture
```

通过打破两个函数之间的抽象边界，可以将互递归函数转换为单个递归函数。在此示例中，`is_odd` 的函数体可以合并到 `is_even` 的函数体中，确保将 `is_odd` 函数体中的 `n` 替换为 `n-1` 以反映传递给它的参数：

```python
>>> def is_even(n):
        if n == 0:
            return True
        else:
            if (n-1) == 0:
                return False
            else:
                return is_even((n-1)-1)
```

因此，互递归并不比简单递归更神秘或更强大，它提供了一种在复杂递归程序中维护抽象的机制。

#### 递归函数中的打印（Printing in Recursive Functions）

递归函数演化出的计算过程通常可以通过调用 `print` 来可视化。作为示例，我们将实现一个函数 `cascade`，以从最大到最小再到最大的顺序，打印一个数的所有前缀。

```python
>>> def cascade(n):
        """Print a cascade of prefixes of n."""
        if n < 10:
            print(n)
        else:
            print(n)
            cascade(n//10)
            print(n)
>>> cascade(2013)
2013
201
20
2
20
201
2013
```

在这个递归函数中，基本条件是一个打印出来个位数。否则，就在两个 `print` 调用之间使用递归调用。

在递归调用之前写出基本条件表达式并不是一个严格的要求。事实上，通过观察得知， `print(n)` 在条件语句的两个子句中重复，因此将基本条件放在所有子句之前可以使得函数的表达更完整。

```python
>>> def cascade(n):
        """Print a cascade of prefixes of n."""
        print(n)
        if n >= 10:
            cascade(n//10)
            print(n)
```

另一个互递归的例子，请思考一个两人博弈的情景，在这个博弈中，桌子上最初有 `n` 个石子。玩家轮流从桌面上拿走一颗或两颗石子，拿走最后一颗石子的玩家获胜。假设 Alice 和 Bob 玩这个游戏，每个人都使用一个简单的策略：

- Alice 只拿走一个石子
- 如果桌子上有偶数个石子，Bob 就拿走两个石子，否则就拿走一个石子

以 `n` 作为初始的石子数量且Alice 先拿，谁最终会赢得这个游戏？

这个问题的分解，是将每个策略封装在它自己的函数中。这使得我们修改一个策略而不会影响另一个策略，并且保持两者之间的抽象“界限”。为了融入游戏的回合制性质，这两个函数在每个回合结束时相互调用。

```python
>>> def play_alice(n):
        if n == 0:
            print("Bob wins!")
        else:
            play_bob(n-1)
            
>>> def play_bob(n):
        if n == 0:
            print("Alice wins!")
        elif is_even(n):
            play_alice(n-2)
        else:
            play_alice(n-1)
            
>>> play_alice(20)
Bob wins!
```

在函数 `play_bob` 中，我们看到函数体中可能会出现多个调用。然而，在这里示例中，每次调用 `play_bob` 最多只会调用一次 `play_alice` 。在下个小节中，我们会思考当单个函数调用同时直接调用了多个递归函数时会发生什么。

#### 树递归（Tree Recursion）

另一种常见的计算模式称为**树递归（Tree recursion）**，在这种模式中，函数会多次调用自己。例如计算斐波那契数列，其中每个数是前两个数的和。

```python
def fib(n):
    if n == 1:
        return 0
    if n == 2:
        return 1
    else:
        return fib(n-2) + fib(n-1)

result = fib(6)
```

相对于我们之前的尝试，这个递归定义非常吸引人：它完全反映了我们熟悉的斐波那契数的定义。具有多个递归调用的函数称为树递归，因为每个调用分支为多个更小的调用，（更小的调用中）每个调用又分支为更小的调用，就像树的分支从主干延伸到更小，同时数量会变得更多一样。

我们已经能够不用树递归定义一个函数来计算斐波那契数列。事实上，我们以前的方法更高效，这是本文后面讨论的主题。接下来，我们思考一个问题，对于这个问题，树递归解基本上比任何迭代方案都简单。

#### 示例：整数拆分（Example : Partions）

对一个正整数 `n` 进行整数拆分，是使用大小为 `m` 的部分（拆分项）进行拆解，即 `n` 可以表现为不大于 `m` 的正整数总和。并且（拆分项）按递增顺序排列的方法。例如，用 4 以内的部分对 6 进行拆分的次数是 9 。

> 翻译注：这里说的不清不楚的，或者是我的英文水平太低了，总之读起来很拗口，这里给出数学上整数拆分的定义，以备参考：
>
> 一个正整数可以写成一些正整数的和。在数论上，跟这些和式有关的问题称为**整数拆分**、、**整数剖分**、**整数分割**、**分割数**或**切割数**（英语：Integer partition）。其中最常见的问题就是给定正整数$$n$$，求不同数组$$(a_1, a_2, a_3, ..., a_k)$$ 的数目，符合下面的条件：
>
> 1. $$a_1 + a_2 + ... + a_k = n （k 的大小不定）$$
>
> 2. $$a_1 \geq a_2 \geq ... \geq a_k \geq 0$$
>
> 3. 其他附加条件（例如限定“k是偶数”，或“ $$a_i$$ 不是1就是2” 等）
>
>    
>
> 分割函数 `p(n)` 是求符合以上第一、二个条件的数组数目。

1.  `6 = 2 + 4`
2. `6 = 1 + 1 + 4`
3. `6 = 3 + 3`
4. `6 = 1 + 2 + 3`
5. `6 = 1 + 1 + 1 + 3`
6. `6 = 2 + 2 + 2`
7. `6 = 1 + 1 + 2 + 2`
8. `6 = 1 + 1 + 1 + 1 + 2`
9. `6 = 1 + 1 + 1 + 1 + 1 + 1`

我们可以定义一个函数 `count_partions(n, m)` ，该函数返回使用 `m` 进行整数拆分时的（数组）数目。这个函数有一个简单的解作为树递归函数。基于如下的观察：

使用不超过 `m` 的整数拆分 `n` 的方法数等于：

1. 使用最大为 `m` 的整数对 `n-m` 进行拆分的方法数量，以及
2. 使用最大为 `m-1` 的整数对 `n` 进行拆分的方法数量。

要知道为什么上面的方法正确的，观察可得，所有拆分 `n` 的方法都可以被分为两种：至少包含一个 `m` 的和那些不包含 `m` 的。并且，第一种中的每个拆分都是一个 `n-m` 的拆分，最后再加上 `m` 。在上面的示例中，前两个拆分包含 4，其余拆分不包含 4 。

因此，我们可以递归地将使用最大为 `m` 的整数拆分 `n` 的问题简化为两个更简单的问题：（1）拆分更小的数 `n-m` ，与 （2）使用小于 `m-1` 的数进行拆分。

为了完成实现，我们需要指定以下基本情况：

1. 拆分 0 有一种方法：不包含任何部分。
2. 没有拆分负数 `n` 的方法。
3. 没有可以使用大小为 0 或更小的数对任何大于 0 的 `n` 进行拆分。

```python
>>> def count_partitions(n, m):
        """Count the ways to partition n using parts up to m."""
        if n == 0:
            return 1
        elif n < 0:
            return 0
        elif m == 0:
            return 0
        else:
            return count_partitions(n-m, m) + count_partitions(n, m-1)
          
>>> count_partitions(6, 4)
9
>>> count_partitions(5, 5)
7
>>> count_partitions(10, 10)
42
>>> count_partitions(15, 15)
176
>>> count_partitions(20, 20)
627
```

我们可以将树递归函数视为探索不同的可能性。 在这种情况下，我们探讨了我们使用大小为 `m` 的部分的可能性以及我们不使用的可能性。 第一次和第二次递归调用对应于这些可能性。

在没有递归的情况下实现此功能将涉及更多。鼓励有兴趣的读者尝试。

# Chapter 2: Building Abstractions with Data

## Lecture 10 : Container

### 引言（Introduction）

我们在第一章里关心的是**计算过程（computational processes）**，以及**函数（function）**在程序设计中扮演的角色。我们了解了如何使用**基本数据（数）（primitive data）**和**基本操作（算术运算）（primitive operation）**：通过组合和控制形成复合函数，以及通过给流程命名来创建函数抽象。我们还看到，高阶函数提升了语言的威力，使我们能够去操纵通用的计算方法，并能对它们做推理。这就是程序设计中最基本的东西。

本章的重点是数据。我们在这里研究的技术使我们可以表示和操作许多不同领域的信息。由于互联网的爆炸式增长，给所有人提供了大量的结构化信息，并且计算也可以应用于大量不同的问题。有效使用内置数据类型和用户定义的数据类型，是数据处理应用程序的基础。

#### 原生数据类型（Native Data Types）

Python 中的每个值都有一个类（class）来确定它值的类型。拥有相同类的值，行为也相同。例如，整数 1 和 2 都是 `int` 类的实例。这两个值就可以用相似的方法进行处理。比如，它们都可以被否定或与另一个整数相加。内置的 `type` 函数允许我们检查任何值的类。

```python
>>> type(2)
<class 'int'>
```

到目前为止，我们使用的值都是 Python 语言中内置的少量原生数据类型的实例。原生数据类型具有以下属性：

1. 有求值结果（的类型）为原生数据类型的表达式，称为**字面量（literals）**。
2. 有用于操作原生类型值的内置函数与操作符。

`int` 类是用来表示整数的原生数据类型。整数字面量（相邻的数字序列）求值结果为 `int` 值，并且数学运算符可以操作这种值。

````python
>>> 12 + 3000000000000000000000000
3000000000000000000000012
````

Python 有三个原生数字类型：整数（ `int` ）、实数（ `float` ）和复数（ `complex` ）。

```python
>>> type(1.5)
<class 'float'>
>>> type(1+1j)
<class 'complex'>
```

浮点数（Floats，即上文中的实数）。“float” 这个名字来自于Python 和许多其他编程语言中实数的表示方式：具有“浮动的小数点” 的数。虽然数字如何表示的细节不是本文的主题，但了解 `int` 和 `float` 对象之间的一些区别也是很重要的。特别说明：`int` 对象可以精确地表示整数，没有任何近似处理或大小限制。另一方面，`float` 对象可以表示很大范围内的小数，但并不是所有的数字都能够被精确表示，`float` 有最小值和最大值。因此，`float` 值应该被视为接近真实值的值。这种近似处理只能保证有限的精度。组合 `float` 值可能会导致近似错误；如果不进行近似处理，下面两个表达式的值都是7。

> Python 3 的 `int` 类型确实是没有最大值的，这里可能有点反直觉，有余力的读者可以看这篇文章的介绍：https://segmentfault.com/a/1190000015284473

```python
>>> 7 / 3 * 3
7.0
>>> 1 / 3 * 7 * 3
6.999999999999999
```

尽管是 `int` 值相组合，使用一个 `int` 值除以另一个 `int` ，却会得到一个 `float` 值：一个截断的有限近似值，相当于两个整数相除的实际比值。

```python
>>> type(1/3)
<class 'float'>
>>> 1/3
0.3333333333333333
```

当我们进行相等测试时，这种近似处理也会出现问题。

```python
>>> 1/3 == 0.333333333333333312345  # Beware of float approximation（请注意浮点近似）
True
```

`int` 类和 `float` 类之间的这些细微差别对编写程序有着广泛的影响，因此，这也是程序员必须熟知的细节。幸运的是，原生数据类型数量只有屈指可数的几种，这限制了熟练掌握一门编程语言所需的记忆量。此外，这些细节在许多编程语言中都是一致的，由诸如 [IEEE 754浮点标准](http://en.wikipedia.org/wiki/IEEE_floating_point) 等社区指南强制要求执行。

**非数值类型（Non-numeric types）**。

值也可以表示许多其他类型的数据，如声音、图像、位置、web地址、网络连接等。少数类型可以用原生数据类型表示，例如表示 `True` 和 `False` 值的 `bool` 类。大多数值的类型必须由程序员使用我们将在本章中学到的组合和抽象方法来定义。

下面几节将介绍更多的 Python 原生数据类型，重点介绍它们在创建有用的数据抽象时所扮演的角色。对于那些对进一步细节感兴趣的人，在线书籍 [Dive Into Python 3](https://diveintopython3.net/) 中有一个关于[原生数据类型](https://diveintopython3.net/native-datatypes.html)的章节，它对所有 Python 的原生数据类型以及如何操作它们进行了实用的阐述，其中包含了大量的使用示例和实用提示。

> *Dive Into Python 3* 的中文版地址：http://www.ttlsa.com/docs/dive-into-python3/

## Lecture 11 : Data Abstraction

### 数据抽象（Data Abstraction）

当我们想在程序中表示世界中大量的事物的时候，我们发现这些事物大部分是具有复合结构的。比如，地理位置具有维度和经度坐标信息。为了表示位置，我们希望编程语言能够将经度和纬度“结合”在一起，形成一对数据，这是一个复合数据结构，我们的程序可以将它作为一个概念单元来操作，但它也可以被看作独立的两个部分。

复合数据的使用使我们能够增加程序的模块化。如果我们可以将地理位置作为整个值来操作，那么我们就可以将程序中使用位置信息进行**计算的部分**从“如何表示位置信息”这种**细节**中屏蔽出来。将程序中处理“数据如何表示的部分”和处理“数据如何操作”的部分进行分离的这种设计方法，称作**数据抽象（data abstraction）**。数据抽象让程序的设计，维护和更改更加容易。

数据抽象在性质上与函数抽象类似。当我们构建函数抽象的时候，具体的实现细节可以隐藏，特定的函数可以用有相同行为的任何其他函数进行替代。换句话说，我们可以构造抽象来使函数的使用方式和函数的实现细节分离。与此类似，数据抽象使我们将复合数据对象的使用细节与它的构造方式分离。

数据抽象的基本思想是构建对抽象数据进行操作的程序。也就是说，程序应该以这样一种方式使用数据，即对数据做尽可能少的假设。同时，将具体的数据表示定义为程序的独立部分。

程序的两个部分：对“抽象数据进行操作”的部分和“定义**具体表示（concrete representation）**”的部分，这两部分由一组根据**具体表示**实现抽象数据的函数进行连接，为了说明该方法，我们将思考如何设计一组用于操作有理数的函数。

#### 示例：有理数（Example : Rational Numbers）

有理数是整数的比值，且有理数构成了实数的重要重要子类。如有理数 `1/3` 和 `17/29` 通常可以写作：

```text
<numerator>/<denominator>
```

其中，`<numerator>` 和 `denominator` 整数的占位符。这两部分共同表述了这个有理数的精确值。实际上，整数相除会产生近似的浮点值，从而丢失了整数的精度。

```python
>>> 1/3
0.3333333333333333
>>> 1/3 == 0.333333333333333300000  # Dividing integers yields an approximation
True
```

然而，我们可以通过将分子和分母结合在一起来得到有理数的精确表示。

在实现程序的某些部分之前，我们已经知道使用函数抽象可以高效地进行编程。让我们首先假设现在我们已经有了一种从分子和分母构造有理数的方法。我们还假设，给定一个有理数，我们有办法选中它的分子和分母。让我们进一步假设，构造函数和选择器可以由以下三个函数提供：

- `rational(n, d)` 返回由分子 `n` 和分母 `d` 构成的有理数。
- `numer(x)` 返回有理数 `x` 的分子。
- `denom(x)` 返回有理数 `x` 的分母。

我们在使用一种强大的程序设计策略：*心想事成（wishful thinking）*。我们没有说明有理数怎么表示，也没有说明 `numer` ， `denom` 和 `rational` 这些函数的具体实现。即便如此，如果我们确实定义了这三个函数，那么，我们就可以对有理数进行加、乘、打印和检验。

```python
>>> def add_rationals(x, y):
        nx, dx = numer(x), denom(x)
        ny, dy = numer(y), denom(y)
        return rational(nx * dy + ny * dx, dx * dy)
    
>>> def mul_rationals(x, y):
        return rational(numer(x) * numer(y), denom(x) * denom(y))
    
>>> def print_rational(x):
        print(numer(x), '/', denom(x))
        
>>> def rationals_are_equal(x, y):
        return numer(x) * denom(y) == numer(y) * denom(x)
```

现在我们拥有了选择器函数 `numer` 和 `denom`， 与构造函数 `rational` （共同）定义的操作，但是到目前为止，我们还没有定义这些函数。我们需要以某种方式来将分子和分母“粘合”为一个单元。

#### 组合（Pairs）

为了使我们能够实现数据抽象的具体级别，Python 提供了一种称为 `list` （列表）的复合结构，它可以通过将表达式放在用逗号分隔的方括号中来构造。这样的表达式称为**列表字面量（list literal）**。

```python
>>> [10, 20]
[10, 20]
```

有两种获访问列表元素的方法。第一种是我们熟悉的**多重赋值（multiple assignment）**方法，它将列表解构为元素，并将每个元素绑定到不同的名称。

```python
>>> pair = [10, 20]
>>> pair
[10, 20]
>>> x, y = pair
>>> x
10
>>> y
20
```

第二种访问列表元素的方法是使用**元素选择操作符（element selection operator，也叫下标运算符）**，也是一个方括号。与列表字面量不同，直接跟在另一个表达式后面的方括号表达式不会求值为列表，而是从前一个表达式的值中选择一个元素。

```python
>>> pair[0]
10
>>> pair[1]
20
```

Python 中的列表（以及大多数其他编程语言中的序列）的索引是从 0 开始（0-indexed）的，意思是索引 0 选择到的是第一个元素，索引 1 是第二个，然后依次类推。我们对这个下标惯例的直觉是，下标表示一个元素距离列表开头元素的距离。

与元素选择运算符等效的函数为 `getitem` ，该函数的索引也是从 0 开始，在列表元素中进行选择的。

```python
>>> from operator import getitem
>>> getitem(pair, 0)
10
>>> getitem(pair, 1)
20
```

两个元素的列表并不是 Python 中表示组合的唯一方法。任何表示将两个元素“绑定”为整个元素的方法都可以认为是一种组合。列表是这样做的常用方法。列表能包含的元素也不止两个，我们会在本章的后续部分进行探讨。

**有理数的表示（Representing Rational Numbers）** 。我们现在可以将有理数表述为两个整数的组合：一个为分子，一个为分母。

```python
>>> def rational(n, d):
        return [n, d]
    
>>> def numer(x):
        return x[0]
    
>>> def denom(x):
        return x[1]
```

与先前定义的数学运算相结合，我们就可以使用定义的这些函数来操作有理数了。

```python
>>> half = rational(1, 2)
>>> print_rational(half)
1 / 2
>>> third = rational(1, 3)
>>> print_rational(mul_rationals(half, third))
1 / 6
>>> print_rational(add_rationals(third, third))
6 / 9
```

上面的示例中，我们的有理数的实现并没有将有理数简化为最简式。我们可以通过更改 `rational` 函数的实现来弥补这个缺陷。如果有用来计算两整数的**最大公约数（greatest common denominator）**的函数，在构建组合之前，我们就可以用该函数来将分子和分母化为最简式。与许多有用的工具一样，Python 库中已经存在这样的函数。

```python
>>> from fractions import gcd
>>> def rational(n, d):
        g = gcd(n, d)
        return (n//g, d//g)
```

地板除法运算符 `//` 表示整数除法，作用为舍入除法结果的小数部分，只保留整数部分。因为我们知道 `g` 能整除 `n` 和 `d`，在这种情况下整数除法是准确的。这个修改了的 `rational` 函数实现保证了有理数的表达式为最简式。

```python
>>> print_rational(add_rationals(third, third))
2 / 3
```

这种改进是通过更改构造函数而不更改任何实现实际算术运算的函数来实现的。

#### 抽象层级（Abstraction Barriers）

在举更多的复合数据和数据抽象的例子之前，思考下在这个有理数例子中提出的一些问题。我们使用**构造函数（constructor）** `rational` 和**选择器（selectors）** `numer` ， `denom` 。一般来说，数据抽象的基本思想是确定一组基本操作，根据这些基本操作，所有对某种类型的值的操作都能被表示出来了，然后在操作数据时只使用这些操作。通过以这种方式限制操作的使用，因此在需要改变抽象数据的表示，而不改变程序的行为的时候就容易得多。

对于有理数，程序的不同部分使用不同的操作方式来操作有理数，如下表所示。

| 用来……的程序部分             | 把有理数当作……     | 只使用……                                                     |
| ---------------------------- | ------------------ | ------------------------------------------------------------ |
| 使用有理数进行计算           | 整个数据值         | `add_rational, mul_rational, rationals_are_equal, print_rational` |
| 创建有理数或实现有理数运算   | 分子和分母         | `rational, numer, denom`                                     |
| 实现有理数的选择函数和构造器 | 包含两个元素的列表 | 列表字面量和元素选择                                         |

在上面的每一层中，最后一列中的函数加强了**抽象层级（abstraction barriers）**。这些函数由较高的抽象级别调用，并使用较低的抽象级别实现。

> 翻译说明：最经典抽象层级的是网络协议了，网络协议的每一层和每一层都是分离的。每一层都是为上层提供服务，消费下层服务。这样，就可以轻轻松松换掉一层实现，而不影响功能。
>
> *选自：https://ruby-china.org/topics/33214*

每当程序的一部分可以使用更高级别的函数而不是使用较低级别的函数时，就违反了抽象层级（的规定）（abstraction barrier violation）。例如，计算有理数平方的函数最好使用函数 `mul_rational` 来实现，该函数没有假设任何关于有理数的实现方式。

```python
>>> def square_rational(x):
        return mul_rational(x, x)
```

直接引用分子和分母将违反抽象层及的规定。

```python
>>> def square_rational_violating_once(x):
        return rational(numer(x) * numer(x), denom(x) * denom(x))
```

假设有理数表示为二元素列表将违反两个抽象层级的规定。

```python
>>> def square_rational_violating_twice(x):
        return [x[0] * x[0], x[1] * x[1]]
```

抽象层级让程序更容易维护和修改。依赖于特定表示的函数越少，在想要更改该表示时所需的更改就越少。.`square_rational` 的所有这些实现都具有正确的行为，但只有第一个实现对未来的更改是健壮的。即使我们改变了有理数的表示，`square_rational` 函数也不需要更新。相比之下，只要选择器或构造函数签名发生变化，`square_rational_violating_once` 就需要更改。并且，`square_rational_violating_twice` 需要在有理数的实现发生变化时就进行更新。

#### 数据的属性（The Properties of Data）

抽象层级塑造了我们思考数据的方式。有理数的有效表示不会限于任何特定的实现（如一个包含两个元素的列表）；它是一个由 `rational` 函数返回的值，（而`rational` ）可以传递给 `number` 和 `denom`。此外，构造函数和选择器之间必须保持适当的关系。也就是说，如果我们从整数 `n` 和 `d` 构造一个有理数 `x` ，那么 `numer(x)/denom(x)` 应该等于 `n/d` 。

一般来说，我们可以使用一组选择器和构造函数，外加一些行为条件来表达抽象数据。只要满足行为条件（如上面的除法属性），选择器和构造函数就构成了一种数据的有效表示。抽象层级之下的实现细节可能会改变，但如果行为没有改变，则数据抽象仍然有效，使用该数据抽象编写的任何程序也是正确的。

这种观点可以被广泛应用，包括我们用来实现有理数的**数值对/组合（pair）**。实际上，我们从未过多地讨论数值对/组合是什么，只是说这种语言提供了创建和操作包含两个元素的列表的方法。实现数值对/组合所需的行为是将两个值“粘”在一起。作为一种行为条件，

- 如果一个数值对/组合 `p` 是由`x` 和 `y` 构造的，那么选择`(p, 0)` 返回 `x` ，选择 `(p, 1)` 返回`y` 。

我们实际上并不需要使用列表类型来创建数值对/组合。相反，我们可以实现两个函数 `pair` 和 `select` ，功能和包含两个元素的列表相同，从而来满足上述描述的需求。

```python
>>> def pair(x, y):
        """Return a function that represents a pair."""
        def get(index):
            if index == 0:
                return x
            elif index == 1:
                return y
        return get
>>> def select(p, i):
        """Return the element at index i of pair p."""
        return p(i)
```

有了上述的函数实现，我们就可以常见和操作数值对/组合了。

```python
>>> p = pair(20, 14)
>>> select(p, 0)
20
>>> select(p, 1)
14
```

这种高阶函数的使用与我们对数据应该是什么样子的直观概念完全不同。然而，这些函数足以在我们的程序中表示组合。函数足以表示复合数据。

展示组合的函数表示的意义，并不在于 Python 实际上是这样运行的（出于效率的考虑，列表的实现更为直接），而是它可以这样运行。这种函数表示法虽然模糊，但它是完全适当的表示组合的一种方法，因为它满足了组合需要满足的唯一条件。数据抽象的实践允许我们在表示方法之间轻松进行切换。

## Lecture 10 : Container & Lecture 12 : Trees

### 序列（Sequences）

**序列（sequence）**是值的有序集合。序列是计算机科学中强大的基本抽象。序列不是特定内置类型或抽象数据表示的实例，而是在几个不同类型的数据之间共享的行为集合。也就是说，序列有很多种，但它们都有共同的行为。尤其是，

- **长度**。序列的长度是有限的。空序列的长度为 0。

- **元素选择**。序列有一个元素，对应于小于其长度的任何非负整数索引，第一个元素从0开始。

Python 有好几种原生数据类型，它们都是序列，其中最重要的一种是**列表（list）**。

#### 列表（Lists）

列表的值是可以具有任意长度的序列。列表有大量的内置行为，以及用于表达这些行为的特定语法。我们已见过了计算为列表实例的列表字面量，以及计算为列表中的值的元素选择表达式。内置的 `len` 函数可以返回列表的长度。如下，`digits` 是一个包含四个元素的列表。索引 3 处的元素是 8。

```python
>>> digits = [1, 8, 2, 8]
>>> len(digits)
4
>>> digits[3]
8
```

此外，列表也可以进行相加，还可与整数相乘。对于序列，其加法和乘法不会对元素添加或相乘，而是组合和复制序列本身。也就是说，`operator` 模块（和 `+` 操作符）中的 `add` 函数生成一个列表，该列表是所添加参数的拼接。`operator` （和 `*` 操作符）中的 `mul` 函数可以接受一个列表和一个整数 `k`，以返回由原始列表的 `k` 次重复组成的列表。

```python
>>> [2, 7] + digits * 2
[2, 7, 1, 8, 2, 8, 1, 8, 2, 8]
```

任何值都可以包含在一个列表中，包括另一个列表。元素选择可以多次应用，以便在包含列表的列表中选择深度嵌套的元素。

```python
>>> pairs = [[10, 20], [30, 40]]
>>> pairs[1]
[30, 40]
>>> pairs[1][0]
30
```

#### 序列迭代（Sequence Iteration）

在许多情况下，我们希望迭代序列的元素，并依次为每个元素执行一些计算。这种模式非常常见，因此Python有一个额外的控制语句来处理顺序数据：`for` 语句。

思考一个值在序列中出现了多少次的问题。我们可以使用 `while` 循环实现一个函数来计算这个计数。

```python
>>> def count(s, value):
        """Count the number of occurrences of value in sequence s."""
        total, index = 0, 0
        while index < len(s):
            if s[index] == value:
                total = total + 1
            index = index + 1
        return total
      
>>> count(digits, 8)
2
```

Python 的 `for` 语句可以通过直接对元素的值进行迭代来简化这个函数体，根本不需要引入名称 `index` 。

```python
>>> def count(s, value):
        """Count the number of occurrences of value in sequence s."""
        total = 0
        for elem in s:
            if elem == value:
                total = total + 1
        return total
      
>>> count(digits, 8)
2
```

`for` 语句由一个子句组成，其形式为：

```python
for <name> in <expression>:
    <suite>
```

`for` 语句的执行过程如下：

1. 对头部的 `<expression>` 求值，必定得到一个可迭代的值。
2. 对于该可迭代值中的每个元素值，按顺序：
   1. 在当前帧中，将 `<name>` 绑定给每个元素值
   2. 执行 `<suite>`

这个执行过程引用了可迭代的值。列表（list）是序列的一种类型，序列是可迭代的值。考虑它们的元素时，需要按顺序进行。 Python 包括其他可迭代类型，但我们现在将关注序列；术语“可迭代”的一般定义出现在第 4 章的迭代器部分。

此计算过程的一个重要结果是 `<name>` 将在 `for` 语句执行后绑定到序列的最后一个元素。`for` 循环引入了另一种可以通过语句更新环境的方式。

**序列解包（Sequence unpacking）**。程序中一个常见模式为：有一个元素序列，这些元素本身也是序列，但这些作为元素的序列本身都有固定的长度。`for` 语句可以在其头部中包含多个名称，以便将每个元素序列“解包”到其各自相应的元素中。例如，我们可能有一个包含两个元素列表的列表。

```python
>>> pairs = [[1, 2], [2, 2], [2, 3], [4, 4]]
```

然后求出第一和第二元素相同的组合的个数。

```python
>>> same_count = 0
```

下面头部中有两个名称的 `for` 语句将分别将每个名称 `x` 和 `y` 绑定到每个组合中的第一个和第二个元素。

```python
>>> for x, y in pairs:
        if x == y:
            same_count = same_count + 1
>>> same_count
2
```

这种将固定长度序列中的多个名称绑定到多个值的模式称为序列解包；这与我们在将多个名称绑定到多个值的赋值语句中知晓的模式相同。

**范围（Ranges）**。`range` 是Python 中另一个内置的序列类型，可以表示一个整数范围。范围可以由 `range` （关键字）创建，它接受两个整数参数：所需范围内的第一个数和最后一个数后面的一个数。

```python
>>> range(1, 10)  # Includes 1, but not 10
range(1, 10)
```

在范围上调用 `list` 的构造函数，得到的结果是与该范围具有相同元素的列表，这样就可以很容易地检查元素。

```python
>>> list(range(5, 8))
[5, 6, 7]
```

如果只给出了一个参数，它将被解释为从 0 开始到该给定参数形成的范围的最后一个值。

```python
>>> list(range(4))
[0, 1, 2, 3]
```

`range` 通常作为 `for` 头部中的表达式出现，用于指定语句组应该执行的次数：通常的约定是，如果该名称在语句组中未使用，则在 `for` 头部中为该名称使用单个下划线字符：

```python
>>> for _ in range(3):
        print('Go Bears!')

Go Bears!
Go Bears!
Go Bears!
```

对解释器来说，这个下划线只是环境中的另一个名称而已，但在程序员中具有常规意义，表示该名称不会出现在任何之后的表达式中。

#### 序列处理（Sequence Processing）

序列是复合数据的一种常见形式，以至于整个程序通常都是围绕这种单一抽象来组织的。具有序列作为输入和输出的模块化组件可以混合和匹配以执行数据处理。通过将序列处理操作的管道链接在一起，可以定义复杂的组件，每个序列处理操作都是简单和集中的。

**列表推导式（List Comprehensions）** 。许多序列处理操作都可以表达成将序列中的每一个元素与固定表达式进行计算，并将计算结果进行收集最终组成结果序列。在 Python 中，列表推导式是执行这种计算的表达式。

```python
>>> odds = [1, 3, 5, 7, 9]
>>> [x+1 for x in odds]
[2, 4, 6, 8, 10]
```

> 翻译注：该部分详细信息可以参见维基百科与runoob相关信息：
>
> - [列表推導式 - 維基百科，自由的百科全書 (wikipedia.org)](https://zh.wikipedia.org/zh-tw/列表推导式)
> - [Python 推导式 | 菜鸟教程 (runoob.com)](https://www.runoob.com/python3/python-comprehensions.html)

上面的 `for` 关键字不是 `for` 语句的一部分，而是列表推导式的一部分，因为它包含在方括号中。对子表达式 `x+1` 进行求值，其中 `x` 依次绑定到 `odds` 的每个元素，并将结果值收集到一个列表中。

另一个常见的序列处理操作是选择满足某些条件的值的子集。列表推导式也可以表达这种模式，例如，选择 `odds` 中所有能整除 25 的元素。

```python
>>> [x for x in odds if 25 % x == 0]
[1, 5]
```

列表推导式的一般形式是：

```latex
[<map expression> for <name> in <sequence expression> if <filter expression>]
```

要计算列表推导式，Python 先计算 `<sequence expression>` ，它必须返回一个可迭代值。然后，对于按顺序排列的每个可迭代值的元素，将其值绑定到 `<name>` ，计算筛选器表达式 `<filter expression>` ，如果它产生一个真值，则计算映射表达式`<map expression>`。映射表达式的值被收集到一个列表中。

**聚合（Aggregation）**。序列处理中的第三种常见模式是将序列中的所有值聚合为单个值。内置函数 `sum`、`min` 和 `max` 都是聚合函数的示例。

通过组合为每个元素计算表达式（evaluating an expression for each element）、选择元素子集（selecting a subset of elements）和聚合元素（aggregating elements）的模式，我们可以使用序列处理方法解决问题。

完美数是一个正整数，等于其除数之和。`n` 的除数是小于 `n` 的正整数，它们被 `n` 整除。用列表推导式可以表示 `n` 的除数。

```python
>>> def divisors(n):
        return [1] + [x for x in range(2, n) if n % x == 0]
    
>>> divisors(4)
[1, 2]
>>> divisors(12)
[1, 2, 3, 4, 6]
```

使用 `divisors` 函数，我们可以使用其它的列表推导式来计算从 1 到 1000 的完美数。（ 通常认为 1 也是完美数，但是它不符合我们函数 `divisors` 中完美数的定义。）

```python
>>> [n for n in range(1, 1000) if sum(divisors(n)) == n]
[6, 28, 496]
```

我们可以重新使用 `divisors` 的定义来解决另一个问题，在给定面积的情况下，求边长为整数的矩形的最小周长。矩形的面积是高乘以宽。因此，给定面积和高度，我们可以计算宽度。我们可以断言，宽度和高度都可以整除面积，以确保边长是整数。

```python
>>> def width(area, height):
        assert area % height == 0
        return area // height
```

矩形的周长是它的边长之和。

```python
>>> def perimeter(width, height):
        return 2 * width + 2 * height
```

边长为整数的矩形的高度必是其面积的除数。我们可以通过考虑所有的高度来计算最小周长。

```python
>>> def minimum_perimeter(area):
        heights = divisors(area)
        perimeters = [perimeter(width(area, h), h) for h in heights]
        return min(perimeters)
    
>>> area = 80
>>> width(area, 5)
16
>>> perimeter(16, 5)
42
>>> perimeter(10, 8)
36
>>> minimum_perimeter(area)
36
>>> [minimum_perimeter(n) for n in range(1, 10)]
[4, 6, 8, 8, 12, 10, 16, 12, 12]
```

**高阶函数（Higher-Order Functions）**。
