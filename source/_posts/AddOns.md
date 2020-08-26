---
title: Terminal AddOns
date: 2020-08-23 17:10:39
tags: [Terminal, Theme]
index_img: /img/image-20200823172029258.png
---

* <a href="https://github.com/romkatv/powerlevel10k">powerlevel10k</a>: a theme for Zsh. It emphasizes [speed](https://github.com/romkatv/powerlevel10k#uncompromising-performance), [flexibility](https://github.com/romkatv/powerlevel10k#extremely-customizable) and [out-of-the-box experience](https://github.com/romkatv/powerlevel10k#configuration-wizard).



* <a href="https://github.com/SpaceVim/SpaceVim">SpaceVim</a>: SpaceVim is a community-driven modular Vim distribution. It manages collections of plugins in layers, which help to collect related packages together to provide IDE-like features. SpaceVim is not just a vimrc but an ultimate Vim configuration, It contains many built-in features.

<img src="image-20200823172304012.png" alt="image-20200823172304012" style="zoom:50%;" />



<img src="image-20200823174451884.png" alt="image-20200823174451884" style="zoom:50%;" />

* <a href="https://github.com/Mayccoll/Gogh">Gogh</a> : color theme

<img src="image-20200823172029258.png" alt="image-20200823172029258" style="zoom:50%;" />



<br>

Vim clipboard无法使用的问题:

* linux

```shell
sudo apt-get install vim-gnome
```

安装后执行

```shell
vim --version | grep "clipboard"
```

可以发现clipboard前面有一个加号

* MacOS

弄了半天还是不行，不搞了，可恶

那就这样吧

To copy the current line, in command mode type:

```
"*yy
```

To copy the whole file/buffer, in command mode, first go to the beginning via `gg`, then type

```
"*yG
```

`"*5yy` says `5 lines yanked` 





