下面介绍反向传播基于的四个方程。

### 1. 输出层误差的方程

第$$L$$层为输出层，该层得分误差向量$$\delta^L$$中每一个元素定义如下：


$$
\delta^L_j= \frac{\partial C}{\partial z^L_j}=\frac{\partial C}{\partial a^L_j}\sigma'(z^L_j)
$$


右式第一项$$\frac{\partial C}{\partial a^L_j}$$表示代价随着$$j^{th}$$输出激活值的变化而变化的速度，第二项$$\sigma'(z^L_j)$$刻画了在$$z^L_j$$处激活函数$$\sigma$$的变化速度。

证明：应用链式法则我们可以就输出激活值的偏导数的形式重新表示上面的偏导数：


$$
\delta^L_j= \frac{\partial C}{\partial z^L_j}=\displaystyle\sum_{k}\frac{\partial C}{\partial a^L_k}\frac{\partial a^L_k}{\partial z^L_j}
$$


这里的求和是在输出层的所有神经元上运行的，当$$k \ne j$$时$$\frac{\partial a^L_k}{\partial z^L_j}=0$$，于是，我们可以简化方程为：


$$
\delta^L_j=\frac{\partial C}{\partial a^L_j}\frac{\partial a^L_j}{\partial z^L_j}
$$


然后$$a^L_j=\sigma(z^L_j)$$，于是，可以写成


$$
\delta^L_j=\frac{\partial C}{\partial a^L_j}\sigma'(z^L_j)
$$


以矩阵的形式重写方程，其中每个元素$$\frac{\partial C}{\partial a^L_j}$$构成的向量表示成$$\nabla_aC$$，每个元素$$\sigma'(z^L_j)$$构成的向量表示成$$\sigma'(z^L)$$，于是得到


$$
\delta^{L}= \begin{bmatrix}
   \delta^L_{1} \\
   \delta^L_{2} \\
   \delta^L_{3} \\
     ... \\
   \delta^L_{n}
\end{bmatrix}=\begin{bmatrix}
   \frac{\partial C}{\partial a^L_1}\sigma'(z^L_1) \\
   \frac{\partial C}{\partial a^L_2}\sigma'(z^L_2) \\
   \frac{\partial C}{\partial a^L_3}\sigma'(z^L_3) \\
     ... \\
   \frac{\partial C}{\partial a^L_n}\sigma'(z^L_n)
\end{bmatrix}=\nabla_aC \odot \sigma'(z^L)
$$


### 2. 使用下一层的误差$$\delta^{l+1}$$来表示当前层误差$$\delta^l$$

特别的，


$$
\delta^l =((w^{l+1})^T\delta^{l+1}) \odot\sigma'(z^l)
$$


其中$$(w^{l+1})^T$$是$$(l+1)^{th}$$层权重矩阵$$w^{l+1}$$的转置。这个公式看过去有点复杂，但是每个元素都有很好得分解释，假设我们知道$$(l+1)^{th}$$的误差向量$$\delta^{l+1}$$，当我们应用转置的权重矩阵$$(w^{l+1})^T$$，我们可以把它看做是沿着网络反向移动误差，给了我们度量在$$l^{th}$$层的误差的方法。然后进行Hadamard乘积运算$$\sigma'(z^l)$$，这会让误差通过$$l$$层的激活函数反向传递回来并给出在第$$l$$层的带权输入误差向量$$\delta^l$$。

根据该公式，我们可以首先根据第一个公式得到输出层的误差$$\delta^L$$，然后得到第$$(L-1)^{th}$$层的误差，如此一步步地方向传播完整个网络。

证明：根据链式法则，我们可以将$$\delta^l_j= \frac{\partial C}{\partial z^l_j}$$写成：


$$
\delta^l_j= \frac{\partial C}{\partial z^l_j}=\displaystyle\sum_{k}\frac{\partial C}{\partial z^{l+1}_{k}}\frac{\partial z^{l+1}_k}{\partial z^l_j}=\displaystyle\sum_{k}\frac{\partial z^{l+1}_k}{\partial z^l_j}\delta^{l+1}_k
$$


这里我们将最后一个式子交换了两边的项，并用$$\delta^{l+1}_k$$代入。然后


$$
z^{l+1}_k=\displaystyle\sum_{j}w^{l+1}_{kj}a^l_j+b^{l+1}_k=\displaystyle\sum_{j}w^{l+1}_{kj}\sigma(z^{l}_j)+b^{l+1}_k
$$


做微分，我们得到


$$
\frac{\partial z^{l+1}_k}{\partial z^l_j}=w^{l+1}_{kj}\sigma'(z^{l}_j)
$$


代入上式，得到


$$
\delta^l_j=\displaystyle\sum_{k}w^{l+1}_{kj}\sigma'(z^{l}_j)\delta^{l+1}_k=(\displaystyle\sum_{k}w^{l+1}_{kj}\delta^{l+1}_k) \sigma'(z^{l}_j)
$$


将公式进行矩阵化，最后得到第一个公式。

举例来说，如下图：

![](/assets/network-bp-pic.png)

当我们已经计算得到了第$$3$$层的误差向量$$\delta^{3}= \begin{bmatrix}
   \delta^3_{1} \\
   \delta^3_{2}
\end{bmatrix}$$，这时计算第$$2$$层的误差向量$$\delta^2$$，我们先计算$$\delta^2_3$$，根据上面的公式可以得到


$$
\delta^2_3=(\displaystyle\sum_{k}w^{3}_{k3}\delta^3_k) \sigma'(z^{3}_3)=(w^{3}_{13}\delta^3_1+w^{3}_{23}\delta^3_2) \sigma'(z^{2}_3)=(\begin{bmatrix}
   w^3_{13} \\
   w^3_{23}
\end{bmatrix}^T \cdot \begin{bmatrix}
   \delta^3_{1} \\
   \delta^3_{2}
\end{bmatrix})\sigma'(z^{2}_3)
$$


也就是图中两条绿色的线所标识的权重与误差的乘积和。

### 3. 代价函数关于网络中任意偏置的改变率


$$
\frac{\partial C}{\partial b^l_j}=\delta^l_j
$$


也就是误差$$\delta^l_j$$和$$\frac{\partial C}{\partial b^l_j}$$完全一致。

证明：根据链式法则，其中$$z^l_j = \displaystyle\sum_{k}w^l_{jk} a^{l-1}_k + b^l_j$$，最终可以得到


$$
\frac{\partial C}{\partial b^l_j} = \frac{\partial C}{\partial z^l_{j}}\frac{\partial z^l_j}{\partial b^l_j}=\frac{\partial C}{\partial z^l_{j}}
$$


用矩阵表示，可以表示成：


$$
\frac{\partial C}{\partial b^l}=\delta^l
$$


### 4. 代价函数关于任何一个权重的改变率


$$
\frac{\partial C}{\partial w^l_{jk}}=a^{l-1}_k\delta^l_j
$$


证明：根据链式法则，其中$$z^l_j = \displaystyle\sum_{k}w^l_{jk} a^{l-1}_k + b^l_j$$，最终可以得到


$$
\frac{\partial C}{\partial w^l_{jk}} = \frac{\partial C}{\partial z^l_{j}}\frac{\partial z^l_j}{\partial b^l_j}=\frac{\partial C}{\partial z^l_{j}} a^{l-1}_j=a^{l-1}_k\delta^l_j
$$


举例来说：

![](/assets/network-bp-w.png)

上面图中，想要计算$$\frac{\partial C}{\partial w^3_{13}}$$，则$$\frac{\partial C}{\partial w^3_{13}}=a^2_3\delta^3_1$$。也可以理解成：


$$
\frac{\partial C}{\partial w}=a_{in}\delta_{out}
$$


其中$$a_{in}$$是输入给权重$$w$$的神经元的激活值，$$\delta_{out}$$是输出自权重$$w$$的神经元的误差。

用矩阵表示，可以表示成（其中第$$(l-1)^{th}$$层神经元有$$m$$个，第$$l^{th}$$层神经元有$$n$$个）


$$
\frac{\partial C}{\partial w^l} = \partial C/\partial \begin{bmatrix}
   w^l_{11} & w^l_{12} & w^l_{13} & ... & w^l_{1m} \\
   w^l_{21} & w^l_{22} & w^l_{23} & ... & w^l_{1m} \\
   w^l_{31} & w^l_{12} & w^l_{13} & ... & w^l_{1m} \\
                                 ... \\
   w^l_{n1} & w^l_{n2} & w^l_{n3} & ... & w^l_{nm} 
\end{bmatrix}=\begin{bmatrix}
   a^{l-1}_1 \delta^l_1 & a^{l-1}_2 \delta^l_1 & a^{l-1}_3 \delta^l_1 & ... & a^{l-1}_m \delta^l_1 \\
   a^{l-1}_1 \delta^l_2 & a^{l-1}_2 \delta^l_2 & a^{l-1}_3 \delta^l_2 & ... & a^{l-1}_m \delta^l_2 \\
   a^{l-1}_1 \delta^l_3 & a^{l-1}_2 \delta^l_3 & a^{l-1}_3 \delta^l_3 & ... & a^{l-1}_m \delta^l_3 \\
                                 ... \\
   a^{l-1}_1 \delta^l_n & a^{l-1}_2 \delta^l_n & a^{l-1}_3 \delta^l_n & ... & a^{l-1}_m \delta^l_n
\end{bmatrix}
$$


于是得到：


$$
\frac{\partial C}{\partial w^l}= \begin{bmatrix}
   \delta_1^{l} \\
   \delta_2^{l} \\
   \delta_3^{l} \\
     ... \\
   \delta_n^{l} 
\end{bmatrix}\cdot  [a^{l-1}_1, a^{l-1}_2, ..., a^{l-1}_m]
$$


其中$$[\delta^l_1, \delta^l_2, ..., \delta^l_n]$$是$$n * 1$$，$$[a^{l-1}_1, a^{l-1}_2, ..., a^{l-1}_m]$$是$$1 * m$$，最终得到$$n*m$$的权重矩阵。

