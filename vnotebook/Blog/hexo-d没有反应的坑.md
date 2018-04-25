---
title: hexo d没有反应的坑
date: 2018-04-24 21:55:16
tags: Blog Hexo 错误处理
---

如下图没任何反应，不知道成功或失败，反正github上没有变化
![](_v_images/_1524579203_21076.jpg)

```bash
npm install hexo-deployer-git --save
```

最大的坑就是配置文件，必须严格按照那特定的格式，可是官方文档也没特别提醒，必须是这个格式，``type``，``repo``，``branch``前两个空格，``：``后面一个空格，差一个都不行的：

```yml
deploy: 

  type: git 

  repository: url 

  branch: master

```
