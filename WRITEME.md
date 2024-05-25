#### 记录一下学习rtl-sdr的过程中的问题和解决过程。有的时候想的太多，连出发的勇气都没有了。
    
    Just-do-it.

---

1. 市面上有很多sdr设备，包括rtl-sdr, hackrf等，但从经济角度来看，似乎rtl-sdr是最划算的

    + 淘宝买的话大概花了100左右

2. gnuradio 中的 Signal Source
    + [Signal Source](https://wiki.gnuradio.org/index.php/Signal_Source)
    
    这里在参考官网给出的描述来看, 用sin举例, 使用$f$记作Frequency, 使用 $s$记为Sample_rate:

    $$output = A sin(2\pi * f / s * n) + offset $$

    对应的数学描述是，对一个频率为$f$的连续信号的，使用离散频率为$s$的冲击采样。参考信号与系统 7.1.1：

    $$
    x_p(t) = \Sigma_{-\infty}^{+\infty}  x(nT) \delta(t-nT)
    $$

    所以比如将 x=sin 带进取就有source的那个结果了, 所以也没有那么高深。

    有一个不理解的地方是，如果我们串连信号源-throttle-sink显示，最终显示的结果是以ms为单位的，为什么不是0-1-2-3的采样次数的数字呢？

    后来把图片放大之后可以发现，每两个有效点的$\Delta t$大约是0.03ms， 正好对应我的采样频率 1/32k， 所以这里其实是把$n/s$当作了实际的横坐标用来标记时间。

    还想看一下源代码，发现看不懂。以后再来看吧


3. gnuradio 中的 Throttle
    
    这里按照官网的意思是，限制后面的采样频率。

    > Throttle flow of samples such that the average rate does not exceed the specific rate (in samples per second).

    其实也可以看到，不管是source 还是gui-time-sink 还是throttle 都有sample_rate这个参数，因此猜测的话，throttle可以限制后面的块的采样频率不能大与前面的采样频率。

    > Output connected:
    
    > If you use the Throttle "in line", i.e. in between a block that produces samples, and one that consumes samples, with both its input and its output connected, it will copy the input to the output. It does so in chunks of multiple samples, while waiting enough time before telling GNU Radio the samples are consumed on its input and produced on its output, to achieve the desired average rate of items per second.

    google了一下，好像没我想的这么简单。

4. 流程图运行逻辑
    
    其实这种流程图画出来之后，如果只从全局来看，似乎是数据会被不断的处理。但是如果考虑具体的实现，并且如果复杂起来的话，流程图背后的各种串/并联关系，数据的同步和调度具体是怎么做的，可能要仔细看看官网和源码才能知道了。
