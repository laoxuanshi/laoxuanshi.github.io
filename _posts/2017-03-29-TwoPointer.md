---
layout: post
title: 算法之双指针
categories: [Algorithm, Array]
description: 利用双指针遍历数组
keywords: algorithm, array, two pointer
---

双指针，顾名思义，就是利用两个指针去遍历数组，一般来说，遍历数组采用的是单指针（index）去遍历，两个指针一般是在有序数组中使用，一个放首，一个放尾，同时向中间遍历，直到两个指针相交，完成遍历，时间复杂度也是O(n)。

### 使用场景

- 时间复杂度：**O(n)** 并且 **One Pass**
- 空间要求：**in place**
- 两数之和满足某条件
  - 先对数组排序，再采用两个指针，分别从前和后往中间遍历，front增大，tail减小，通过对条件的判断，可以在O(n)内遍历，而非使用双重循环。
- in place交换
  - 一个指针遍历，一个指针去找可以用来交换的元素。

### 常见题型

#### Move Zeros

> 给一个数组 *nums* 写一个函数将 `0` 移动到数组的最后面，非零元素保持原数组的顺序。[详见Leetcode 283](https://leetcode.com/problems/move-zeroes/)

- 解法1：用一个指针 i 来记录当前遍历的位置，另一个指针 j 记录第一个零的位置，然后遇到非零的时候，nums[j] 和 nums[i]交换即可。

  - 如此即可保证所有非零元素均在第一个零的前面
  - 相邻元素（忽略i和j之间的零）交换类似于冒泡排序（稳定排序）中的交换，所以非零元素保持原数组的顺序。

  ```java
  public void moveZeroes(int[] nums) {
      int j = 0;
      for(int i = 0; i < nums.length; i++) {
          if(nums[i] != 0) {
              int temp = nums[j];
              nums[j] = nums[i];
              nums[i] = temp;
              j++;
          }
      }
  }
  ```

- 解法2：事实上只需要记住非零元素的个数，遍历nums数组时即可将遇到的非零元素插入到相应的位置，最后将后面的元素置零即可。

  ```java
  public void moveZeroes(int[] nums) {
      if (nums == null || nums.length == 0) return;        
      int insertPos = 0;
      for (int num: nums) {
          if (num != 0) nums[insertPos++] = num;
      }        
      while (insertPos < nums.length) {
          nums[insertPos++] = 0;
      }
  }
  ```

#### Remove Element

> Given an array and a value, remove all instances of that value in place and return the new length.[详见Leetcode 27](https://leetcode.com/problems/remove-element/)

- 解法1：同Move Zeros解法1，利用双指针将value移动到数组的最后面即可。

  ```java
  public int removeElement(int[] nums, int val) {
  	int j = 0;
      for(int i = 0; i < nums.length; i++) {
          if(nums[i] != val) {
              int temp = nums[j];
              nums[j] = nums[i];
              nums[i] = temp;
              j++;
          }
      }
      return j;
  }
  ```

- 解法2：同Move Zeros解法2。

  ```java
  public int removeElement(int[] nums, int val) {
  	if (nums == null || nums.length == 0) return 0;
      int insertPos = 0;
      for (int num: nums) {
          if (num != val) nums[insertPos++] = num;
      }
  	return insertPos;
  }
  ```

- 解法3：由于本题没有要求保持原数组顺序，可以每次将数组末端元素与零元素交换。

  ```java
  public int removeElement(int[] A, int elem) {
      int len = A.length;
      for (int i = 0 ; i< len; ++i){
          while (A[i]==elem && i< len) {
              A[i]=A[--len];
          }
      }
      return len;
  }
  ```

#### Remove Duplicates from Sorted Array

> 给一个有序整数数组，去除重复的元素。将去除重复之后的元素放在数组的开头，并返回去除重复元素之后的元素个数。

- 一个指针负责遍历，另一个指针负责记录当前不重复的数组的长度，遇到相同的则移动遍历指针，否则交换，最后返回不重复数组的长度。

#### 2Sum

> 给定一个数组，然后找到两个数之和等于一个target。

- 遍历数组，HashMap存放< nums[i],  i>，对于新来的nums[j]，判断target - nums[j]是否已存在即可。
- 排序后双指针

#### 3Sum

> 给出一个有n个整数的数组S，在S中找到三个整数a, b, c，找到所有使得a + b + c = 0的三元组。

- 先选定一个数，再用双指针找两个数的和等于这个target为该数取负。

#### 4Sum

- 先选定i和j，同理。

#### Partition Array

快排里的partition。

#### Container With Most Water

##### 分析

> 由于桶的容量由最短的那个木板决定： tmpArea= min(h[left],h[right]) * (right - left)

- left和right分别指向两端的木板。
- left和right都向中央移动，每次移动left和Right中间高度较小的（因为反正都是移动一次，宽度肯定缩小1，这时候只能指望高度增加来增加容量，肯定是替换掉高度较小的，才有可能找到更大的容量。）
- 看新桶子的容量是不是大于Vol_max，直到left和right相交。

```java
public int maxArea(int[] height) {
        int max = 0;
        int i = 0;
        int j = height.length - 1;
        if (height.length < 2)	 return 0;
        while (i < j) {
            int lowerHeigt = 0;
            int tmpArea = 0;
            if (height[i] <= height[j]) {
                lowerHeigt = height[i];
                i++;
            } else {
                lowerHeigt = height[j];
                j--;
            }
            tmpArea = lowerHeigt * (j - i);
            if (tmpArea > max)	max=tmpArea;
        }
        return max;
    }
```

#### Trapping Rain Water

> Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.
> For example, given [0,1,0,2,1,0,1,3,2,1,2,1], return 6.

#####  分析



```java
public int trap(int[] A){
    int a=0;
    int b=A.length-1;
    int max=0;
    int leftmax=0;
    int rightmax=0;
    while(a<=b){
        leftmax=Math.max(leftmax,A[a]);
        rightmax=Math.max(rightmax,A[b]);
        if(leftmax<rightmax){
          // leftmax is smaller than rightmax, so the (leftmax-A[a]) water can be stored
            max+=(leftmax-A[a]);
            a++;
        }
        else{
            max+=(rightmax-A[b]);
            b--;
        }
    }
    return max;
}
```

#### 