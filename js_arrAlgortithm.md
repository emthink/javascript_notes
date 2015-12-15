#JavaScript数组值之多样操作

本篇主要总结常见的排序算法在JavaScript中的实现及js数组取最值，去重。

##JavaScript之最值

想一想给你一个数组，让你取最值，你会如何实现呢？这里我们只给出取最大值的实例。

###遍历

上代码：
```

    function max(arr) {
        var temp = arr[0];
        for (var i = 1; i < arr.length; i++) {
            if (arr[i] > temp) {
                temp = arr[i];
            }
        }
        return temp;
    }
```

###apply借用Math.max()

```
    function max(arr) {
        return Math.max.apply(null, arr);
    } 
```


##JavaScript之排序算法

主要介绍最常用的几种排序算法的JavaScript实现：桶排序，冒泡排序，快速排序。

###桶排序

试想一下，你们五个小伙伴考试微积分，分别考了5分，6分，3分，8分，6分，接下来按分数高低进行排名。
如何实现呢？
这里我们只需要一个1数组就能轻易解决：

```

    function oSort(arr) {
        var _max = Math.max(arr);
    }
```