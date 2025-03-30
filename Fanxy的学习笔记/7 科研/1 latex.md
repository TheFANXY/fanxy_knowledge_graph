# `Latex` 公式

## 1. 乘法

点乘：a \cdot b

叉乘：a \times b

除以：a \div b



## 2. 分式和根式

分式使用 `\frac{分子}{分母}`来书写。分式的大小在行间公式中是正常大小，而在行内被极度压缩。amsmath 提供了方便的命令display(生成较大的)`\dfrac` （display fraction）和text(生成较小的) `\tfrac`（text fraction），令用户能够在行内使用正常大小的分式，或是反过来。



`frac` $\frac{爸爸}{儿子}$		`dfrac`	$\dfrac{分子}{分母}$     `tfrac`   $\tfrac{分子}{分母}$



一般的根式使用(square root)`\sqrt{...}`；表示 n 次方根时写成 `\sqrt[n]{...}`。

`sqrt`  $\sqrt{{\dfrac{1}{n}}}$           `sqrt[n]`   $\sqrt[5]{{\dfrac{1}{n}}}$ 



而表达指数次方 可以这么表示  `x^{2}`		$x^{2}$   

当然也可直接使用上下标   `_` `^`