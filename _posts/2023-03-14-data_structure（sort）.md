---
title: 数据结构（排序）
date: 2023-03-14 16:00:00 +0800
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

# 排序的分类

- ## 内部排序

  待排序列完全放在内存中进行排序

- ## 外部排序

  对大文件的排序（内存和外部存储器的多次数据交换）

- 排序是否稳定

  排序后，相同的元素在前面的是否一直在前面

  前面的相同元素与后面的相同元素不交换，则是稳定的

# 内部排序

## 内部排序分类

- 交换排序
- 选择排序
- 插入排序
- 归并排序
- 基数排序

## 基本数据结构

```
typedef int KeyType;  // 数据类型

typedef struct {  // 单体
	KeyType key;
	...
} RecordType, RcdType;

typedef struct {  // 顺序表
	RcdType * rcd;
	int length;
	int size;
} RcdSqList;
```

## 交换排序

- 冒泡排序



- 快速排序

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

## 选择排序

简单选择排序

堆排序

## 插入排序

- 直接插入排序

将无序区的第一个记录按关键字插入到有序区的合适位置（在循环查找插入位置时，顺便将元素后移）

```
void InsertSort(RcdSqList &L) {
	int i, j;
	for(i=1; i < L.length; i++)
		if (L.rcd[i+1].key < L.rcd[i].key) {
			L.rcd[0] = L.rcd[i+1];
			j = i+1;
			do {
				j--;L.rcd[j+1] = L.rcd[j]
			} while (L.rcd[0].key < L.rcd[j-1].key)
			L.rcd[j] = L.rcd[0];
		}
}
```

- 希尔排序


```
void ShellInsert(RcdSqList &L, int dk) {
	int i, j;
	for (i = 1; i <= L.length-dk; ++i)
		if(L.rcd[i+dk].key < L.rcd[i].key) {
			L.rcd[0] = L.rcd[i+dk];
			j = i + dk;
			do {j-= dk; L.rcd[j+dk] = L.rcd[j]}  // 只移动一个
			while (j-dk > 0 && L.rcd[0].key < L.rcd[j-dk].key);
             L.rcd[j] = L.rcd[0];
		}
}

// d[]为增量序列, 有t个元素，即循环做t次，每次增量为d[k]的希尔排序
void ShellSort(RcdSqList & L, int d[], int t) {
	int k;
	for (k=0; k<t; ++k) ShellInsert(L, d[k])
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



## 基数排序

- 链式基数排序



- 计数基数排序

​	外循环：从低位到高位

​	内循环：

​	count数组记录0-9数字出现的次数

​	pos数组安排各数字对应存放的内存位置（要预留足够所有数字存放的位

​	置），放入一个要+1

​	将数按照pos数组进行存放

```
// 顺序表数据结构
typedef struct {
	KeysType * keys;
	...
} KeysRcdType;  // 关键字字段
typedef struct {
	KeysRcdType * rcd;  // 关键字起始位置（哨兵）
	int length;
	int size;
	int digitNum;
	int radix;
} KeysSqList;  // 顺序表类型
```

```
void RadixPass(KeysRcdType rcd[], KeysRcdType rcd1[], int n, int i, int count[], int pos[], int radix){
	
}
```

```
Status RadixSort(KeysSqList & L) {
	KeysRcdType * rcd1
	int i = 0, j;
	int * count, * pos;
	// 申请内存空间
	count = (int *) malloc (L.radix * sizeof (int));
	pos = (int *) malloc (L.radix * sizeof (int));
	rcd1 = (KeysRcdType *) malloc ((L.length + 1) * sizeof (KeysRcdType));
	
	// 申请校验
	if (NULL==count || NULL==pos || NULL==rcd1) return OVERFLOW;
	
	// 开始基数排序
	while(i<L.digitNum){
		for(j=0; j<L.radix; ++j) count[j]=0;
		if(0==i%2)
			RadixPass
	}
	if(1 == L.digitNum%2)
		for(j=1;j<=L.length;++j) L.rcd[j] = rcd1[j];
	free(count); free(pos); fress(rcd1);
	return OK;
}
```

