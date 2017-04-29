---
layout: post
title: 算法之二分查找
categories: [Algorithm, Array]
description: 常用数组排序算法
keywords: algorithm, array, binarysort
---

二分查找是一种在有序数组中查找某一特定元素的搜索算法。

# 使用场景

- 有序数组
- O(logn)的时间复杂度

# 算法要素
## while条件

- `start + 1 < end`
    - 结束时end = start + 1，需判断A[start] 和 A[end] 哪一个为所求索引。
    - 适用于查找target在数组中的起始位置或结束位置。
- `start < end`
    - 需判断A[start]是否为所求索引，若是直接跳出循环。
      - 否则容易陷入死循环
    - 适用于查找target在数组中是否存在（或只需返回其中一个索引）。

## mid计算方式
- `start + (end - start) / 2`或`start +((end - start) >>1)` 
    - 注意>>的优先级比+低，(end - start) >>1必须再添加一重()。
    - 推荐
- `(start + end) / 2`
    - int范围溢出，不推荐

## 判断下一步往哪一半走
- `A[mid] == target`时取哪一半
    - 保留剩下来一定有解的那一半。
- mid指针是否应该保存
    - A[mid]是否有可能为解。

# 常见问题类型
## 查找某数的位置
> 在一个排序数组中找一个数，返回该数出现的任意位置，如果不存在，返回-1
>

## 查找某数的插入位置

> 搜索target在数组A中的插入位置（即返回A中大于等于target的最小索引）

```java
    public int searchInsert(int[] A, int target) {
        if (A == null || A.length == 0){
            return 0;
        }
        int start = 0;
        int end = A.length - 1;
        while (start + 1 < end){
            int mid = start + (end - start) / 2;
            if (target == A[mid]) {
                return mid;
            } else if (target < A[mid]){
                end = mid;
            } else {
                start = mid + 1;
            }
        }
        if (target <= A[start]) {
            return start;
        } else if (target <= A[end]) {
            return end;
        } else {
            return end + 1;
        }
    }
```

## 查找某数的索引范围
> 给定一个包含 n 个整数的排序数组，找出给定目标值 target 的起始和结束位置。
> 如果目标值不在数组中，则返回[-1, -1]
>

```java
public static int[] searchRange(int[] array, int target) {
        if (array == null || array.length < 1) {
            return new int[]{-1, -1};
        }
        int[] result = new int[2];
        //先查找起始地址
        int start = 0;
        int end = array.length - 1;
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            //关键在于array[mid] == target时留下起始地址所在的左边
            if (array[mid] < target) {
                start = mid;
            } else {
                end = mid;
            }
        }
        //先判断较低位是否为起始地址
        if (array[start]==target) {
            result[0]=start;
        }else if (array[end]==target) {
            result[0]=end;
        }else{
            return new int[]{-1, -1};
        }
        //然后查找结束地址
        start=result[0];
        end= array.length - 1;
        while (start + 1 < end) {
            int mid = start + (end - start) / 2;
            //array[mid] == target时留下结束地址所在的右边
            if (array[mid] > target) {
                end = mid;
            } else {
                start = mid;
            }
        }
        //先判断较高位是否为结束地址
        if (array[end]==target) {
            result[1]=end;
        }else if (array[start]==target) {
            result[1]=start;
        }        
        return result;
    }
```
## 旋转数组中的最小值

> 假设一个旋转排序的数组其起始位置是未知的（比如0 1 2 4 5 6 7 可能变成是4 5 6 7 0 1 2）。
>
> 你需要找到其中最小的元素。
>
> 假设数组中不存在重复的元素。

### 分析

- num[start] < num[end]时，说明数组start~end段并非旋转数组，直接返回num[start]即可。
- num[mid] > num[end]时，说明最小值在mid~end段，且num[mid] 不是最小值，故最小值在mid+1~end段。
- num[mid] < num[end]（没有重复元素，故不存在num[mid] = num[end]）时，说明数组mid~end段有序，故最小值在start~mid段（此时mid可能为最小值，故end = mid）。

```java
	public int findMin(int[] num) {
        int start = 0;
        int end = num.length - 1;
        while (start < end) {
          	if(num[start] < num[end]){
              	return num[start];
          	}
            int mid = start + (end - start) / 2;
            if (num[mid] > num[end]) {
                start = mid + 1;
            } else {
                end = mid;
            }
        }
        return num[start];
    }
```

## 旋转数组中的最小值II

> 假设一个旋转排序的数组其起始位置是未知的（比如0 1 2 4 5 6 7 可能变成是4 5 6 7 0 1 2）。
>
> 你需要找到其中最小的元素。
>
> 数组中可能存在重复的元素。

### 分析

与上题不同的情况如下：

- 会出现num[mid] = num[end]的情况，这时候不清楚最小值在mid的哪一边，只需要将end减1继续比较即可。

```java
	public int findMin(int[] num) {
        int start = 0;
        int end = num.length - 1;
        while (start < end) {
          	if(num[start] < num[end]){
              	return num[start];
          	}
            int mid = start + (end - start) / 2;
            if (num[mid] > num[end]) {
                start = mid + 1;
            } else if (num[mid] < num[end]){
                end = mid;
            } else {
              	end--;
            }
        }
        return num[start];
    }
```
# 参考资料

[算法之美——二分法](http://blog.bcmeng.com/post/binarysearch.html)