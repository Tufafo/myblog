---
{"dg-publish":true,"permalink":"/dg/physical/the-install-and-config-of-clojure/"}
---


## Introduce

clojure is a kind of lisp language that run on the java virtual machine. Recently, I want to install clojure on my server and program with emacs. Here is my configuration.

Environment: Ubuntu20.04, emacs29.

## Install Clojure

The apt in Ubuntu20.04 contains the package of `clojure` `leiningen` `deault-jre`, so just install them directly.

```bash
sudo apt install clojure
sudo apt install leiningen
sudo apt install default-jre
```

## The configuration of emacs environment

Here is my elisp config code and it is saved in file `/.emacs.d/init.el` .

```elisp
;;clojure interactive development environment that rocks!
(use-package cider
  :ensure t)

(use-package lsp-treemacs
  :ensure t)

;;grammer checking
(use-package flycheck
  :ensure t)

;;auto compelete
(use-package company
  :ensure t)

;;clojure main mode
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


## Start the environment

Create new clojure project in tmux.

```bash
lein new myapp
```

Start the repl of my clojure project.

```bash
cd myapp
lein repl
```

Connect my clojure project in Emacs.

```emacs
M-x cider RET
cider-connect-clj RET
localhost RET
TAB RET
```

## Refference

- [Clojure, Install Clojure](https://clojure.org/guides/install_clojure)
- [emacs-lsp.github, Configure Emacs as a Clojure IDE](https://emacs-lsp.github.io/lsp-mode/tutorials/clojure-guide/)
