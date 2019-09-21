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
        for (int j = i; j > 0 && array[j] < array[j - 1]; j--) {
            array[j] ^= array[j - 1];
            array[j - 1] ^= array[j];
            array[j] ^= array[j - 1];
        }
    }
    return array;
}
```

2.希尔排序\
希尔排序，也称递减增量排序算法，1959年Shell发明。是插入排序的一种高速而稳定的改进版本。\
代码实现
```java
public static int[] shellSort(int[] arrayToSort) {
    int[] array;
    int length;
    if ((array = arrayToSort) == null || (length = array.length) == 0) {
        return null;
    }
    for (int gap = length >> 1; gap > 0; gap >>= 1) {
        for (int i = gap; i < length; i += gap) {
            for (int j = i; j > 0 && array[j] < array[j - gap]; j -= gap) {
                array[j] ^= array[j - gap];
                array[j - gap] ^= array[j];
                array[j] ^= array[j - gap];
            }
        }
    }
    return array;
}
```

3.简单选择排序


4.堆排序


5.冒泡排序


6.快速排序


7.归并排序


8.基数排序

[参考: 八大排序算法总结与java实现](https://itimetraveler.github.io/2017/07/18/八大排序算法总结与java实现)

[直接插入排序]:<https://itimetraveler.github.io/gallery/sort-algorithms/insert-sort.gif>