---
title: 修复hexo博客的一个bug
date: 2017-08-25 18:18:37
tags: hexo
categories: hexo
---
之前不小心把JAVA分类写成了java发布了，然后又改了回来，并且手动地在博客`public/categories/`和`.deploy_git/categories/`下的java文件夹改成JAVA重新发布，结果在点击JAVA分类时会报404。经过一番研究我发现虽然我本地都将java改成了JAVA,但是GitHub仓库里的文件夹名称还是java。对比“java”和“JAVA”我猜测可能是git大小写不敏感所以JAVA并没有覆盖掉java，谷歌一下果然如此。由于我把java改成了JAVA所以在网页点击"分类"时访问的是：`https://xxxxxxxx/JAVA/`,而GitHub仓库里依然是java，只能访问`https://xxxxxxxxxx/java/`，由于http协议对于URL是大小写敏感的，所以访问`https://xxxxxxxx/JAVA/`必然会报404。找到问题所在下面开始修复bug:
 1. 进入.deploy_git文件夹，输入`git config core.ignorecase false`把忽略大小写
关闭。(必须要进入.deploy_git执行命令，因为只有这个文件夹是git仓库，这里的文件才会推送到GitHub仓库)
 2. 回到博客根目录，执行`hexo d`重新发布博客

执行完毕，bug修复！


















