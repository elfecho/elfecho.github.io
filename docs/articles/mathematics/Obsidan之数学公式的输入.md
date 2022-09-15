# 前言：

```
最近在学习专升本的高数，还想继续使用Obsidian作为笔记软件，但是苦于不知道数学公式怎么输入，于是有了这一篇文章😅😎
```

LaTex的语法
注意：这里的数学公式都要在`$在这$` ，或者`$$在这$$`

先说下怎么换行

```
$$
\begin{aligned}a=b+c \\ b=c-a \\ c=a+b \end{aligned}
$$
```
$$
\begin{aligned}a=b+c\\b=c-a\\c=a+b \end{aligned}
$$


```
$$ 
\begin{matrix}已知y=\sqrt{x+3}&&(x>=0)\\求y的最大值是多少 \end{matrix}
$$
```
$$
\begin{matrix}已知y=\sqrt{x+3}&&(x>=0)\\求y的最大值是多少 \end{matrix}
$$

```
$$
 \begin{vmatrix}
 0&1&2\\
 3&4&5\\
 6&7&8\\
 \end{vmatrix}
$$
```

$$
 \begin{vmatrix}
 0&1&2\\
 3&4&5\\
 6&7&8\\
 \end{vmatrix}
$$


 ```
 $$
 \begin{Vmatrix}
 0&1&2\\
 3&4&5\\
 6&7&8\\
 \end{Vmatrix}
$$
 ```

$$
 \begin{Vmatrix}
 0&1&2\\
 3&4&5\\
 6&7&8\\
 \end{Vmatrix}
$$

- 希腊字母

α \alphaα、β \betaβ、χ \chiχ、Δ \DeltaΔ、Γ \GammaΓ、Θ \ThetaΘ 之类的

![](https://img-blog.csdnimg.cn/img_convert/54b67772e9ccb1e9242f9c05944f4561.jpeg)

一些数学结构

![](https://img-blog.csdnimg.cn/img_convert/c29930fba2831080025bfe93ef1b4e25.jpeg)

效果如下：
```
$\frac{123}{999}$、$\sqrt[n]{abc}$、$\frac{\sqrt{234}}{\sqrt[n]{abc}}$、$\underrightarrow{abc}$、$\overrightarrow{abc}$
```
$\frac{123}{999}$、$\sqrt[n]{abc}$、$\frac{\sqrt{234}}{\sqrt[n]{abc}}$、$\underrightarrow{abc}$、$\overrightarrow{abc}$



- 插入定界符


效果如下
```
$|$、$\|$、$\Uparrow$、$\{\}$
```

$|$、$\|$、$\Uparrow$、$\{\}$

- 插入一些可变大小的符号

![](https://img-blog.csdnimg.cn/img_convert/cb6a9862d5fca7af9718b4a6f8b2ab95.png)


效果如下：
```
$\sum$、$\int$、$\oint$、$\iint$、$\bigcap\bigcup\bigoplus\bigotimes$
```

$\sum$、$\int$、$\oint$、$\iint$、$\bigcap\bigcup\bigoplus\bigotimes$



- 插入一些函数名称

![](https://img-blog.csdnimg.cn/img_convert/e59f97f3b96d7f7d4c6b7d29eef09c10.jpeg)

效果如下：

```
$\sin$、$\cos$、$\tan$、$\log$、 $\tan(at-n\pi)$
```

$\sin$、$\cos$、$\tan$、$\log$、 $\tan(at-n\pi)$


- 关系运算符和二进制运算符

![](https://img-blog.csdnimg.cn/img_convert/0048bd339dccf25bf152126d9ec0fe1b.jpeg)

效果如下：
```
$\times$、$\ast$、$\div$、$\pm$、$\leq$、$\geq$、$\neq$、$\thickapprox$、$\sqsupset$、$\subset$、$\supseteq$、$\sqsupset$、$\sqsupseteq$、$\in$
```
$\times$、$\ast$、$\div$、$\pm$、$\leq$、$\geq$、$\neq$、$\thickapprox$、$\sqsupset$、$\subset$、$\supseteq$、$\sqsupset$、$\sqsupseteq$、$\in$


- 插入箭头符号

![](https://img-blog.csdnimg.cn/img_convert/0eb121c8d0a15ec5c09a5d38db1c7a0c.jpeg)

效果如下：

```
$\leftarrow$、$\Leftarrow$、$\nLeftarrow$、$\rightleftarrows$
```

$\leftarrow$、$\Leftarrow$、$\nLeftarrow$、$\rightleftarrows$

- 其他符号

![](https://img-blog.csdnimg.cn/img_convert/535defcb0a558d6ca7bd45628cb1472d.jpeg)

- 效果如下

```
$\infty$、$\angle$、$\int$、$\triangle$、$\square$
```

$\infty$、$\angle$、$\int$、$\triangle$、$\square$

- 插入上下标

用`^`表示上标，用`_`表示下标记

效果如下：

```
$$ sin ⁡ 2 ( θ ) + cos ⁡ 2 ( θ ) = 1 \sin^2(\theta) + \cos^2(\theta) = 1 $$
```
$$ sin ⁡ 2 ( θ ) + cos ⁡ 2 ( θ ) = 1 \sin^2(\theta) + \cos^2(\theta) = 1 $$ 
```
$$∑ n = 1 ∞ k \sum_{n=1}^\infty k$$
```
$$∑ n = 1 ∞ k \sum_{n=1}^\infty k$$
```
$$∫ a b f ( x )   d x \int_a^bf(x)\,dx$$
```
$$∫ a b f ( x )   d x \int_a^bf(x)\,dx$$
```
$$lim ⁡ x → ∞ exp ⁡ ( − x ) = 0 \lim\limits_{x\to\infty}\exp(-x) = 0$$
```
$$lim ⁡ x → ∞ exp ⁡ ( − x ) = 0 \lim\limits_{x\to\infty}\exp(-x) = 0$$

注意：`\,`在积分里的作用是为了增加些许间距，`\!`会减少一些间距。

输出分段函数
用`\begin{cases}`和`\end{cases}`来构造分段函数，中间则用`\\`来分段

```
$$
f(x) = \begin{cases} 2x, x>0 
\\ 3x,x≤0 \end{cases}
$$
```
$$
f(x) = \begin{cases} 2x, x>0 
\\ 3x,x≤0 \end{cases}
$$

 

一些常见的数学公式
```
$$
 f'(x) = x^2 + x
$$
```
$$
 f'(x) = x^2 + x
$$
```
$$
 \lim_{x\to0}\frac{9x^5+7x^3}{x^2+6x^8}
$$
```
$$
 \lim_{x\to0}\frac{9x^5+7x^3}{x^2+6x^8}
$$

```
$$
 \int_a^b f(x)\,dx
$$
```
$$
 \int_a^b f(x)\,dx
$$
```
$$
 \int_0^{+\infty}f(x)\,dx
$$
```
$$
 \int_0^{+\infty}f(x)\,dx
$$
```
$$
 \int_{x^2+y^2\leq R^2} \,f(x,y)\,dx\,dy = \int_{\theta=0}^{2\pi}\int_{r=0}^R \,f(r\cos\theta,r\sin\theta)\,r\,dr\,d\theta
$$
```
$$
 \int_{x^2+y^2\leq R^2} \,f(x,y)\,dx\,dy = \int_{\theta=0}^{2\pi}\int_{r=0}^R \,f(r\cos\theta,r\sin\theta)\,r\,dr\,d\theta
$$

```
$$
 \int\!\!\!\int_D f(x,y)dxdy
$$
```
$$
 \int\!\!\!\int_D f(x,y)dxdy
$$