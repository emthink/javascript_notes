# CSS过渡、动画、变换

JavaScript是单线程执行的，代码是一句一句执行的，而异步任务，如定时器或者动画，会被压入执行队列，在线程空闲时执行。在CSS动画出现之前，我们通常使用定时器和JavaScript实现动画效果，但是由于JavaScript单线程执行，很容易发生动画卡顿，效果较差。

## 过渡（transition）

CSS过渡在独立于JavaScript执行线程的另一个线程里执行，JavaScript的执行不会阻塞CSS动画，使得应用动画效果更加流畅。CSS过渡语法如下：

```

    transition: [property] [duration] [timing-function] [delay];
```

- property，属性可以是一个单一属性，也可以是一个属性集列表；
- duration，过渡动画持续的时间；
- timing-function，动画执行时间函数；

    - ease-in，动画变化逐渐变快；
    - ease-out，动画变化逐渐变慢；
    -ease-in-out，动画先逐渐变快然后逐渐变慢。

- delay，动画延迟执行时间。