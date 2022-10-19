---
title: 关于FragmentContainerView闪退问题
date: 2022-5-20
categories: bug
tags: fragment
author: 小朱
comments: true
hide: index
---

# 前言

fragment在google官方的建议下使用FragmentContainerView进行管理使用，免去了手动管理所带来的麻烦的问题

bug：

在fragment中不建议直接使用onCreate重载，两次初始化会导致页面直接闪退？？？？

