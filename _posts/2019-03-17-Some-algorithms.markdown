---
layout:       post
title:        "JavaScript 算法小结"
subtitle:     "巩固复习常见算法"
date:         2019-03-17
author:       "corcd"
header-img:   null
header-mask:  0.05
catalog:      true
tags:
    - 技术
    - JavaScript
---

## 排序算法

#### 冒泡排序
##### 算法思想
很简单，层级向上冒泡

```
function bubbleSort(arr) {
  if (!arr || arr.length <= 2) {
    return
  }

  for (let i = 0; i < arr.length - 1; i++) {
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        let temp = arr[j + 1]
        arr[j + 1] = arr[j]
        arr[j] = temp
      }
    }
    console.log(`第${i}趟排序后的顺序: ${arr}`)
  }
  return arr
}

let sortArr = [3, 6, 4, 2, 11, 10, 5]

console.log(bubbleSort(sortArr)) // [ 2, 3, 4, 5, 6, 10, 11 ]
```

打印出的结果：
- 第0趟排序后的顺序: 3,4,2,6,10,5,11
- 第1趟排序后的顺序: 3,2,4,6,5,10,11
- 第2趟排序后的顺序: 2,3,4,5,6,10,11
- 第3趟排序后的顺序: 2,3,4,5,6,10,11
- 第4趟排序后的顺序: 2,3,4,5,6,10,11
- 第5趟排序后的顺序: 2,3,4,5,6,10,11

能不能让它比较的次数少点呢？可以，加入一个 flag 变量，我们明显可以看到，上边的第 2、3、4 趟都是一样的，说明做了无用功的比较，这时候通过 flag 来记录冒泡排序是否发生数据元素位置交换。如果没有发生交换，说明序列已经有序了，不必继续进行下去了

##### 改良后算法：
```
function bubbleSort(arr) {
  if (!arr || arr.length <= 2) {
    return
  }

  let flag = 1 // 有序 = 0 ，无序 = 1

  for (let i = 0; i < arr.length - 1 && flag != 0; i++) {
    flag = 0
    for (let j = 0; j < arr.length - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        let temp = arr[j + 1]
        arr[j + 1] = arr[j]
        arr[j] = temp
        flag = 1
      }
    }
    console.log(`第${i}趟排序后的顺序: ${arr}`)
  }
  return arr
}

let sortArr = [3, 6, 4, 2, 11, 10, 5]

console.log(bubbleSort(sortArr)) // [ 2, 3, 4, 5, 6, 10, 11 ]
```

改良后打印出的结果：
- 第0趟排序后的顺序: 3,4,2,6,10,5,11
- 第1趟排序后的顺序: 3,2,4,6,5,10,11
- 第2趟排序后的顺序: 2,3,4,5,6,10,11
- 第3趟排序后的顺序: 2,3,4,5,6,10,11

##### 时间复杂度和空间复杂度
冒泡排序在平均和最坏情况下的时间复杂度都是 O(n^2)，最好情况下都是 O(n)，空间复杂度是 O(1)

- 最坏的情况就是第一种，时间复杂度就是 O(n^2)
- 最好的情况就是第二种，加了一个 flag 标志，时间复杂度 O(n)

#### 插入排序
##### 算法思想
1. 从第一个元素开始，该元素可以认为已经被排序
2. 取出下一个元素，在已经排序的元素序列中从后向前扫描
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置
4. 重复步骤 3，直到找到已排序的元素小于或者等于新元素的位置
5. 将新元素插入到该位置后
6. 重复步骤 2 ~ 5

##### 直接插入排序
```
function insertSort(arr) {
    if (!arr || arr.length <= 2) {
      return
    }

    for (let i = 1; i < arr.length; i++) {
      let temp = arr[i]
      let j = i-1
      while(j >= 0 && arr[j] > temp) {
        // 在已排序好的队列中从后向前扫描
        arr[j+1] = arr[j]
        j--
      }
      arr[j+1] = temp
      console.log(`第${i}趟排序后的顺序: ${arr}`)
    }
    return arr
  }

  let sortArr = [3, 6, 4, 2, 11, 10, 5]

  console.log(insertSort(sortArr))  // [ 2, 3, 4, 5, 6, 10, 11 ]
```

- 第1趟排序后的顺序: 3,6,4,2,11,10,5
- 第2趟排序后的顺序: 3,4,6,2,11,10,5
- 第3趟排序后的顺序: 2,3,4,6,11,10,5
- 第4趟排序后的顺序: 2,3,4,6,11,10,5
- 第5趟排序后的顺序: 2,3,4,6,10,11,5
- 第6趟排序后的顺序: 2,3,4,5,6,10,11

##### 二分插入排序
```
function insertSort(arr) {
  for (let i = 1; i < arr.length; i++) {
    let temp = arr[i],
      left = 0,
      right = i - 1
    // 缩小范围
    while (left <= right) {
      // 利用折半查找插入位置
      let middle = parseInt((left + right) / 2)
      if (arr[middle] > temp) {
        // 插入值小于中点值
        right = middle - 1
      } else {
        left = middle + 1
      }
    }
    // left即为找到的要插入的位置，所以下边的循环将left-(i-1)位置的元素依次向后移动
    for (let j = i - 1; j >= left; j--) {
      arr[j + 1] = arr[j]
    }
    arr[left] = temp // 将temp插入到left位置
  }
  return arr
}
var arr = [3, 6, 4, 2, 11, 10, 5]
console.log(insertSort(arr))
```

##### 希尔排序

```
```

##### 时间复杂度和空间复杂度
- 如果序列是完全有序的，插入排序只要比较 n 次，无需移动，时间复杂度为 O(n)
- 如果序列是逆序的，插入排序要比较 O(n^2)次，移动 O(n^2)，时间复杂度为 O(n^2)

总得来说，时间复杂度最好的情况是 O(n),最差的情况是 O(n^2)， 平均复杂度为 O(n^2)

#### 选择排序
##### 算法思想
首先在未排序的数列中找到最小(or 最大)元素，然后将其存放到数列的起始位置；接着，再从剩余未排序的元素中继续寻找最小(or 最大)元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕

```
function selectSort(arr) {
  let minIndex // 无序中最小元素位置
  for (let i = 0; i < arr.length; i++) {
    // 每一趟循环比较时，min用于存放较小元素的数组下标，这样当前批次比较完毕最终存放的就是此趟内最小的元素的下标，避免每次遇到较小元素都要进行交换
    minIndex = i

    //找 "a[i+1]..a[n]" 之间最小元素，并赋给minIndex
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[minIndex]) {
        // 这里 < ，从小到大，如果是 > ，则从大到小
        minIndex = j
      }
    }

    // 如果 minIndex !== i ，交换 arr[i] 和 arr[minIndex]
    // 交换了之后，保证了 arr[0] 到 arr[i] 之间元素有序
    if (minIndex !== i) {
      let temp = arr[minIndex]
      arr[minIndex] = arr[i]
      arr[i] = temp
    }
  }
  return arr
}

console.log(selectSort([20, 40, 30, 10, 60, 50])) // [ 10, 20, 30, 40, 50, 60 ]
```

##### 时间复杂度
选择排序的时间复杂度是 O(N^2) ：假设被排序的数列中有 N 个数。遍历一趟的时间复杂度是 O(N)，需要遍历 N-1 次，因此，选择排序的时间复杂度是 O(N^2) 。无论数组原始排列如何，比较次数是不变的；对于交换操作，在最好情况下也就是数组完全有序的时候，无需任何交换移动

#### 归并排序
##### 算法思想
利用归并的思想实现的排序方法，该算法采用经典的分治法，将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并

```
/**
 * @desc sort，目的是分，不断的分段，再调用merge去合
 */
function sort(arr, low, hight) {
  let middle = parseInt((low + hight) / 2)
  if (low < hight) {
    // 处理左边
    sort(arr, low, middle)
    // 处理右边
    sort(arr, middle + 1, hight)
    // 左右归并
    merge(arr, low, middle, hight)
  }
  return arr
}

/**
 * @desc 合并两个数组
 */
function merge(arr, low, middle, hight) {
  let temp = []
  let i = low, // 指针 i，从左边开始走
    j = middle + 1, // 指针 j，从右边开始走
    k = 0 // temp[k]，目的是为了存放数据

  // 找到最小值放在temp辅助数组中
  while (i <= middle && j <= hight) {
    if (arr[i] < arr[j]) {
      temp[k++] = arr[i++]
    } else {
      temp[k++] = arr[j++]
    }
  }

  // 处理较长部分
  while (i <= middle) {
    temp[k++] = arr[i++]
  }
  while (j <= hight) {
    temp[k++] = arr[j++]
  }

  // 使用temp中的元素覆盖arr中元素
  for (let q = 0; q < temp.length; q++) {
    arr[q + low] = temp[q] // low为传进来的值，这里是为了覆盖arr
  }
}

var arr = [3, 5, 2, 7, 4, 9, 8, 6, 1]
console.log(sort(arr, 0, arr.length - 1))
```

##### 时间复杂度和空间复杂度
归并排序的效率是比较高的，设数列长为 N，将数列分开成小数列一共要 logN 步，每步都是一个合并有序数列的过程，时间复杂度可以记为 O(N)，故一共为 O(NlogN)。因为归并排序每次都是在相邻的数据中进行操作，所以归并排序在 O(NlogN)的几种排序方法（快速排序，归并排序，希尔排序，堆排序）也是效率比较高的。

**时间复杂度无论是在最好情况下还是在最坏情况下均是 O(nlgn)**

**需要一个辅助向量来暂存两有序子文件归并的结果，故其辅助空间复杂度为O(n)**

#### 快速排序（分治法思想）
##### 算法思想
分治法的基本思想是：将原问题分解为若干个规模更小但结构与原问题相似的子问题。递归地解这些子问题，然后将这些子问题的解组合为原问题的解

利用分治法可将快速排序的分为三步：
1. 在数据集之中，选择一个元素作为”基准”（pivot）
2. 所有小于”基准”的元素，都移到”基准”的左边；所有大于”基准”的元素，都移到”基准”的右边。这个操作称为分区操作，分区操作结束后，基准元素所处的位置就是最终排序后它的位置
3. 对”基准”左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止

```
afunction quickSort(arr) {
    // 交换
    function swap(arr, a, b) {
        var temp = arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }

    // 分区
    function partition(arr, left, right) {
        /**
         * 开始时不知最终pivot的存放位置，可以先将pivot交换到后面去
         * 这里直接定义最右边的元素为基准
         */
        var pivot = arr[right];
        /**
         * 存放小于pivot的元素时，是紧挨着上一元素的，否则空隙里存放的可能是大于pivot的元素，
         * 故声明一个storeIndex变量，并初始化为left来依次紧挨着存放小于pivot的元素。
         */
        var storeIndex = left;
        for (var i = left; i < right; i++) {
            if (arr[i] < pivot) {
                /**
                 * 遍历数组，找到小于的pivot的元素，（大于pivot的元素会跳过）
                 * 将循环i次时得到的元素，通过swap交换放到storeIndex处，
                 * 并对storeIndex递增1，表示下一个可能要交换的位置
                 */
                swap(arr, storeIndex, i);
                storeIndex++;
            }
        }
        // 最后： 将pivot交换到storeIndex处，基准元素放置到最终正确位置上
        swap(arr, right, storeIndex);
        return storeIndex;
    }

    function sort(arr, left, right) {
        if (left > right) return;

        var storeIndex = partition(arr, left, right);
        sort(arr, left, storeIndex - 1);
        sort(arr, storeIndex + 1, right);
    }

    sort(arr, 0, arr.length - 1);
    return arr;
}

console.log(quickSort([8, 4, 90, 8, 34, 67, 1, 26, 17]));
```

##### 时间复杂度
最好的情况，时间复杂度为 O(nlog2n), 最差的情况，时间复杂度为 O(n²) ,平均复杂度为 O(nlog2n)

##### 其他的话
快速排序也有不足之处，比如对于元素较少或接近有序的数组来说，快速排序平均性能比插入排序差。这是因为小数组信息熵相对来说比较小（特别是经过一系列的快速排序调用以后），而插入排序在数据接近有序的情况下时间复杂度接近 O(N)，再加上快速排序递归调用也会有一些性能损耗

**因此，针对于小数组的话，可以采用插入排序，如果大数组，就采用快速排序**

Java 标准库自带的排序 DualPivotQuicksort 就是这么干的，INSERTION_SORT_THRESHOLD = 47

那有没有更好能够优化快速排序性能的方法？ 双枢轴，即将数组三切分(大于枢轴，等于枢轴，小于枢轴），为什么这样划分呢？
因为对大规模数组进行排序时，数据重复的情况可能比较多，因此使用双枢轴可以有效避免相等元素之间的比较

快排的改进主要有三种方法：小数组使用插入排序、双枢轴（快速三向切分）、划分策略优化（五取样划分）
经过优化后的快速排序算法时间复杂度可以介于 O(N) 到 O(NlogN) 之间，性能更优

#### 堆排序
##### 算法思想
1. 将待排序序列构造成一个大顶堆，此时，整个序列的最大值就是堆顶的根节点。将其与末尾元素进行交换，此时末尾就为最大值。然后将剩余 n-1 个元素重新构造成一个堆，这样会得到 n 个元素的次小值。如此反复执行，便能得到一个有序序列了
2. 构造初始堆。将给定无序序列构造成一个大顶堆（一般升序采用大顶堆，降序采用小顶堆)
3. 将堆顶元素与末尾元素进行交换，使得末尾元素最大(也就是数组 arr[arr.length-1] 为最大)，然后继续调整堆，再讲堆顶元素与末尾元素交换，得到第二大元素(也就是数组 arr[arr.length-2] 为第二大)，反复交换

```
/**
 * @desc 构建大根堆
 */
function heapSort(arr) {
  // 构建大根堆
  for (let i = parseInt(arr.length / 2) - 1; i >= 0; i--) {
    //从第一个非叶子结点从下至上，从右至左调整结构
    ajustHeap(arr, i, arr.length)
  }

  // 经过上面的 for 循环之后，构建的是一个将 无序数组 arr 构建成了一个大根堆
  console.log(arr) // [ 10, 9, 7, 8, 3, 2, 4, 6, 5, 1 ]

  // 调整堆结构 + 交换堆顶元素与末尾元素
  for (let j = arr.length - 1; j > 0; j--) {
    // swap(arr, 0, j) // 堆顶元素和末尾元素交换
    let temp = arr[0]
    arr[0] = arr[j]
    arr[j] = temp
    ajustHeap(arr, 0, j) // 重新对堆进行调整
  }

  return arr // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
}

/**
 * @desc 调整大顶堆（仅是调整过程，建立在大顶堆已构建的基础上）
 */
function ajustHeap(arr, i, length) {
  let temp = arr[i] // 取出当前的元素
  for (let k = i * 2 + 1; k < length; k = k * 2 + 1) {
    //从i结点的左子结点开始，也就是2i+1处开始
    if (k + 1 < length && arr[k] < arr[k + 1]) {
      //如果左子结点小于右子结点，k指向右子结点
      k++
    }
    if (arr[k] > temp) {
      arr[i] = arr[k]
      i = k
    } else {
      break
    }
  }
  arr[i] = temp // 把temp放在最终的位置
}

var arr = [9, 6, 2, 5, 3, 7, 4, 8, 10, 1]
console.log(heapSort(arr)) // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

##### 时间复杂度和空间复杂度
时间复杂度为: O(logN)，空间复杂度为: O(1)

## 查找算法

#### 顺序查找

##### 说明
顺序查找适合于存储结构为顺序存储或链接存储的线性表

##### 基本思想
顺序查找也称为线形查找，属于无序查找算法。从数据结构线形表的一端开始，顺序扫描，依次将扫描到的结点关键字与给定值k相比较，若相等则表示查找成功；若扫描结束仍没有找到关键字等于k的结点，表示查找失败

```
/**
 * 
 * @param {被查找数组} arr 
 * @param {查找的关键值} value 
 *
 */
function SequenceSearch(arr, value){
    for(let i = 0; i < arr.length; i++){
        if (arr[i] == value){
            return i;
        }
    }
    return - 1;
}
```

##### 时间复杂度
时间复杂度为O(n)

#### 二分查找

##### 基本思想
也称为折半查找————首先要找到一个中间值，通过与中间值比较，大的放又，小的放在左边。再在两边中寻找中间值，持续以上操作，直到找到所在位置为止；找不到返回false

```
// 递归
function binarySearch(data, dest, start, end){
    var end = end || data.length - 1;
    var start = start || 0;
    var m = Math.floor((start + end) / 2);

    //直接命中
    if (data[m] == dest){
        return m;
    }

    if (data[m] > dest){     // 放左
        end = m - 1;
        return binarySearch(data, dest, start, end);
    }else{         // 放右
        start = m + 1;
        return binarySearch(data, dest, start, end);
    }
    return false;
}

// 非递归 用while
//代码中的判断条件必须是while (left <= right)，
//否则的话判断条件不完整，比如：array[3] = {1, 3, 5};
//待查找的键为5，此时在(low < high)条件下就会找不到，因为low和high相等时，指向元素5，但是此时条件不成立，没有进入while()中

function binarySearch2(data, dest){
    var end = data.length - 1;
    var start = 0;
    while(start <= end){
        var m = Math.floor((end + 1) / 2);
        if(data[m] == dest){
            return m;
        }
        if (data[m] > dest){
            end = m - 1;
        }else{
            start = m + 1;
        }
    }
    return false;
}
```

##### 时间复杂度
时间复杂度:O(log2n)

## 另