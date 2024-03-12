---
{"dg-publish":true,"permalink":"//clojure/","tags":["环境配置"]}
---


## 简介

clojure是一种安装在java虚拟机上的lisp方言。最近想在linux环境上尝试clojure的语言，配合emacs使用。现在记录安装过程。

环境Ubuntu20.04，emacs29。

## 安装Clojure

Ubuntu20.04环境下apt中本身就包含Clojure的安装包，所以直接安装clojure即可。

```bash
sudo apt install clojure
sudo apt install leiningen
sudo apt install default-jre
```


## Emacs环境

elisp代码如下，保存在配置文档 `/.emacs.d/init.el`：

```elisp
;;clojure interactive development environment that rocks!
(use-package cider
  :ensure t)

(use-package lsp-treemacs
  :ensure t)

;;语法检查
(use-package flycheck
  :ensure t)

;;自动补全
(use-package company
  :ensure t)

;;clojure主模式
(use-package clojure-mode
  :ensure t
  :config
  (add-hook 'clojure-mode-hook 'lsp)
  (add-hook 'clojurescript-mode-hook 'lsp)
  (add-hook 'clojurec-mode-hok 'lsp))

(use-package lsp-mode
  :ensure t
  :config)
```

## 启动环境

在tmux中创建新的clojure工程：

```bash
lein new myapp
```

启动该工程的repl：

```
cd myapp
lein repl
```

在emacs中接入该工程：

``` emacs
M-x cider RET
cider-connect-clj RET
localhost RET
TAB RET
```

在repl中进行代码测试

```bash
myapp.core> (+ 3 5)
8
```

## 语法

关于语法，可以参考[W3Cschool, clojure教程](https://www.w3cschool.cn/clojure/clojure-j5w81wf2.html) 《Programming Clojure》、 《 Web Development with Clojure, Third Edition》 等书籍。

## 参考以及相关链接
- [W3Cschool, clojure教程](https://www.w3cschool.cn/clojure/clojure-j5w81wf2.html)
- [clojure官网安装教程](https://clojure.org/guides/install_clojure)
- [Configure Emacs as a Clojure IDE](https://emacs-lsp.github.io/lsp-mode/tutorials/clojure-guide/)