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

-  `=>` 蕴含

-  `<=>` 与或




### Q3：TLA+的表达式

#### LET IN

```tla+
RotateRight(seq) ==
  LET
    last == seq[Len(seq)]
    first == SubSeq(seq, 1, Len(seq) - 1)
  IN <<last>> \o first
>> RotateRight(<<1, 2, 3>>)
<<3, 1, 2>>
```

#### IF THEN ELSE

`IF Condition THEN Exp1 ELSE Exp2`


#### CHOOSE


A case statement. Subsequent cases are marked by a `[]`.

```tla+
CASE x = 1 -> TRUE
  [] x = 2 -> TRUE
  [] x = 3 -> 7
  [] OTHER -> FALSE
```

CHOOSE x \in S : P(x) is “select an x such that P(x) is true.”

## TLA+ 的函数

### DOMAIN

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



### @@

