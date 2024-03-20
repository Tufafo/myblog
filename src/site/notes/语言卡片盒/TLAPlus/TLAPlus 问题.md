---
{"dg-publish":true,"permalink":"/语言卡片盒/TLAPlus/TLAPlus 问题/","tags":["TLAplus"]}
---

## 目前遇到的问题
#### Q1：The spec status is not "parsed"

![Pasted image 20240318131144.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240318131144.png)

直译好像是，规范状态未被“解析”，检查模型前必须让状态是“被解析”的。

![Pasted image 20240318134551.png](/img/user/%E5%9B%BE%E7%89%87/Pasted%20image%2020240318134551.png)

问题根源找到了。

`Extends` 部分我设置成为了

`EXTENDS Integers, Sets`

把 `Sets`去掉就可以。

