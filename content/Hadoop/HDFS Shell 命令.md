---
title: HDFS Shell 命令
layout: page
date: 2016-10-26 15:03
---

[TOC]

如何使用 Shell 操作 HDFS 参考 Blog: [使用 Shell 操作 HDFS](http://blog.smallcpp.cn/04-shi-yong-shell-cao-zuo-hdfs.html)

# 常用命令
get == copyToLocal

put == copyFromHdfs

## cp
源和目标都是 hdfs

-f 覆盖

-p 保留文件属性 (cp 文件时, 更新时间会变, 所属组也会变成操作者)

## mv
源和目标都是 hdfs

## count
统计目录数量

列出指定目录下的子目录数量 文件数量 目录大小 文件路径

-q 查看限额

-h 格式化大小

## du
统计目录大小

-s 汇总

-h 格式化

## dus
相当于 du -s

## expunge
清空 hdfs 回收站

## mkdir
-p 递归创建

## touchz
创建空白文件

## rm
-f 文件不存在不提示

-r 递归

-R 同 -r

## cat/text
查看文件内容

## tail
显示最后 1k 的内容

-f 动态显示新建内容

## setrep
设置副本?

# dfsadmin 系列
## report
集群报告

-live 活 (默认)

-dead 死

-decommissioning 正在解除中

## safemode
当前集群安全模式 (只读)

enter 进入安全模式

leave 离开安全模式

get 当前状态

wait

## setQuota quota dirname
数量限额, 限定 dirname 下最多有 quota 个文件/目录

注意, 包含目录本身, 例如有个目录, 两面有两个文件, 那这个目录总共是算 3 个

## clrQuota
清除数量限额

## setSpaceQuota 4k dirname
空间限额, 限定目录空间大小

## clrSpaceQuota
清除空间限额

## allowSnapshot
设置一个目录为可快照: `hdfs dfsadmin -allowSnapshot <path>`

## disallowSnapshot
取消目录可快照: `hdfs dfsadmin -disallowSnapshot <path>`
