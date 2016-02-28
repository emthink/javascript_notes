# JavaScript之字符串操作
在各类脚本语言中，对字符串的操作相关的方法都是特别多的，JavaScript也不例外。

## 具体操作方法实现
首先介绍ECMA262v6中计划添加的方法：repeat、startsWith、endsWith、contains。

- contains：判断一个字符串是否包含另一字符串。

正常来说，我们判断一个字符串是否包含另一字符串，可以用正则来实现，但是，如果需要频繁使用该方法，使用正则，性能太差，用字符串原生方法indexOf、search等则效率高很多。

```

    function contains(target, str) {
        return target.indexOf(str) != -1;
    }
```

在实际开发场景中，我们最常见的是判断一个元素的className是否包含某一特定class:

```

    function contains(target, str, sep) {
        return sep ? (sep + target + sep).indexOf(sep + str + sep) > -1 : target.indexOf(str) > -1;
    }
    contains('one two three fote', 'three', ' '); //输出true
    contains('one two three fote', 'threes', ' '); //输出false
```
*HTML元素可以同时添加多个class，以空格分开。*

- startsWith：判断目标字符串是否位于原字符串的开始位置。