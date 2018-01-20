---
title: HEXO博客搭建笔记
date: 2017-07-08 18:25:39
tags: tags
categories: HEXO
---

```python
<!-- more --> 阅读全文 手动进行截断

“```python
```”插入代码

```
```bash
hexo g&&hexo d
```

<!-- more -->

改了一下github的名字，结果hexo d命令失败，先让登录，结果怎么都是登录失败，遇到这个问题要改hexo-deployer-git里面的
```java
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
```
问题如下：
```java

Logon failed, use ctrl+c to cancel basic credential prompt.
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com/': Invalid argument
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: Logon failed, use ctrl+c to cancel basic credential prompt.
bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com/': Invalid argument


```