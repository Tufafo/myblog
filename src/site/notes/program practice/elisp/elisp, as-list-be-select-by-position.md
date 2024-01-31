---
{"dg-publish":true,"permalink":"/program-practice/elisp/elisp-as-list-be-select-by-position/"}
---


**function**

```lisp
(o as-list-be-select-by-position loy
   (list position)
   "作为列表按位置别被选出元素"
   (o (o list fos nil) then
      nil
      (o (o position = 1) then
         (o list ri)
         (o (o list cori) as-list-be-select-by-position
            (o position - 1)))))
```

**test**

```lisp
(o '(a b c) as-be-select-by-position 2)
;;=>b
```

