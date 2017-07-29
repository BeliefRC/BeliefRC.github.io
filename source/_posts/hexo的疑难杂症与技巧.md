---
title: hexo的疑难杂症与技巧
date: 2017-07-29 14:18:48
tags: hexo
---

# hexo的疑难杂症与技巧

前言：
今天手欠捯饬了下博客，结果就出现各种莫名奇妙的问题，无奈之下**删掉所有文件**重新发布一边！现在特意总结一下

## 一.命令报错和警告
###1. Deployer not found: git
输入 hexo d后报错
--deployer not found: git
**解决办法**：npm install hexo-deployer-git –save
**分析**：HEXO更新到3.0之后，deploy的type 的github需要改成git
同时需要安装一个依赖支持此命令

###2.The file will have its original line endings in your working directory.
输入 hexo d后警告
--warning: LF will be replaced by CRLF in tags/生活/index.html.
The file will have its original line endings in your working directory.

**解决办法**：git config --global core.autocrlf  false
**分析**：这个设置可以取消Git的自动替换 换行方式 的选项

### 3.Git Pull Failed: refusing to merge unrelated histories
输入git pull 失败
**解决办法**：git pull origin master --allow-unrelated-histories
**分析**："git merge" used to allow merging two branches that have no common base by default, which led to a brand new history of an existing project created and then get pulled by an unsuspecting maintainer, which allowed an unnecessary parallel history merged into the existing project. The command has been taught not to allow this by default, with an escape hatch --allow-unrelated-histories option to be used in a rare event that merges histories of two projects that started their lives independently.
**详情参考**[这里](https://stackoverflow.com/questions/37937984/git-refusing-to-merge-unrelated-histories "这里")

## 二.hexo常用设置
### 1.头像与博客favicon设置
### 2.文章的置顶功能
### 3.tags设置

