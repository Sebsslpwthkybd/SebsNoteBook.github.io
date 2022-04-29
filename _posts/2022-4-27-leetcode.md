---
title: leetcode刷题
date: 2022-04-27 15:00:00 +0800
categories: [随笔]
tags: [leetcode]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true



---

#### 88.合并两个有序数组（1）

##### 双指针

一开始想把快排嵌入到nums1和nums2的合并中， 后来发现完全没必要（两个数组都是有序的）

正好复习下快排：分治，左右指针从头尾开始，左大于右，交换，左右指针一定有一个是一开始的基准值