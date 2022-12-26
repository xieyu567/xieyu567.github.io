---
title: FP中一些抽象代数的概念
mathjax: true
date: 2022-12-26 16:23:54
updated: 2022-12-26 16:23:54
categories: Functional Programming
tags:
---

#### Magma

原群是在二元运算下封闭的集合。举一个例子

$$ M = \{true, false\} \ and \ \bullet \ = \&\& $$
集合$M$是布尔值在$\&\&$运算下的集合，很显然这个集合是封闭的，$true$和$false$任意组合进行$\&\&$运算，结果仍为$true$或$false$。
原群的数学表达为
$$ \forall a, b \in M \Rightarrow a \cdot b \in M $$
其中$M$就是原群

<!--more-->
### Semigroup

半群就是二元运算满足结合律的原群。数学表达为
$$ \forall a, b, c \in S \Rightarrow a \cdot (b \cdot c) = (a \cdot b) \cdot c $$
常见的字符串拼接操作符在非空字符串集合可以看作是半群，比如
$$ ("ab" <> "bc") <> "cd" = "ab" <> ("bc" <> "cd")$$

#### Monoid

幺半群就是在半群的基础上再添加一个幺元。数学表达为
$$ e \in M, \forall a \in M \Rightarrow a \cdot e = e \cdot a = a $$
幺元最简单的例子就是自然数集合中，乘法之于1，加法之于0。另外上面的字符串拼接的例子，补上空字符串就构成了幺半群。再举一个例子
$$ M = \{true, false\} \ and \ \bullet = ||, e = false$$
$$
\begin{align}
&true || false = true   &(封闭性)\\
&true || (true || false) = (true || true) || false &(运算结合性)\\
&false || true = true || false = true &(幺元)
\end{align}
$$
计算机中的Monoid和数学中的Monoid是同构的，实际上就是类型，比如提到的字符串、数组、整型等都是幺半群。

#### Group

群是在幺半群的基础上在加上逆元的约束，每个元素都有一个唯一的逆元。数学表达为
$$ \forall a \in G, a^\prime \in G, e \in G \Rightarrow a \cdot a^\prime = a^\prime \cdot a = e $$
最简单的例子是自然数加法中的相反数和乘法中的倒数。有一点需要注意的就是幺元的逆元是自身。
计算机中加法的模运算就是个一个群，可以参考[模运算的wiki](https://en.wikipedia.org/wiki/Modular_arithmetic#:~:text=In%20mathematics%2C%20modular%20arithmetic%20is,Disquisitiones%20Arithmeticae%2C%20published%20in%201801)

#### Abelian Group

阿贝尔群是在群的基础上加交换律的约束。数学表达为
$$ \forall a \in A, b \in A \Rightarrow a \cdot b = b \cdot a $$
在purescript中其实也就是简单加了个约束，下面两个类型签名是等价的
```purescript
find :: \forall a. Abelian a (Set a -> Maybe a)
find :: \forall a. Group a => Commutative a => Set a -> Maybe a
```

#### Semiring

半环是一种代数结构，包含集合$R$和两个二元运算符$+$和$\bullet$。
在repl中我们可以看到加法和乘法的类型签名
```purescript
> :t (*)
forall a. Semiring a => a -> a -> a

> :t (+)
forall a. Semiring a => a -> a -> a
```
另外还有四个约束条件，具体可以查看[半环的wiki](https://en.wikipedia.org/wiki/Semiring#:~:text=In%20abstract%20algebra%2C%20a%20semiring,must%20have%20an%20additive%20inverse.)。这里需要注意的是wiki中的+，$\bullet$，0，1，都是抽象的，当然我们可以具体的带入加法和乘法，但是它们可以为任意函数，0只是代表运算符$+$的幺元，1代表运算符$\bullet$的幺元。
$$
\begin{align}
&(R, +)\ is\ a\ Commutative\ Monoid\ with\ Identity\ of\ 0: \\
\\
&(a + b) + c = a + (b + c) &[Associativity]\ (Semigroup)\\
&0 + a = a + 0 = a &[Identity]\ (Monoid)\\
&a + b = b + a &[Commutative]\ (Commutative Monoid)
\end{align}
$$
$$
\begin{align}
&(R, \cdot)\ is\ a\ Monoid\ with\ Identity\ of\ 1: \\
\\
&(a \cdot b) \cdot c = a \cdot (b \cdot c) &[Associativity]\ (Semigroup)\\
&1 \cdot a = a \cdot 1 = a &[Identity]\ (Monoid)
\end{align}
$$
$$
\begin{align}
&a \cdot (b + c) = (a \cdot b) + (a \cdot c) &[Left Distributivity]\\
&(a + b) \cdot c = (a \cdot c) + (b \cdot c) &[Right Distributivity]
\end{align}
$$
$$
a \cdot 0 = 0 \cdot a = 0 \qquad\qquad [Annihilation]
$$
我们再看一下半环的类型签名
```purescript
class Semiring a where
  add :: a -> a -> a
  zero :: a
  mul :: a -> a -> a
  one :: a
```
还有Int的实现
```purescript
instance semiringInt :: Semiring Int where
  add = intAdd
  zero = 0
  mul = intMul
  one = 1

foreign import intAdd :: Int -> Int -> Int
foreign import intMul :: Int -> Int -> Int
```

#### Ring

环在半环的基础上添加一个加法逆元的约束。
$$
a + (-a) = 0  \qquad\qquad [Additive\ Inverse]
$$
也就是说$(R, +)$从可交换幺半群变成了可交换群，不仅可以做加法操作还可以进行减法操作。
看一下类型签名和Int的实现
```purescript
class Semiring a <= Ring a where
  sub :: a -> a -> a

instance ringInt :: Ring Int where
  sub = intSub
```

#### Commutative Ring & Euclidean Ring

再回顾一下半环的约束条件，只有对于$(R, +)$是可交换的，交换环增加了对$(R, \cdot)$可交换的约束。实际上在purescript中交换环仅仅是个声明，没有额外添加约束，它的类型签名如下
```purescript
class Ring a <= CommutativeRing a
```
欧式环是用来支持除法的交换环，在交换环的基础上添加了三个约束。
第一个约束是Integral domain。任意两个非零元素乘积不为零。
$$
\forall a \in R, b \in R, witch\ a \ne 0\ and\ b \ne 0 \Rightarrow a \cdot b \ne 0
$$
第二个约束是Euclidean function degree
$$
\forall a \in R, which\ a \ne 0 \Rightarrow degree\ a >= 0
$$
$$
\begin{equation}
\left.\begin{aligned}
\forall a, b \in R, which\ b \ne 0 \\
q = a / b \\
r = a\ mod\ b \\
\end{aligned}
\right \}
\Rightarrow a = q \ast b + r, which\ degree\ r = 0\ or\ degree\ r < b  
\end{equation}
$$
第三个约束是Submultiplicative euclidean function
$$
\forall a \in R, b \in R, witch\ a \ne 0\ and\ b \ne 0 \Rightarrow degree\ a \leq degree\ (a \ast b)
$$
最后看一下欧式环的类型签名
```purescript
class CommutativeRing a <= EuclideanRing a where
  degree :: a -> Int
  div :: a -> a -> a
  mod :: a -> a -> a
```
