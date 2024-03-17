---
{"dg-publish":true,"permalink":"/数字花园/TLAPlus/TLAPlus 练习，电报问题/"}
---



现在请你设计一个电报交换系统，包括以下几个条件：

- 每一个电报只能在银行中两个不同的人间进行传递，而且每个电报至少一美元。
- 如果电报是成功的，那么电报的值便会从发送者方减去，而加在接收方的账户中。
- 如果电报失败，两个账户将没有任何改变。
- 电报不能让任何一个账户是负值。
- 多个电报可能同时发生。


**单进程建模**

```PlusCal
EXTENDS Integers

(*--algorithm wire
variables
    people = {"Zhang", "Wang"},
    acc = [p \in people |-> 5],
    sender = "Zhang",
    reciver = "Wang",
    amount \in 1..4;
        
define
        NoOverdrafts == \A p \in people: acc[p] >= 0
end define;

begin
    Withdraw:
        acc[sender] := acc[sender] - amount;
    Deposite:
        acc[reciver] := acc[reciver] + amount;
end algorithm;**)
```


其中，如果修改 amount 的值为 范围 1..6，则会报错。

![Pasted image 20240317110004.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317110004.png)

因为小张和小王的初始金额都是5，如果一次电报发送6美元，无论谁发都会出现负值。

不过有意思的是错误数量。

分别试试把 `amount` 设为 1..7，1..9看看吧。

- `amount \in 1..6`
	- ![Pasted image 20240317111028.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317111028.png)
- `amount \in 1..7`
	- ![Pasted image 20240317111053.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317111053.png)
- `amount \ in 1..9`
	- ![Pasted image 20240317111114.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317111114.png)
可以看到的是，错误检测始终只报出一个错误，似乎是检测到错误就停止了。但是可区分的状态却有所不同。

> [!faq] Diameter，States Found，Distinct State，Queue Size分别是什么？有什么方法可以在无错状态下查看执行路径？


**多进程处理**

```PlusCal
EXTENDS Integers

(*--algorithm wire
variables
    people = {"Zhang", "Wang"},
    acc = [p \in people |-> 5];
        
define
        NoOverdrafts == \A p \in people: acc[p] >= 0
end define;

process Wire \in 1..2
    variables
        sender = "Zhang",
        reciver = "Wang",
        amount \in 1..acc[sender];

begin
    CheckFonds:
        if amount <= acc[sender] then
            Withdraw:
                acc[sender] := acc[sender] - amount;
            Deposit:
                acc[reciver] := acc[reciver] + amount;
        end if;
end process;

end algorithm;**)

```

执行上述代码，我们来看看 Error Trace 是什么样的吧。

> [!example] Error Trace

- Error Trace
	- S1
		- ![Pasted image 20240317112602.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317112602.png)
	- S2
		- ![Pasted image 20240317112613.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317112613.png)
	- S3
		- ![Pasted image 20240317112621.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317112621.png)
	- S4
		- ![Pasted image 20240317112631.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317112631.png)
	- S5
		- ![Pasted image 20240317112638.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317112638.png)

看完这些，你会发现，pc代表机器所处的状态。

里面的两个值分别代表两个机器本身处于什么状态。

比如说：

![Pasted image 20240317114850.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317114850.png)

这代表第一个进程处于 `CheckFonds` 状态，而第二个进程也处于 `CheckFonds` 状态。

那么这整个堆栈显示了什么错误呢？

我们可以看到 amount 的值为`<1, 5>`，这意味着在该情形下，第一个进程要发1美元，第二个进程要发5美元过去。

而错误发生在第四步到第五步之间。发生了什么呢？

小张想给小王寄5美元钱，但是只有4美元，减去后发现账户余额变成负一美元。这种错误超过了`acc[p]>0` 的约束条件。

而这五个步骤中整体发生了什么呢？

小张想给小王寄钱，在小王还没有收到钱时，小张第一次寄一美元，第二次寄五美元，账户变成负值。

那我们要怎样规避这些错误呢？

来看看教材给出的方案吧。

![Pasted image 20240317115911.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317115911.png)

它把检查和账户减发送金额的步骤合在了一起。这样，当小张发送一美元完成检查时，由于金额一并被减去了，所以进程二检查时发送的金额不会再超过现有金额四美元。

```tla+
EXTENDS Integers

(*--algorithm wire
variables
    people = {"Zhang", "Wang"},
    acc = [p \in people |-> 5];
        
define
        NoOverdrafts == \A p \in people: acc[p] >= 0
end define;

process Wire \in 1..2
    variables
        sender = "Zhang",
        reciver = "Wang",
        amount \in 1..acc[sender];

begin
    CheckAndWithDraw:
        if amount <= acc[sender] then
                acc[sender] := acc[sender] - amount;
            Deposit:
                acc[reciver] := acc[reciver] + amount;
        end if;
end process;

end algorithm;**)


\* BEGIN TRANSLATION
```

现在没有问题了。

之后要做什么？

整体金额不变的部分称为 `Temporal Properties` ，世俗的属性，时间的属性。而每次金额不变的属性称为 `invariants` ，也就是不变量。两者最大的区别目前看来是是否需要跨越不同的系统状态进行检查。 `invariants` 只需要在状态内检查属性就可以了。不过 `Temporal Properties` 则需要综合不同的系统状态考虑约束条件。

![Pasted image 20240317120852.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317120852.png)

语法格式变得奇怪了。

`EventuallyConsistent` ，最终不变的。

> [!faq] 问题是在 `PlusCal` 中，`<>[]()` 是什么表达？

- 一个错误
	- ![Pasted image 20240317122037.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317122037.png)
	- 定位到这一部出现错误。
	- 整行代码是：
	- ![Pasted image 20240317122420.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317122420.png)
	- 检查错误报告是：
	- ![Pasted image 20240317122501.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240317122501.png)

来看看DuckDuckgo上有什么消息吧。





## 参考书籍

