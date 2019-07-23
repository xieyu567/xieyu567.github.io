---
title: Transformer
date: 2019-06-27 14:07:58
categories: NLP
visitors: 
mathjax: true
tags: machine learning
---
&emsp;&emsp;这是2017年Google Brain的一篇论文,使用self-attention机制构建模型.和之前完全不同的思路,
- RNN因为是递归式的结构,无法做到并行,而且要获得全局信息必须要做双向RNN.
- CNN则是因为感受域限制,只能获取局部信息.
- Transformer能够一步获取全局信息,在做NLP任务时表现很优异,如共指消解.

&emsp;&emsp;Transformer使用传统的Encoder-Decoder结构,给定一个序列$(x_1,...,x_n)$经过处理得到$(y_1,...,y_m)$.模型的每一步都是自回归的,每次生成都使用之前的生成作为附加信息输入.

<img src="http://ww1.sinaimg.cn/large/006tNc79gy1g4fpkw99egg30m80jo4ni.gif" width="80%" height="80%">

<img src="http://ww4.sinaimg.cn/large/006tNc79gy1g4fpbsaicwj30ng0ystfy.jpg" width="80%" height="80%" aligns=center>

{% codeblock lang:python %}
class EncoderDecoder(nn.Module):
    def __init__(self, encoder, decoder, src_embed, tgt_embed, generator):
        super(EncoderDecoder, self).__init__()
        self.encoder = encoder
        self.decoder = decoder
        self.src_embed = src_embed
        self.tgt_embed = tgt_embed
        self.generator = generator

    def forward(self, src, tgt, src_mask, tgt_mask):
        return self.decode(self.encode(src, src_mask), src_mask, tgt, tgt_mask)

    def encode(self, src, src_mask):
        return self.encode(self.src_embed(src), src_mask)

    def decode(self, memory, src_mask, tgt, tgt_mask):
        return self.decoder(self.tgt_embed(tgt), memory, src_mask, tgt_mask)
{% endcodeblock %}

这是构建EncoderDecoder框架,注意到encode(src, src_mask)就是memory,也就是用encode的输出作为了Decode的输入,图上两个部分相连的那条线.

{% codeblock lang:python %}
class Generator(nn.Module):
    def __init__(self, d_model, vocab):
        super(Generator, self).__init__()
        self.proj = nn.Linear(d_model, vocab)

    def forward(self, x):
        return F.log_softmax(self.proj(x), dim=-1)
{% endcodeblock %}

&emsp;&emsp;这里是定义一个标准的线性然后softmax的标准生成步骤.因为之前没有用过pytorch,所以还是记录一下用到的内置函数. 

- $nn.Linear()$函数是用来做线性变化的,输入一个维度为$m\times n$的矩阵,经过线性变化$nn.Linear(n,t,bias=None)$得到的输入维度为$m\times t$.具体的实现是输入矩阵乘$t\times n$矩阵再加上一个维度为$m$的$\text{bias}$.
$$y=xA^T+\text{bias}$$
- $\text{F.log\_softmax()}$函数就是在softmax函数外面再加一个$\text{log}$函数.需要注意的是$\text{dim}$的指定,以二维函数为例,取$\text{dim}=1$时,是对每一行进行softmax,而取0时是每一列进行计算.可以理解为沿着某条轴,将数据集中到轴上.
$$\log(\text{Softmax}(x_{i})) = \log(\frac{\exp(x_i) }{ \sum_j \exp(x_j)})$$

---
接下来是Encoder部分
```python
def clones(module, N):
    return nn.ModuleList([copy.deepcopy(module) for _ in range(N)])
```
&emsp;&emsp;首先定义一个clone方法,因为Encoder是由多个相同的层构成的.这里设$N=6$
```python
class Encoder(nn.Module):
    def __init__(self, layer, N):
        super(Encoder, self).__init__()
        self.layers = clones(layer, N)
        self.norm = LayerNorm(layer.size)

    def forward(self, x, mask):
        for layer in self.layers:
            x = layer(x, mask)
        return self.norm(x)

class LayerNorm(nn.Module):
    def __init__(self, features, eps=1e-6):
        super(LayerNorm, self).__init__()
        self.a_2 = nn.Parameter(torch.ones(features))
        self.b_2 = nn.Parameter(torch.zeros(features))
        self.eps = eps

    def forward(self, x):
        mean = x.mean(-1, keepdim=True)
        std = x.std(-1, keepdim=True)
        return self.a_2 * (x - mean) / (std + self.eps) + self.b_2
```
&emsp;&emsp;然后定义Encoder和LN模块.LayerNorm本来torch里也有内置函数.这里是自己实现的.
$$y=\frac{x-E[x]}{\sqrt{Var[x]+\epsilon}}\times \gamma +\beta$$
&emsp;&emsp;其中$\gamma$和$\beta$是参数,$\epsilon$是一个很小数,torch中取$e^{-5}$,为了保持数值的稳定性,防止分母趋近0.

&emsp;&emsp;对应到图上就是$\text{Add\&Norm}$这一块,对于每个sublayer有$LayerNorm(x+Sublayer(x))$.具体是$dropout\rightarrow sublayer的input相加\rightarrow Normalized$.但是在实际编码中,将Norm放到了dropout前面.
```python
class SublayerConnection(nn.Module):
    def __init__(self, size, dropout):
        super(SublayerConnection, self).__init__()
        self.norm = LayerNorm(size)
        self.dropout = nn.Dropout(dropout)

    def forward(self, x, sublayer):
        return x + self.dropout(sublayer(self.norm(x)))
```
&emsp;&emsp;定义sublayer的残差连接.残差连接实际上就是$f(x)+x$,目的是为了反向传播的时候,梯度连乘,不会造成梯度消失.因为会出现常数项.可以看到是先normalizion再dropout.
```python
class EncoderLayer(nn.Module):
    def __init__(self, size, self_attn, feed_forward, dropout):
        super(EncoderLayer, self).__init__()
        self.self_attn = self_attn
        self.feed_forward = feed_forward
        self.sublayer = clones(SublayerConnection(size, dropout), 2)
        self.size = size

    def forward(self, x, mask):
        x = self.sublayer[0](x, lambda x: self.self_attn(x, x, x, mask))
        return self.sublayer[1](x, self.feed_forward)
```
&emsp;&emsp;Encoder每层有两个sublayer,第一个sublayer[0]用多头注意力机制,第二个sublayer[1]用位置全连接前馈网络.

---
然后是Decoder层
```python
class Decoder(nn.Module):
    def __init__(self, layer, N):
        super(Decoder, self).__init__()
        self.layers = clones(layer, N)
        self.norm = LayerNorm(layer.size)

    def forward(self, x, memory, src_mask, tgt_mask):
        for layer in self.layers:
            x = layer(x, memory, src_mask, tgt_mask)
        return self.norm(x)
```
和Encoder一样,设计为6个相同的层.
```python
class DecoderLayer(nn.Module):
    def __init__(self, size, self_attn, src_attn, feed_forward, dropout):
        super(DecoderLayer, self).__init__()
        self.size = size
        self.self_attn = self_attn
        self.src_attn = src_attn
        self.feed_forward = feed_forward
        self.sublayer = clones(SublayerConnection(size, dropout), 3)

    def forward(self, x, memory, src_mask, tgt_mask):
        m = memory
        x = self.sublayer[0](x, lambda x: self.self_attn(x, x, x, tgt_mask))
        x = self.sublayer[1](x, lambda x: self.src_attn(x, m, m, src_mask))
        return self.sublayer[2](x, self.feed_forward)
```
&emsp;&emsp;Decoder的sublayer有三个,其中sublayer[0]和Encoder一样用自注意力机制,sublayer[2]同样是前馈网络,sublayer[1]是对Encoder的输出用多头注意力机制.残差网络和Norm和Encoder一样.
```python
def subsequent_mask(size):
    attn_shape = (1, size, size)
    subsequent_mask = np.triu(np.ones(attn_shape), k=1).astype('uint8')
    return torch.from_numpy(subsequent_mask) == 0
```
&emsp;&emsp;定义subsequent_mask方法,为了在Decoder时遮盖当前位置后的内容. $np.triu$方法是取上三角矩阵,$k$为偏移量,正数时向上偏移,比如有矩阵$\begin{vmatrix} 1&2 \\ 2&3 \\ 3&4 \end{vmatrix}$, 其上三角矩阵为$\begin{vmatrix} 1&2 \\ 0&3 \\ 0&0 \end{vmatrix}$, $k=1$时为$\begin{vmatrix} 0&2 \\ 0&0 \\ 0&0 \end{vmatrix}$.最后返回的是取反后的矩阵.也就是上三角为0.

---
接下来是attent模块.