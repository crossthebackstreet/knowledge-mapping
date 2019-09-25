#### 排序算法

1.直接插入排序
![直接插入排序]

代码实现
```java
public static int[] insertSort(int[] arrayToSort) {
    int[] array;
    int length;
    if ((array = arrayToSort) == null || (length = array.length) == 0) {
        return null;
    }
    for (int i = 1; i < length; i++) {
        int tmp = array[j - 1];
        for (int j = i; j > 0 && array[j] < tmp; j--) {
            array[j - 1] = array[j];
            array[j] = tmp;
        }
    }
    return array;
}
```

2.希尔排序\
希尔排序，也称递减增量排序算法，1959年Shell发明。是插入排序的一种高速而稳定的改进版本。

代码实现
```java
public static int[] shellSort(int[] arrayToSort) {
    int gap = 1, len = arrays.length;
    while (gap < len / 3)
        gap = gap * 3 + 1;
    for (; gap > 0; gap /= 3) {
        for (int i = gap; i < len; i++) {
            int temp = arrays[i];
            int j = i - gap;
            for (; j >= 0 && arrays[j] > temp; j -= gap)
                arrays[j + gap] = arrays[j];
            arrays[j + gap] = temp;
        }
    }
    return array;
}
```

3.简单选择排序


4.堆排序


5.冒泡排序
![冒泡排序]

代码实现
```java
public static int[] bubbleSort(int[] arrays) {
    for (int i = arrays.length; i >= 0; i--) {
        for (int j = 1; j < i; j++) {
            int tmp = array[j - 1];
            if (arrays[j] < tmp) {
                arrays[j - 1] = array[j];
                arrays[j] = tmp;
            }
        }
    }
    return arrays;
}
```

6.快速排序
![快速排序]

代码实现
```java
public static int[] quickSort(int[] arrays) {
    return quickSort(arrays, 0, arrays.length - 1);
}

public static int[] quickSort(int[] arrays, int left, int right) {
    if (left < right) {
        int pivot = arrays[left];
        int l = left;
        int r = right;
        while (l < r) {
            while (l < r && arrays[r] >= pivot) {
                r--;
            }
            arrays[l] = arrays[r];
            while (l < r && arrays[l] < pivot) {
                l++;
            }
            arrays[r] = arrays[l];
        }
        arrays[l] = pivot;
        quickSort(arrays, left, l - 1);
        quickSort(arrays, r + 1, right);
    }
    return arrays;
}
```

7.归并排序


8.基数排序

[参考: 八大排序算法总结与java实现](https://itimetraveler.github.io/2017/07/18/八大排序算法总结与java实现)

[直接插入排序]:<https://itimetraveler.github.io/gallery/sort-algorithms/insert-sort.gif>
[冒泡排序]:<https://itimetraveler.github.io/gallery/sort-algorithms/bubble-sort.gif>
[快速排序]:<https://itimetraveler.github.io/gallery/sort-algorithms/quick-sort09.gif>