---
title: Why Functional Programming?
date: 2017-08-17 23:15:35
tags:
---

![Why Functional Programming?](https://lh3.googleusercontent.com/FXZ-mN3B3uKCrN4aazrmNfvxCeNHCibi0FZ4Fp6c2xoQQlUQZtQS7Jzm6iQVizb17iA8ydDQ8vW3c4QLv5bfuh4kZE2b_HW5VK48m3Gan4B1VbzOkcJ_mEu8Bb7oo7dheTWrk2olKoax3UVS5Zh3QVehiquSVWZfTMeiH7AHuL5F_MqnwE7__fgEezNZxzyHCA7ZKY3SF82CYciTyw3Yb_FaE4I88lXICm-wyYrmhXgtB36VamFsEFeiZHvLwWnJGxaBWPYqyC4Yz2_Q2jnM3_3LVYbb5aLRbAikJpkXkE7td58BlcBBqwZz0tQMCpcwJeWjAGviZu3sv7Hiz9m_EIk2dSCq2VhVaouiD9mW8lCTvHOh9rAQJhxGYCCsf92uoNyN1rXMdEfN2zkk0HnGcEVGraMD8-ZRfvPTH5v1CuoLBIcnLy7eRAIewlPXSBtkGyQB-Qjt3cQ3tiAoR8XOs5bxo4IumjCCBgInj-67eJZiymGysNJ1JkA4U_c4LQlxO9xKDRLEf56OVevEs4rncS2dUeYt5lg4bUsE-ihF8Pa9h0_YmQURg2hJU7KRtK_kk4ON5YLXmip7ITOnbB2sUVVLWr7UysRpTqfAwIfTwBHVPkjzDR748A=w402-h284-no "Why Functional Programming?")

Why Static Type?
----------------

* 性能 - 方法调用速度更快，因为不需要在运行时才来判断调用的是哪个方法。
* 可靠性 - 编译器验证了程序的正确性，因而运行时崩溃的概率更低。
* 可维护性 - 陌生代码更容易维护，因为你可以看到代码中用到的对象的类型。
* 工具支持 - 静态类型使 IDE 能提供可靠的重构、精确的代码补全以及其他特性。

Benefit of Functional Programming
---------------------------------

* 头等函数 - 把函数（一小段行为）当作值使用，可以用变量保存它，把它当作参数传递，或者当作其他函数的返回值。
* 不可变性 - 使用不可变对象，这保证了它们的状态在其创建之后不能再变化。
* 无副作用 - 使用的是纯函数。此类函数在输入相同时会产生同样的结果，并且不会修改其他对象的状态，也不会和外面的世界交互。

![Moving Parts](https://lh3.googleusercontent.com/0zedjH2HXa0X9-ycI4v4_TkXaSI1gpRi792Ns1jQRY1iClfFMqtQ23gxP90wbhB-U8YosTXcNwXFWA-M7HRp9Uc9scHkNW_CaMoojShIIZ9DhescUF83ctzVEacIJKsG5aFLuGDJbhSqsXOTFVacsrsGph4UVNOB7hmR8_bSjX9VSS1dDAmXJnUQgWr-zPBuIx9Bygx2yAMZDZeZoIgp3MJa2e_rOCowuKGCqtLRtqckh_F_UoeUm8QzJFx989QRF1my42oJ6Su5-i5YBEDYjtuJuhDZzr9-wKZUvCwBKGgXw5u5hHKvQLq6otLywNmFaZ451s_dVq-87rWBog3-WndY46hjOJT6LkO5nNZBukD9SCl6bmgCOsXHvLlcFBSP6oD_ME9E2yV9M76NpNfJ-rUL-x5IuVeAtEABZ-Ybj2kOsNwy5_o8_MkK3ZDoYQ76jRH5p8f-zf8tUlY2hmaIG0kSzmHGjCJiVFuKQiJzmVjNIcCQbI2NsXZ83mC4tZ-fyizu0MswpAhK5o0Ov56eUjDoGI7ub4X58v_YgC_x6_YIYzPyCyqNljhIH0OPHzrC1L_R3zwXsqZCvzaCrAZ6srVmBCE9SSMf0BgCR3-LozUuhk3A3vTPvw=w500-h315-no "Moving Parts")

* 简洁

函数式风格的代码 比相应的命令式风格的代码更优雅、更简练，因为把函数当作值可以让你获得更强大的抽象能力，从而避免重复代码。

假设你有两段类似的代码，实现相似的任务但具体细节略有不同，可以轻易地将这段逻辑中公共的部分提取到一个函数中，并将其他不同的部分作为参数传递给它。这些参数本身也是函数，但你可以使用一种简洁的语法来表示这些匿名函数，被称作 lambda 表达式。

* 多线程安全

多线程程序中最大的错误来源之一就是，在没有采用适当同步机制的情况下，在不同的线程上修改同一份数据。如果你使用的是不可变数据结构和纯函数，就能保证这样不安全的修改根本不会发生，也就不需要考虑为其设计复杂的同步方案。

* 测试更加容易

没有副作用的函数可以独立地进行测试，因为不需要写大量的设置代码来构造它们所依赖的整个环境。


References
----------

* Kotlin 初体验：主要特征与应用, http://geek.csdn.net/news/detail/231497
