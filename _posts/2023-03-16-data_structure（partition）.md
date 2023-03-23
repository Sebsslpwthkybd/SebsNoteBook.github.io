---
title: 数据结构（递归和分治）
date: 2023-03-16 16:00:00 +0800
categories: [C/C++]
tags: [data structure]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true
---

## 折半查找

一个数组

先切分一半，按照中位元素判定是前半区还是后半区

将半区数组传入递归

不断切分直至中位元素关键字即为查找的元素

```
int BinSearch(RcdType rcd[], KeyType key, int low, int high) {
	int mid = (low + high) / 2;  // 获得中位索引
	if (low > high) return -1;  // 传入错误
	if (rcd[mid].key == key)
		return mid;  // 查找到元素的情况
	else if (rcd[mid].key > key)
		return BinSearch(rcd, key, low, mid-1);
	else
		return BinSearch(rcd, key, mid+1, high);
}
```

## 归并排序

无序数组分割成若干个等长的有序数列，然后组合这些有序数列

2-路归并

```
void Merge(RcdType SR[], RcdType TR[], int i, int m, int n){
	// 两个有序区间SR[i, m]和SR[m+1, n],归并到TR[i, n]里去
	int k, j;
	for (j=m+1, k=i; i<=m && j<=n; ++k) {
		if (SR[i].key <= SR[j].key)  // 加上等于号就不会变不稳定
			TR[k] = SR[i++];
		else TR[k] = SR[j++]
	}
	while (i<=m) TR[k++] = SR[i++];  // [m+1, n]部分已归并完,还差m未归并
	while (j<=n) TR[k++] = SR[j++];
}
```

(需要实现两个数组的交替归并，指示数i为单数则R2向R1归并)

递归调用2-路归并

```
void MSort(RcdType R1[], RcdType R2[], int i, int s, int t) {
	int m;
	if(s=t) {
		if(1==i%2) R2[s]=R1[s];
	}
	else {  // 平分区间后按照交替归并原则分开归并
		m = (s+t) / 2;
		MSort(R1, R2, i+1, s, m);
		MSort(R1, R2, i+1, m+1, t);
		if(i==i%2)
			Merge(R1, R2, s, m, t);
		else
			Merge(R2, R2, s, m, t);
	}
}
```

申请内存

```
void MergeSort(RcdSqList & L) {
	RcdType * R;
	R = (RcdType *) malloc ((L.length+1) * sizeof(RcdType));
	MSort(L.rcd, R, 0, 1, L.length);
	free(R);
}
```

## 快速排序

冒泡的改进

选定一个目标，然后对比，将比目标小的值放目标左边，将比目标大的值放目标右边

划分-快排

划分算法

把低位弄去指示位，找高位元素比中枢小的，放进低位（高位空），然后找低位元素比中枢大的，放进高位（低位空），然后再把指示位元素放回低位。

```
int Partition(RcyType rcd[], int low, int high) {
	rcd[0] = rcd[low];
	while (low < high) {
		// 找出比中枢大、小的各一个元素
		while(low<high && rcd[high].key >= rcd[0].key) --high;
		rcd[low]=rcd[high];
		while(low<high && rcd[low].key <= rcd[0].key) ++low;
		rcd[high]=rcd[low];
	}
	rcd[low]=rcd[0];
}
```

递归调用划分算法

```
void QSort(RcdType rcd[], int s, int t) {
	int pivotloc;
	if (s<t) {
		pivotloc = Partition(rcd, s, t);
		QSort(rcd, s, pivotloc-1);
		QSort(rcd, pivotloc+1, t);
	}
}
```

```
void QuickSort(RcdSqList &L) {
	QSort(L.rcd, 1, L.length);
}
```

