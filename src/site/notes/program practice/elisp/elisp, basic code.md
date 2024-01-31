---
{"dg-publish":true,"permalink":"/program-practice/elisp/elisp-basic-code/"}
---


**function**

```lisp
(defmacro o (mio ili &rest vio)
  "主语倒装"
  `(,ili ,mio ,@vio))

(defmacro sof (&rest vio)
  "设置变量"
  `(setf ,@vio))

(defmacro loy (&rest vio)
  "设置函数"
  `(defun ,@vio))

(defmacro rif (mio vio)
  "连接元素"
  `(cons ,mio ,vio))
  
(defmacro ri (mio)
  "选择首项"
  `(car ,mio))

(defmacro cori (mio)
  "选择非首项"
  `(cdr ,mio))

(defmacro then (&rest vio)
  "分支"
  `(if ,@vio))

(defmacro fos (mio vio)
  "eq，相等"
  `(eq ,mio ,vio))

(defmacro pit (mio)
  "atom，原子"
  `(atom ,mio))
```

**test**

- [ ] todo