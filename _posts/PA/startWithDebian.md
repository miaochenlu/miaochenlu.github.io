

[bash: adduser: command not found [Debian Buster\]](https://unix.stackexchange.com/questions/565665/bash-adduser-command-not-found-debian-buster)

Adding `sbin` to path seems to at least temporarily fix the problem for me:

```
export PATH="$PATH:/sbin:/usr/sbin:usr/local/sbin"
```

------

Or try executing `/sbin/useradd`



惊了，将username加到了sudo后不知道怎么就一直不能通过sudo访问，重启了之后就好了，估计重启了之后才能起作用

makefile中PHONY的作用

Tmux入门: https://linux.cn/article-3952-1.html





