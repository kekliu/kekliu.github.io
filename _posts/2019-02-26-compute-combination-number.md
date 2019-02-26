---
layout:     post
title:      "复杂度分析--以计算排列数为例"
subtitle:   ""
date:       2019-02-26 12:00
author:     "kekliu"
header-img: ""
catalog:    true
tags:
    - Algorithm
---


### 1. 代码和分析
话不多说，直接上代码。
```
class Solution {
public:
	// 直接递归
	// 原理: C(i,j)=C(i-1,j)+C(i-1,j-1)
	// 时间复杂度: O(C(n,k)) 空间复杂度: O(n)
	int C0(int n, int k) {
		return (k == 0 || n == k) ?
			1
			:
			C0(n - 1, k) + C0(n - 1, k - 1);
	}

	// 打表
	// 原理: C(i,j)=C(i-1,j)+C(i-1,j-1)
	// 时间复杂度: O(n^2) 空间复杂度: O(n^2)
	int C1(int n, int k) {
		vector<vector<int>> C(n + 1, vector<int>(n + 1));
		for (int i = 0; i < n + 1; ++i)
			for (int j = 0; j <= i; ++j)
				C[i][j] = (j == 0 || i == j) ? 1 : C[i - 1][j] + C[i - 1][j - 1];
		return C[n][k];
	}

	// 打表
	// 原理: F(i,j)=F(i-1,j)+F(i,j-1) where C(i,j)=F(j,i-j)
	// 时间复杂度: O(k(n-k)) 空间复杂度: O(k(n-k))
	int C2(int n, int k) {
		int m = n - k;
		vector<vector<int>> C(m + 1, vector<int>(k + 1));
		for (int i = 0; i < m + 1; ++i)
			for (int j = 0; j < k + 1; ++j)
				C[i][j] = (i == 0 || j == 0) ? 1 : C[i - 1][j] + C[i][j - 1];
		return C[m][k];
	}

	// 动态规划
	// 原理: 直接利用帕斯卡三角
	// 时间复杂度: O(n^2) 空间复杂度: O(n)
	int C3(int n, int k) {
		vector<int> A(n + 1);
		A[0] = 1;
		for (int i = 1; i <= n; i++) {
			A[i] = 1;
			for (int j = i - 1; j > 0; j--) {
				A[j] += A[j - 1];
			}
		}
		return A[k];
	}

	// 动态规划
	// 原理: C2的改进
	// 时间复杂度: O(k(n-k)) 空间复杂度: O(min(k,n-k))
	int C4(int n, int k) {
		int m = min(k, n - k);
		k = n - k;
		vector<int> A1(m + 1, 1), A2(m + 1, 1);
		for (int i = 0; i < k; ++i) {
			for (int j = 1; j < m + 1; ++j)
				A2[j] = A2[j - 1] + A1[j];
			A1.swap(A2);
		}
		return A1[m];
	}
};
```
