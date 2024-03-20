---
{"dg-publish":true,"permalink":"/语言卡片盒/TLAPlus/TLAPlus 操作符和函数/","tags":["TLAplus"]}
---



## TLA+ 的变量
### Q1：TLA+ 有什么自定义操作符？

- 我比较感兴趣的是用户自定义操作符：

```tla+
set ++ elem == set \union {elem}
set -- elem == set \ {elem}
>> {1, 2} ++ 3
{1, 2, 3}
>> {1, 2} – 2
{1}
```

现在可不可以吧fovis之类的定义了呢？

```tla+
mio \ifvp vio \da ilio == IFVP(mio, vio, ilio) 
```

好像不可以。

```tla+
mio  vio -da ilio == IFVP(mio, vio, ilio) 
```

麻烦了。只能定义两元运算符。

现在定义一下元素的流动吧：

一个元素从fovis 到 fogim怎么处理？

现有的表达方式是： `mio \ elem; vio \ elem; ilio \union elem;`

而我期待更加简洁的表达：`elem rfvffgd moko`

![Pasted image 20240319111028.png](/img/user/Pasted%20image%2020240319111028.png)

整理一个表格吧：

![Pasted image 20240319111253.png](/img/user/Pasted%20image%2020240319111253.png)


| ASCii     | 图像        | 意义     | ASCii | 符号          | 意义  | ASCII | 符号         | 意义  |
| --------- | --------- | ------ | ----- | ----------- | --- | ----- | ---------- | --- |
| `++`      | $++$      | fofos  |       | $\sqcap$    |     |       | $\sim$     |     |
| `--`      | $--$      | focos  |       | $\sqcup$    |     |       | $\simeq$   |     |
| `**`      | $**$      |        |       | $\odot$     |     |       | $\approx$  |     |
| `//`      |           |        |       | $\prec$     |     |       | $\cong$    |     |
| `^^`      |           |        |       | $\preceq$   |     |       | $\doteq$   |     |
| `&&`      |           |        |       | $\succ$     |     |       | $\asymp$   |     |
| `%%`      |           |        |       | $\succeq$   |     |       | $\bigcirc$ |     |
| `@@`      |           |        |       | $\ll$       |     |       | $\propto$  |     |
| `##`      |           |        |       | $\gg$       |     |       | $\wr$      |     |
| `$$`      |           |        |       | $\sqsubset$ |     |       | $\uplus$   |     |
| `!!`      |           |        |       | $\sqsupset$ |     |       |            |     |
| `??`      |           | 判断维解状态 |       | $\subset$   |     |       |            |     |
| `$`       |           |        |       | $\supset$   |     |       |            |     |
| `\bullet` | $\bullet$ |        |       | $\|-$       |     |       |            |     |
| `\star`   | $\star$   |        |       | $-\|$       |     |       |            |     |
| `\|`      |           |        |       | $=\|$       |     |       |            |     |
| `&`       |           |        |       | $\|=$       |     |       |            |     |


如果考虑到维解总要在有意义的某个集合上进行计算。那么实际上，Mio就应该具有两个集合。

$oso = [rio \mapsto \{1, 2, 5\}, fao \mapsto \{1, 2 ,3, 4\}]$

当两个集合相互作用的时候就会产生维解。

比如说：

$oso_a = [rio \mapsto \{1, 2, 5\}, fao \mapsto \{1, 2 ,3, 4\}]$
$oso_b = [rio \mapsto \{1, 2, 6\}, fao \mapsto \{1, 2 ,3, 4\}]$

在 fao相同的意义上，a和b可以进行维解。

$Mok(oso_a, oso_b) \triangleq [mio \mapsto \{1, 2, 5\}, vio \mapsto \{1, 2, 6\}, ilio \mapsto \{1, 2, 3, 4\}]$

那么我怎么规定维解的意义呢？

判断是否为某种状态？

选取某种区域的元素？

还是说在进入集合之前先从逻辑上判断？

集合和逻辑，感觉好麻烦。

```tla+

define
    mio ++ vio == mio <=> vio
    mio -- vio == ~mio <=> vio
end define;
```

这样很省事，但是我需要的是集合运算。


```tla+
   \* symbol define
   DMM(Mio, Vio) == IF Mio.fao = Vio.fao THEN TRUE ELSE FALSE 
   M ++ V == /\ DMM(M, V)
             /\ IFFP([mio |-> M.rio, vio |-> V.rio, ilio |-> M.fao])
   M -- V == /\ DMM(M, V)
             /\ IFCP([mio |-> M.rio, vio |-> V.rio, ilio |-> M.fao])
             
   RIFO == [](a ++ b)
```

### Q2：TLA+的逻辑操作符

 - `\A` 全部

-  `\E` 存在

-  `=>` 逻辑判断，蕴含

-  `<=>` 逻辑判断，与或




### Q3：TLA+的表达式有哪些？

 **LET IN**

```tla+
RotateRight(seq) ==
  LET
    last == seq[Len(seq)]
    first == SubSeq(seq, 1, Len(seq) - 1)
  IN <<last>> \o first
>> RotateRight(<<1, 2, 3>>)
<<3, 1, 2>>
```

**IF THEN ELSE**

`IF Condition THEN Exp1 ELSE Exp2`


**CHOOSE**


A case statement. Subsequent cases are marked by a `[]`.

```tla+
CASE x = 1 -> TRUE
  [] x = 2 -> TRUE
  [] x = 3 -> 7
  [] OTHER -> FALSE
```

CHOOSE x \in S : P(x) is “select an x such that P(x) is true.”

## TLA+ 的函数

### Q1：DOMAIN——函数的域

DOMAIN，给予一个函数可能的输入

> If `func == [x \in set |-> ...]` , then `DOMAIN func = set`.

测试一下吧。

~~在tla+中函数输入一般用 `[]` 方括号括起来。那么圆括号 `()` 表示什么呢？~~

在规范中定义函数 foo。

```tla+
\* in define
foo(S) == [x \in S |-> x + 1]
```

然后在检查中测试：

`foo(1..3)` => `<<2, 3, 4>>`

如果在foo前加上 `DOMAIN` 呢？

`DOMAIN {1, 2, 3}` => `{1, 2, 3}`

另外有一个问题：

> [!faq] 为什么以下形式在tla+中是非法表达？
> `foo[1..3]`


我想是因为我的定义是：`foo(S) == [x \in S |-> x + 1]`

如果修改为：`foo[x \in 1..3] == "ok"`

进行测试：`foo[1]` => `"ok"`，`DOMAIN foo` => `{1, 2, 3}`

是可以通过的。

> 那么可以把 `foo[x \in 1..3]` 替换成 `foo(x \in 1..3)` 吗？

不能，定义`foo(x \in 1..3) == "ok"` 会出错：
![Pasted image 20240320091503.png](/img/user/Pasted%20image%2020240320091503.png)

很显然圆括号的定义不是函数定义，那么圆括号定义了什么呢？——**操作符**

> [!faq] 那么函数可以缺少域的定义吗？

不能，定义 `foo[x] == "ok"` 会出错：

![Pasted image 20240320091919.png](/img/user/Pasted%20image%2020240320091919.png)



### Q2：@@——函数的分支

需要导入 TLC 扩展。

`f @@ g` 相当于 `Merge(f, g)`，功能是测试域的所有情况。

首先是在函数 `f` 的域中的元素，根据函数 `f` 的映射规则得到值域。

其次是在函数 `g` 的域中的元素，根据函数 `g` 的映射规则得到值域。

> [!faq] 那如果函数f 和函数 g 的值域重合了怎么办？

其中，`Merge` 的定义如下：

```tla+
Merge(f, g) == [
  x \in (DOMAIN f) \union (DOMAIN g) |->
    IF x \in DOMAIN f THEN f[x] ELSE g[x]
  ]
```

很显然，函数`f`的判断是更加优先的。一个元素首先会在 `f` 的域中进行判断，然后才是 `g` 。

测试：

```tla+
f[x \in 1..5] == "m"
g[x \in 2..9] == "k"
f @@ g
```

=> `<<"m", "m", "m", "m", "m", "k", "k", "k", "k">>`

可以看到，函数 `f` 和函数 `g` 存在域的交集 `2..5` ，此时域中元素依据 `f` 的规则映射到值域中。
### Q3：:>——无名函数

```tla+
>> (2 :> 3)[2]
3
>> ("a" :> "b").a
"b"
```

看起来像极了 lambda：

```lisp
(funcall (lambda (x) (if (= 2 2) 3)) 2)
```


### Q4：函数的集合

`|->` 用于函数/结构体内的映射，`->` 用于函数集的映射，看起来会产生笛卡尔积。

```tla+
>> [s \in {"a", "b"} |-> {1, 2}]
[a |-> {1, 2}, b |-> {1, 2}]
>> [{"a", "b"} -> {1, 2}]
{ [a |-> 1, b |-> 1],
     [a |-> 1, b |-> 2],
     [a |-> 2, b |-> 1],
     [a |-> 2, b |-> 2] }
```

暂时不知道应该怎么用。

测试一下吧：

```tla+
[{"siv", "kiv", "kig"} -> {"m", "o", "k"}]
```

=>

```tla+
{ [siv |-> "m", kiv |-> "m", kig |-> "m"],
     [siv |-> "m", kiv |-> "m", kig |-> "o"],
     [siv |-> "m", kiv |-> "m", kig |-> "k"],
     [siv |-> "m", kiv |-> "o", kig |-> "m"],
     [siv |-> "m", kiv |-> "o", kig |-> "o"],
     [siv |-> "m", kiv |-> "o", kig |-> "k"],
     [siv |-> "m", kiv |-> "k", kig |-> "m"],
     [siv |-> "m", kiv |-> "k", kig |-> "o"],
     [siv |-> "m", kiv |-> "k", kig |-> "k"],
     [siv |-> "o", kiv |-> "m", kig |-> "m"],
     [siv |-> "o", kiv |-> "m", kig |-> "o"],
     [siv |-> "o", kiv |-> "m", kig |-> "k"],
     [siv |-> "o", kiv |-> "o", kig |-> "m"],
     [siv |-> "o", kiv |-> "o", kig |-> "o"],
     [siv |-> "o", kiv |-> "o", kig |-> "k"],
     [siv |-> "o", kiv |-> "k", kig |-> "m"],
     [siv |-> "o", kiv |-> "k", kig |-> "o"],
     [siv |-> "o", kiv |-> "k", kig |-> "k"],
     [siv |-> "k", kiv |-> "m", kig |-> "m"],
     [siv |-> "k", kiv |-> "m", kig |-> "o"],
     [siv |-> "k", kiv |-> "m", kig |-> "k"],
     [siv |-> "k", kiv |-> "o", kig |-> "m"],
     [siv |-> "k", kiv |-> "o", kig |-> "o"],
     [siv |-> "k", kiv |-> "o", kig |-> "k"],
     [siv |-> "k", kiv |-> "k", kig |-> "m"],
     [siv |-> "k", kiv |-> "k", kig |-> "o"],
     [siv |-> "k", kiv |-> "k", kig |-> "k"] }
```

看起来真的很方便，直接帮助我罗列了所有可能。




## 总结

在本篇笔记中，我记录了自己了解 TLA+ 基本操作符，表达式和函数的过程。

自定义操作符虽然很有意思，不过在基础学习的阶段，还是了解好 `\A` `\E` `f[x \in set]` 这些基础定义的特性要好一点。

