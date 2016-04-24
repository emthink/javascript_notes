# 算法杂谈

- 查找字符串中出现频率最高的字符及其出现次数

```
	
	function countMax(str) {
		var str = str || "";
		var result = {
			char: [],
			count: []			
		};
		var ostr = '', max = 0, ch, cha, len, reg;
		while (str) {
			ostr = str;
			ch = str.substring(0, 1);
			reg = new RegExp(ch, "gm");
			str = str.replace(reg, "");
			len = ostr.length - str.length;
			console.log(ostr, str);
			if (len >= max) {
 				cha = ch;
				max = len; 
			}		
		}
		result.char.push(cha);
		result.count.push(max);
		return result;
	}
```

```

	function countNum(str) {
		var str = str || "";
		var result = {
			char: [],
			count: []			
		};
		var ostr = '', max = 0, ch, len, reg;
		while (str) {
			ostr = str;
			ch = str.substring(0, 1);
			reg = new RegExp(ch, "gm");
			str = str.replace(reg, "");
			len = ostr.length - str.length;
			console.log(ostr, str);
			result.char.push(ch);
			result.count.push(len);		
		}
		return result;
	}
	function quickSort(arr, ch_arr) {
	    	function _sort(left, right) {
			var _left = left, _right = right;
			var temp, ch;
			if (left > right) {
				return;
			}
			temp = arr[left];
			ch = ch_arr[left];
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
					ch = ch_arr[left]
					arr[left] = arr[right];
					ch_arr[left] = ch_arr[right];
					arr[right] = temp;
					ch_arr[right] = ch;
				}
			}
			//将此趟基准数交换至正确位置（小于基准数的数列与大于基准数的数列之间）
			temp = arr[_left];
			ch = ch_arr[_left];
			arr[_left] = arr[left];
			ch_arr[_left] = ch_arr[left];
			arr[left] = temp;
			ch_arr[left] = ch;
			_sort(_left, left - 1);//对分割后的两部分数列递归
			_sort(left + 1, _right);
		}
        	_sort(0, arr.length - 1);//开始快排
		console.log(arr, ch_arr);
		return arr;
	}
	var res = countNum("This is a test string for counting nums");
	quickSort(res.count, res.char);
```