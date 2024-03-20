---
{"dg-publish":true,"permalink":"/语言卡片盒/TLAPlus/TLAPlus Pluscal/","tags":["TLAplus"]}
---



## Q1：Pluscal代码的基本格式是怎样的？

一个标准的Pluscal代码由以下几部分组成。

```tla+
---- (1) MODULE wire ----
EXTENDS Integers \* (2)
(*--algorithm wire \* (3)
    variables (4)
        people = {"alice", "bob"},
        acc = [alice |-> 5, bob |-> 5];
begin \* (5)
    skip;
end algorithm;*) (3)
==== \* (1)
```

可以看到，一个标准的 Pluscal 代码由上述五部分组成。

### （1）首尾标识和模型名

```tla+
---- MODULE name ----
contents of pluscal
====
```

### （2）导入的包名

```tla+
EXTENDS Package-names
```

常用的包有：Integers, ...

### （3）算法的起止声明，用注释+声明形式
```tla+
(*--algorithm name
...
end algorithm;*)
```

### （4）变量声明
```tla+
variables
	var_1 = value_1,
	var_2 = value_2,
	...
	var_n = value_n;
```

### （5）算法声明

```tla+
begin
	...;
```

## Q2：能不能在TLA+中测试语句？

tla+中的表达要么是 **值**（value），要么是 **操作符**（operator）。

在 `Module Checking Results` 栏目下可以看到 `Evaluate Constant Expression` 选项。在Expression 文本框中输入 tla+ 表达式，按 `F11` 即可在 Value 窗口查看语句执行结果。

![Pasted image 20240318120229.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240318120229.png)

## Q3：tla+ 的基本操作符（operators）有哪些？

### （1）基础操作符

直接看表好了：

| 表达式      | 意义             | 例子                         |
| -------- | -------------- | -------------------------- |
| `x = y`  | 等于（Equal）      | `1 = 3` => `FALSE`         |
| `x /= y` | 不等于（Not Equal） | `1 /= 3` => `TRUE`         |
| `x # y`  | 不等于（Not Equal） |                            |
| `x /\ y` | 且（and）         | `TRUE /\ FALSE` => `FALSE` |
| `x \/ y` | 或（or）          | `TRUE \/ FALSE` => `TRUE`  |
| `x := y` | 设置（Assignment） | only in pluscal            |

> [!faq] `=` 和 `:=` 的区别是什么？

在Pluscal 中，`x = y` 如果不是第一次声明x，意味着相等判断。如果是第一次声明x，则意味着初始化x的值。`x := y` 则意味着重新给 x 设定值。

而在tla+中，`x = y` 的功能和Pluscal相同。但是重新赋值选项则以 `x' = new_vlue` 方式进行。

### （2）集合

除了整数（Integers）之外，tla+还包括集合（set）、元组（tuples/sequences）、结构体（structures）和函数（functions）。

> [!tip] ==集合==的所有的元素都必须有相同的类型。
> 例如 `A = {1, 2, 3}` 是合法的，因为所有元素都是整数。
> 但是 `A = {1, 2, "3"}` 则是非法的，因为集合 A 中同时包含了整数和字符串两种数据类型。

#### 集合操作符

那么在tla+中集合的基本操作符有那些呢？

来看看表格吧：

| 表达式                    | 意义             | 例子                                  | 备注                 |
| ---------------------- | -------------- | ----------------------------------- | ------------------ |
| `x \in set`            | x 是集合set的元素    | `1 \in 1..3` => `TRUE`              |                    |
| `x \notin set`         | x不是集合set的元素    | `1 \notin 1..3` => `FALSE`          |                    |
| `set1 \subsetq set2`   | set1是集合set2的子集 | `{1, 3} \subsetq 1..3` => `TRUE`    |                    |
| `set1 \union set2`     | 求set1和set2的并集  | `{1, 3} \union {2}` => `{1, 2, 3}`  | 其它表达：`\cup`        |
| `set1 \intersect set2` | 求set1和set2的交集  | `{1, 3} \intersect {1, 4}` => `{1}` | 其它表达：`\cap`        |
| `set1 \ set2`          | 求set1和set2的差集  | `{1, 3} \ {3}` => `{1}`             |                    |
| `Cardinality(set)`     | 求set的长度        | `Cardinality({1..8)` => 8           | 需要导入包 `FiniteSets` |

还有两种集合的遍历和映射表达方式。

#### 集合过滤（filter）方式


集合过滤的基本表达方式为：`{x \in set: conditional}` ，意义是根据条件筛选集合中的元素。

比如说：

`{x \in 1..8: x + 2 > 3}` => `{2, 3, 4, 5, 6, 7, 8}`

> [!faq] 不过为什么 `1..8` 加上括号就会非法呢？

`{x \in {1..8}: x + 2 > 3}` => wrong

> [!bug] The `Evaluate Constant Expression� section�s evaluation failed.
> 
> Attempted to apply the operator overridden by the Java method
> public static tlc2.value.IntValue
> tlc2.module.Integers.Plus(tlc2.value.IntValue,tlc2.value.IntValue), but it produced the following error:
> argument type mismatch

类型不匹配......java写的。

如果在外面赋值的时候呢？

`1 \in {1..3}` 不会返回 `TRUE` 。

换言之 `1..3` 本身就是一个集合。

验证一下吧：

`Cardinality({1..3})` => 1

`Cardinality(1..3)` => 3

果然如此。

所以实际上 `{x \in {1..8}: ...}` 中 x的遍历实际上会遍历到 `{1, 2, 3, 4, 5, 6, 7, 8}` 这个集合本身。

而在后面的 `x + 2 > 3` 这个判断中， `>` 需要两个参数为整数，第一个为集合，所以出现类型错误。

好吧。

#### 集合映射（map）方式

集合映射的表达方式是：`{expression: x \in set}`，意义是对集合中每个元素进行指定动作的处理。

测试一下：

`{x + 1 : x \in 1..3}` => `{4, 5, 6}`

#### 对比rif和tla+的集合操作

> [!faq] 那么集合过滤与集合映射与rif的区别何在呢？

之前我曾经编写过rif函数，大概格式是：

```lisp
(rlb-rif '(1, 2, 3) '()
		 (rlb-fip (r f) (> (car r) 2))
		 (rlb-ted (r f) (+ 1 (car r))))
;;=>'(1)
```

tla+中的过滤和映射在我这里则是fip和ted。

不过我还能找到之前的代码吗？


找到了：

```lisp
;;动作执行局部函数
  (defvar rlb-tedo 0)
  (rlb-solimi rlb-ted (hisos &rest body)
              `(progn
                 (setf rlb-tedo (+ 1 rlb-tedo))
                 (setf rlb-rinafo (+ 1 rlb-rinafo))
                 (lambda ,hisos ,@body)))

(rlb-solimi 态势 (hisos &rest body)
            `(rlb-ted ,hisos ,@body))

;;条件判断局部函数
  (defvar rlb-fipo 0)
  (rlb-solimi rlb-fip (hisos &rest body)
              `(progn
                 (setf rlb-fcipo (+ 1 rlb-fipo))
                 (setf rlb-rinafo (+ 1 rlb-rinafo))
                 (lambda ,hisos ,@body)))

(rlb-solimi 情境 (hisos &rest body)
            `(rlb-fip ,hisos ,@body))

;;自此而彼按照指定条件和动作处理列表
  (defvar rlb-rifo 0)
  (defun rlb-rifa_loyo (rino famo fipo tedo)
    (if (equal nil (equal (car rino) nil))
        (rlb-rif (cdr rino)
                 (if (funcall fipo rino famo)
                     (append
                      (list (funcall tedo rino famo)) famo)
                     famo)
                 fipo tedo)
        (progn
          (setf rlb-rifo (+ 1 rlb-rifo))
          (setf rlb-rinafo (+ 1 rlb-rinafo))
          famo)))
  (rlb-solimi rlb-rif (rino famo fipo tedo)
    `(rlb-rifa_loyo ,rino ,famo ,fipo ,tedo))

(rlb-solimi 迈进 (rino famo fipo tedo)
            `(rlb-rif ,rino ,famo ,fipo ,tedo))

```

那么tla+能不能把遍历和过滤结合在一起呢？

`{e + 3 : e \in {e \in 1..9 : e > 3}}` => `{7, 8, 9, 10, 11, 12}`

同样的功能，在rlisp中则要表达为：

```lisp
(rlb-rif '(1, 2, 3, 4, 5, 6, 7, 8, 9) '()
		 (rlb-fip (r f) (> (car r) 3))
		 (rlb-ted (r f) (+ (car r) 3)))
;;=>'(7, 8, 9, 10, 11, 12)
```

不过最大的区别也许在于，tla+中集合要求所有元素都是相同的类型。但是，rlisp中的元素则是任意的。

### （3）元组

#### 元组表达式

tla+中的元组表达形式为：`<<element_1, element_2, ...>>`。

和集合不同，tla+的元组不需要所有元素都为相同的类型。

可以通过 `元组名[位置]` 的方式来访问元组中的元素。例如：


先进行简单测试来看看吧。

`<<1, 2, 1..3>>[3]` => `1..3`
`<<1, {1, 3}, 2>>[2]` => `{1, 3}` 

然后在模型中测试：

```tla+

(*--algorithm t2
    variables
        a = <<1, 2, {1, 4}>>;
        
define
    Famo == <>(a = {1, 4})
end define;

begin
    test:
        a := a[3];
end algorithm;*)   

```

> [!bug] 类型匹配错误！
> Attempted to check equality of the function <<1, 2, {1, 4}>> with the value:
{1, 4}
While working on the initial state:
/\ a = <<1, 2, {1, 4}>>
/\ pc = "test"

~~也就是说，在test执行前，a仍为元组时，对于时间属性Famo的检测要求元组和元素通过 `=` 相匹配，结果报错。~~ 因为 `<>P` 这种表达要求时间属性首尾状态为真，所以a一开始赋值为 `<<1, 2, {1, 4}>>` 注定了它的错误。

另一种检测方式：

```tla+
(*--algorithm t2
    variables
        a = <<1, 2, 3>>;
        b = 3;
        
define
    Famo == <>(b = 3)
end define;

begin
    p1: b := a[1];
    p2: b := a[2];
    p3: b := a[3];
end algorithm;*) 
```

#### 元组操作符

tla+ 提供了 `Sequences` 扩展包，包括以下操作符：

| 操作符              | 意义            | 例子  |
| ---------------- | ------------- | --- |
| `Head(sequence)` | 获取序列的第一个元素    |     |
| `Tail(sequence)` |               |     |
| `Append(seq, x)` |               |     |
| `seq1 \o seq2`   | 将序列1和序列2结合在一起 |     |
| `Len(seq)`       | 获取序列长度        |     |
### （4）结构体（Structs）

在tla中，结构体的表达方式为：`[key_1 |-> val_1, key_2 |-> val_2, ...]`

结构体中的元素可以通过 `结构体名称.键名` 进行访问。

例如：

```tla+
(*--algorithm t2
    variables
        Alice = [name |-> "Alice", age |-> 23];
        See = 0;
        
define
    Famo == <>(Alice.name = "Alice")
end define;

begin
    p1: See := Alice.name;
    p2: See := Alice.age;
    p3: See := Alice.name;
end algorithm;*)  
```

测试通过，Alice的名字自始至终是"Alice"。


## Q3：算法主体

### （1）assignment

Pluscal 通过 `x := value` 表达来为变量赋新值。 

### （2）assert

使用assert语句需要导入 `TLC` 扩展包。

assert可以通过假定某个变量的值来判断系统状态，用于begin之后的部分。例如我认为在p2状态Alice的名字是bob，结果出错：

```tla+

EXTENDS Integers, FiniteSets, TLC

(*--algorithm t2
    variables
        Alice = [name |-> "Alice", age |-> 23];
        See = 0;
        
define
    Famo == <>(Alice.name = "Alice")
end define;

begin
    p1: See := Alice.name;
    p2: See := Alice.age;
    assert Alice.name = "bob";
    p3: See := Alice.name;
end algorithm;*)   
```

![Pasted image 20240318154452.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240318154452.png)


### （3）skip

在函数主体中，skip表示什么都不做。例如：

```tla+

EXTENDS Integers, FiniteSets, TLC

(*--algorithm t2
    variables
        Alice = [name |-> "Alice", age |-> 23];
        
define
    Famo == <>(Alice.name = "Alice")
end define;

begin
    AfterOneYear: Alice.age := Alice.age + 1;
    AfterThreeYear: Alice.age := Alice.age + 3;
    AfterTenYear: Alice.age := Alice.age + 10;
    assert Alice.age = 27;
end algorithm;*)   
```

![Pasted image 20240318155412.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240318155412.png)

在经过十年时，Alice的年龄由于+10而成为37，不同于假定。但是如果在经过十年时使用skip的话——却可以通过测试。

### （4）if

基本格式：

```tla+
if condition1 then
  body
elsif condition2 then
  body
else
  body
end if;
```

### （5）while

基本格式：

```tla+
while condition do
  body
end while;
```

### （6）macros

tla+的macro感觉很像函数。需要注意的是，macro的定义在define之后。

```tla+
macro name(arg1, arg2) begin
  \* assignments
end macro;
begin
  name(x, y);
end algorithm;
```

那么可不可以通过 macro 来定义fovis呢？

先定义 IMMP（IsMiaMoviaPimo，此集处在维系位图中）吧：

```tla+
(*--algorithm t2
    variables
        Moko = [mio |-> {1, 2, 3, 8}, vio |-> {1, 2}, ilio |-> {1, 2, 3, 4, 8}];
        Judgement = FALSE

define
    Famo == <>(Judgement = FALSE)
end define;

macro IMMP(Moko) begin
    Judgement := \A m \in Moko.mio : m \in Moko.ilio
end macro;

begin
P1: IMMP(Moko);
end algorithm;*)  
```

`Moko.mio`中的元素都在 `Moko.ilio` 中，IMMP应为假，测试通过。接下来换几组数据试试看吧。

| Moko.mio       | Moko.ilio      | Judgement | 解释          |
| -------------- | -------------- | --------- | ----------- |
| `{1, 2, 3, 8}` | `{1, 2, 3, 4}` | `FALSE`   | 此集的8不在域中    |
| `{1, 2, 3}`    | `{1, 2, 3, 4}` | `TRUE`    | 此集的所有元素都在域中 |
由此得到 IMMP的宏定义：

```tla+
macro IMMP(Moko) begin
    Judgement := \A m \in Moko.mio : m \in Moko.ilio
end macro;
```

那么同样可以得到IVMP的宏定义：

```tla+
macro IVMP(Moko) begin
    Judgement := \A m \in Moko.vio : m \in Moko.ilio
end macro;
```

现在可以得到IFVP的宏定义：

```tla+
macro IFVP(Moko) begin
    Judgement := IMMP(Moko) /\ IVMP(Moko)
end macro;
```

不对，这样会出现错误，因为IMMP和IVMP都会修改Judgement的值。

为什么不能让宏命令本身返回值呢？

不能，Plucal的宏命令只能是 `:=` 的形式。

那么为什么不把IVMP之类的语句放在定义里呢？

```tla+
EXTENDS Integers, FiniteSets, TLC

(*--algorithm t2
    variables
        Mok = [mio |-> {1, 2, 3, 8}, vio |-> {1, 2}, ilio |-> {1, 2, 3, 4}],
        Judgement = TRUE;

define
    IMMP(Moko) == \A m \in Moko.mio : m \in Moko.ilio
    IVMP(Moko) == \A m \in Moko.vio : m \in Moko.ilio
end define;

begin
P1: Judgement := IMMP(Mok);

end algorithm;*)   
```

不变量设置为Judgement，测试数据如下：

| Moko.mio       | Moko.ilio      | Judgement | 解释          |
| -------------- | -------------- | --------- | ----------- |
| `{1, 2, 3, 8}` | `{1, 2, 3, 4}` | `FALSE`   | 此集的8不在域中    |
| `{1, 2, 3}`    | `{1, 2, 3, 4}` | `TRUE`    | 此集的所有元素都在域中 |

这样定义要比集合差之类的简单多了。

## Q4：多种开始状态

代码格式：

```tla+
variables x in set
```

## Q5：With 和 Either

Either 语法格式

```tla+
either
  \* branch 1
or
  \* branch 2
  \* ...
or
  \* branch n
end either;
```

With 语法格式

```tla+
with var = value do
  \* body
end with;
\* or
with var \in set do
  \* body
end with;
```



## 参考
- Pratical TLA+ 

