---
title: Count Inversion ( Divide and Conquer)
date: 2019-10-15 22:07:35
tags: [算法, C++]
categories: 算法
---



## **Problem Description**

Recall the problem of finding the number of inversions. As in the course, we are given a sequence of **n** numbers a1,a2,⋯,an，and we define an inversion to be a pair i<j such that ai>aj

We motivated the problem of counting inversions as a good measure of how different two orderings are. However, one might feel that this measure is too sensitive. Let's call a pair a significant inversion if **i<j** and **ai>3aj**. Give an **O(nlogn)** algorithm to count the number of significant inversions between two orderings.

The array contains N elements (1<=N<=100,000). All elements are in the range from 1 to 1,000,000,000.

### Input

The first line contains one integer N, indicating the size of the array. The second line contains N elements in the array.

- 50% test cases guarantee that N < 1000.

### Output

Output a single integer which is the number of pairs of significant inversions.

### Sample Input

```
6
13 8 5 3 2 1
```

### Sample Output

```
6
```



## **主要思路**

这一题是求解逆序数的变形题，仅仅是改变了**ai>3aj**这一条件，但是基本解题思路是一样的，使用了分治的策略，具体操作其实就是在归并排序的过程中记录一下“逆序数”的个数（这题的逆序数表示的是前面的数大于后面的数的三倍）。所以只需要写一个归并排序就能够解决这一问题，下面简单回顾一下归并排序。



## **归并操作**

归并操作(merge)，也叫归并算法，指的是将两个顺序序列合并成一个顺序序列的方法。（百度百科）

下面一个动态图，很直观的展示了归并的具体操作（来源https://www.jianshu.com/p/33cffa1ce613）

<img width=800 src="2019-10-15-Count-Inversion-Divide-and-Conquer/merge.gif" >



下面另举一个实例，这个例子来自[百度百科](https://baike.baidu.com/item/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F/1639015?fr=aladdin)（这里的逆序数表示的单纯的前面的数大于后面的数)

如设有数列{6，202，100，301，38，8，1}

初始状态分为若干部分：{6}	{202} 	{100}	{301}	{38}	{8}	{1}

第一次归并后：{6,202},{100,301},{8,38},{1}，比较次数：3；

第二次归并后：{6,100,202,301}，{1,8,38}，比较次数：4；

第三次归并后：{1,6,8,38,100,202,301},比较次数：4；

总的比较次数为：3+4+4=11；

逆序数为14；



## **代码（C++）**

```c++
#include <iostream>
#include <stdio.h>
using namespace std;

void  Merge(unsigned int *a,unsigned int b,unsigned int m,unsigned int e,unsigned int &Count)
{
    unsigned int i =b;
    unsigned int j = m+1;
    unsigned int nb = e-b+1; //之后数组的长度
    unsigned int *mer = new unsigned int[nb];
    unsigned int index = 0;
    while(i<=m&&j<=e)
    {
         if(a[i]>3*a[j])//如果条件为a[i]>a[j]则为普通的逆序数程序
        {
           //注意，这里a[i]一旦满足逆序数条件则在i之后的所有数都满足逆序数条件（因为待合并的两个数组是有序的）
            Count+=m-i+1;
            j++;
        } else{
           i++;
        }
    }
    //合并操作
    i = b;
    j = m+1;
    while(i<=m&&j<=e)
    {
        if(a[i]<a[j])
        {
            mer[index++] = a[i];
            i++;
        }else{
             mer[index++] = a[j];
             j++;
        }
    }
    if(i<=m)
    {
        for( unsigned int k = i; k<= m; k++)
            mer[index++] = a[k];

    }
    if(j<=e)
    {
        for( unsigned int k = j; k<= e;k++)
            mer[index++] = a[k];
    }
    for( unsigned int k = 0; k<nb; k++)
    {
        a[b+k] = mer[k];

    }


}
void MergeSort(unsigned int *a, unsigned int b, unsigned int e,unsigned int &Count)
{
    if(b>=e)
        return ;
    if( b<e){
    unsigned int mid = (b+e)/2;
    MergeSort(a,b,mid,Count);
    MergeSort(a,mid+1,e,Count);
    Merge(a,b,mid,e,Count);
    }

}
int main()
{
    unsigned int n;
    scanf("%d",&n);

    unsigned int *a = new unsigned int[n];
    for(unsigned int i =0; i< n; i++)
        scanf("%d",&a[i]);
    unsigned int c = 0;

    MergeSort(a,0,n-1,c);

    printf("%d",c);

    return 0;
}

```