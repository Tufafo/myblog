---
{"dg-publish":true,"permalink":"/数字花园/TLA+/TLA+ 练习，电报问题/"}
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

![Pasted image 20240317110004.png](/img/user/Pasted%20image%2020240317110004.png)

因为小张和小王的初始金额都是5，如果一次电报发送6美元，无论谁发都会出现负值。

不过有意思的是错误数量。

分别试试把 `amount` 设为 1..7，1..9看看吧。

- `amount \in 1..6`
	- ![Pasted image 20240317111028.png](/img/user/Pasted%20image%2020240317111028.png)
- `amount \in 1..7`
	- ![Pasted image 20240317111053.png](/img/user/Pasted%20image%2020240317111053.png)
- `amount \ in 1..9`
	- ![Pasted image 20240317111114.png](/img/user/Pasted%20image%2020240317111114.png)
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
		- ![Pasted image 20240317112602.png](/img/user/Pasted%20image%2020240317112602.png)
	- S2
		- ![Pasted image 20240317112613.png](/img/user/Pasted%20image%2020240317112613.png)
	- S3
		- ![Pasted image 20240317112621.png](/img/user/Pasted%20image%2020240317112621.png)
	- S4
		- ![Pasted image 20240317112631.png](/img/user/Pasted%20image%2020240317112631.png)
	- S5
		- ![Pasted image 20240317112638.png](/img/user/Pasted%20image%2020240317112638.png)

看完这些，你会发现，pc代表机器所处的状态。

里面的两个值分别代表两个机器本身处于什么状态。


