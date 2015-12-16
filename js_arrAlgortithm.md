#JavaScript数组值之排序、去重及最值操作

本篇主要总结常见的排序算法在JavaScript中的实现及js数组取最值，去重，篇幅较长，如有需要可以跳越阅读。

##JavaScript之最值

想一想给你一个数组，让你取最值，你会如何实现呢？这里我们只给出取最大值的实例。

- **遍历**

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

- **apply借用Math.max()**

```

    function max(arr) {
        return Math.max.apply(null, arr);
    } 
```
apply有两个参数,第一个参数为执行环境，一个以数组形式传入参数。

##JavaScript之排序算法

主要介绍最常用的几种排序算法的JavaScript实现：桶排序，冒泡排序，快速排序。

###桶排序

> 将数组分到有限数量的桶里，数组项值对应桶的编号，桶里的值则是其编号对应相等的数组项值出现的次数，然后，根据桶里的值将桶的编号依次输出。

试想一下，你们五个小伙伴考试微积分，分别考了5分，6分，3分，8分，6分，接下来按分数高低进行排名。
如何实现呢？
这里我们只需要一个数组就能轻易解决：

*大数组下标为待排序数组项的值，大数组值为其对应下标在待排序数组中出现次数，默认为0。大数组长度为待排序数组中最大值加1（因为下标从0开始）。*


```

    function barrelSort(arr) {
    	var _arr = [];
		var _max = Math.max.apply(null, arr); //借用取数组最大值
		var _num = _max + 1;
		while((_num--) >= 0) { //生成一个大一维数组，长度为数组最大值+1
			_arr[_num] = 0;//初始化大数组项全为0
		}
		var _len = arr.length;
		while ((_len--) >= 0) {//循环数组
			_arr[arr[_len]]++;//将数组项值对应为大数组下标,并统计数组项值出现次数，存入大数组
		}
		var _item = _max + 1;
		arr = [];//重新生成排序后的数组
		while ((_item--) >= 0)  {//循环大数组
			var k = 1;
			while((k++) <= _arr[_item]) {//统计大数组中出现次数大于0的下标值，并存入数组
				arr.push(_item);
			}
		}
		console.log(arr);
		return arr;
	}
```

- **时间复杂度O(m+n)** 
            
*如代码，第一个while循环m次（m为桶个数，即待排序数组最大值+1），第二个while循环n次(n为待排序数组长度)，最后嵌套while循环m+n次，即先循环大数组，然后只统计大数组中值大于0的对应下标，其实也就等于待排序数组长度，所以整个排序算法一共循环2*（m+n）次，其时间复杂度可为O(m+n)。

###冒泡排序

何谓冒泡排序呢，先来理解一下：

> 重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。因为越大的元素会经由交换慢慢“浮”到数列的顶端，故得名--冒泡。

- **趟** 从0开始循环到数组倒数第二个数，每次循环称为一趟。
- **冒泡** 从第一位数一直循环到倒数第二位数，依次两两比较，一次比较为一次冒泡。

*冒泡排序中一趟循环确定一位数字的正确位置，每趟循环都需要从当前数组序列的第一位数到倒数第二位数依次进行冒泡循环，最终确定排序后的数组序列。*

请看代码：

```

    function bubbleSort(arr) {
    	var _len = arr.length - 1;
		var t;
		for (var i = 0; i < _len; i++) {//从0开始循环到数组倒数第二个数，每次循环称为一趟
			for(var j = 0; j < _len - i; j++) {//从第一位数一直循环到倒数第二位数，每次循环为有若干次冒泡
				if (arr[j] < arr[j + 1]) {//比较两个数大小，根据要求交换位置，即冒泡，结束后进行下一次冒泡
					t = arr[j];
					arr[j] = arr[j + 1];
					arr[j + 1] = t;
				}
			}
		}
		console.log(arr);
		return arr;
	}
```

- **时间复杂度O(N^2)** 

如代码所示，冒泡排序算法只有一个双重嵌套循环，共循环n*n次，其时间复杂度即为O(N^2)。

###快速排序

快速排序，可谓最常用的排序算法，也是目前提到的平均时间复杂度最快的算法，先来了解一下：

> 找一个基准数，通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比基准数要小，另一部分的所有数据都比基准数要大，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

- **基准数** 一个参照数，用来将待排序数列分割成两部分。
- **哨兵** 勘探待排序数组值依次与基准数进行比较的标记，以找到合格数（分别大于和小于基准数的两个数）。
- **趟** 一趟排序找一个基准数，然后选取从左至右与从右至左同时进行勘探。

*一般基准数我们取数组第一位数。两个哨兵分别为待排序数组首尾项。*

直接上代码：

```

    function quickSort(arr) {
    	function _sort(left, right) {
			var _left = left, _right = right;
			var temp;
			if (left > right) {
				return;
			}
			temp = arr[left];
			while (left != right) {
				//从右往左查找
				while (arr[right] >= temp && left < right) {//如果哨兵标记的数大于等于基准数则继续‘向前’，标记下一个数
					right--;
				}
				//从左往右查找
				while (arr[left] <= temp && left < right) {//如果哨兵标记的数小于等于基准数则继续‘向前’，标记下一个数
					left ++;
				}
				//交换两个数位置
				if (left < right) {//两个哨兵均已找到目标数，如果两个哨兵未相遇，则交换当前两个哨兵所指向的目标数
					temp = arr[left];
					arr[left] = arr[right];
					arr[right] = temp;
				}
			}
			//将此趟基准数交换至正确位置（小于基准数的数列与大于基准数的数列之间）
			temp = arr[_left];
			arr[_left] = arr[left];
			arr[left] = temp;
			_sort(_left, left - 1);//对分割后的两部分数列递归
			_sort(left + 1, _right);
		}
        _sort(0, arr.length - 1);//开始快排
		console.log(arr);
		return arr;
	}
```

- **时间复杂度平均O(NlogN)**

如代码示，最坏情况发生时，每次划分过程产生的两个数列分别包含n-1个元素和1个元素，此时相当于有两层嵌套循环，时间复杂度为O(N^2),与冒泡排序一致，而最好情况下，每次划分产生的两个数列长度大致相等，时间复杂度为O(NlogN)。

**尽管快速排序的最坏时间为O(n2)，但就平均性能而言，它是基于关键字比较的内部排序算法中速度最快者，快速排序亦因此而得名。它的平均时间复杂度为O(nlgn)。**

##JavaScript数组去重

关于数组去重，也有很多种方法，在此给出几种效率最高的，借鉴一二。

```
    
    /** 第一种 */
    function uniqueArray(arr) {
        var res = [arr[0]];
        for(var i = 1; i < arr.length; i++) //从第二项开始遍历
    	{
    		//若数组第i项在当前数组中下标不是i，则说明第i项重复，应忽略。否则存入结果数组
    		if (arr.indexOf(arr[i]) == i) ｛
                res.push(arr[i]);
            ｝
    	}
    	return res;
    }

    /** 第二种 */
    function uniqueArray(arr) {
    	var _hash = {}, result = [];
		for (var i = 0; i < arr.length; i++) {
			if (!_hash[arr[i]]) {
				_hash[arr[i]] = true; //将首次遍历到的数组项存入hash对象，效率比indexOf快得多
				result.push(arr[i]);
			}
		}
		return result;
	}
```

总结：此篇虽篇幅略长，但细细读来，想来还是能有些收获，足矣。