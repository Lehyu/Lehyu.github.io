## 1. 二值变量

### 1.1 伯努利分布

对于 $x\in\{0,1\},p(x=1\vert \mu)=\mu$，$x$ 的概率分布有

$$\begin{equation}
\begin{array}{rcl}
Bern(x\vert \mu) &=& \mu^x(1-\mu)^{1-x} \\
E[x] &=& \mu \\
var[x] &=& \mu(1-\mu)
\end{array}
\end{equation}$$

假设一个数据集 $\mathcal{D}=\{x_1,x_2,\dots,x_N\}$，$x_i$ 服从伯努利分布，并且独立同分布，那么

$$\begin{equation}
\begin{array}{rcl}
p(\mathcal{D}\vert \mu) &=& \prod_{n=1}^N{\mu^{x_n}(1-\mu)^{1-x_n} }
\end{array}
\end{equation} \tag{1}$$

我们能够通过最大化似然函数来求解 $\mu$，

$$\begin{equation}
\begin{array}{rcl}
\ln{p(\mathcal{D}\vert \mu)} &=& \sum_{n=1}^N{x_n\ln\mu+(1-x_n)\ln(1-\mu) } \\
\frac{\partial\ln{p}}{\partial\mu} &=& 0 \\
\Rightarrow \mu_{ML} &=& \frac{1}{N}\sum_{n=1}^Nx_n \\
\Rightarrow \mu_{ML} &=& \frac{m}{N} \\
\end{array}
\end{equation} $$

由于 $x_i\in\{0,1\}$，如果设 $D$ 中 $m$ 个值为1的观测值，那么上式可以写成

$$\begin{equation}
\begin{array}{rcl}
\Rightarrow \mu_{ML} = \frac{m}{N}
\end{array}
\end{equation} $$

假设我们抛一枚有损的硬币 $p(x=1\vert \mu)=\mu$ 可以不为0.5，如果 $\mathcal{D}$ 有3个观测值，而且全部为1，即头部向上，那么我们由最大似然函数得到的结果就是 $\mu_{ML}=1$，如果我们用这个值去预测以后的结果，明显准确率会十分差，这就是所谓的过拟合。

### 1.2 二项分布

假设我们已经知道观测值中总共有 $m$ 个值为1的观测值，那么对 $m$ 的概率分布有

$$\begin{equation}
\begin{array}{rcl}
Bin(x\vert N,\mu) &=& \binom{N}{m}\mu^m(1-\mu)^{N-m} \\
\end{array}
\end{equation} \tag{2}$$

$$\begin{equation}
\begin{array}{rcl}
\binom{N}{m} &=& \frac{N!}{(N-m)!m!} \\
E[m] &=& N\mu \\
var[m] &=& N\mu(1-\mu)
\end{array}
\end{equation}$$

以上关于期望与方差的计算可以重新计算，或者用第一章习题1.10的结论直接得出。

### 1.3 贝塔分布

前面我们知道最大似然函数会容易过拟合，那么可以用贝叶斯分析来减小这个问题，但是之前我们先介绍一下贝塔分布

$$\begin{equation}
\begin{array}{rcl}
Beta(\mu\vert a,b) &=& \frac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)}\mu^{a-1}(1-\mu)^{b-1} \\
\end{array}
\end{equation} \tag{3}$$

$$\begin{equation}
\begin{array}{rcl}
\Gamma(x) &=& \int_0^{\infty}{\mu^{x-1}\exp^{-\mu} }d\mu \\
E[\mu] &=& \frac{a}{a+b} \\
var[\mu] &=& \frac{ab}{(a+b)^2(a+b+1)}
\end{array}
\end{equation}$$

其中 $a,b$ 是超参数，当 $a,b$ 取不同值时，分布图如下所示：

![beta plot](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/beta_plot.png?raw=true)

### 1.4 贝叶斯分析

贝叶斯公式：$p(y\vert x)\propto p(x\vert y)p(y)$

为了使过程简化，我们希望后验概率 $p(y\vert x)$ 与先验概率 $p(y)$ 有同样的形式，这个先验被称为共轭先验(conjugate prior)。对于抛硬币问题(服从伯努利分布)，由式(2)我们知道似然函数 $p(m\vert N,\mu) \propto \mu^m(1-\mu)^{N-m}$，那么我们可以取贝塔分布作为共轭先验，那么

$$\begin{equation}
\begin{array}{rcl}
p(\mu\vert m,l,a,b) &\propto& \mu^{m+a-1}(1-\mu)^{l+b-1} \\
m+l &=& N \\
\Rightarrow p(\mu\vert m,l,a,b) &=& \frac{\Gamma(a+b+l+m)}{\Gamma(a+m)\Gamma(b+l)}\mu^{a+m-1}(1-\mu)^{b+l-1} \\
\Rightarrow p(x=1\vert \mathcal{D}) &=& \int_0^1{p(x=1\vert \mu)p(\mu\vert D) }d\mu \\
&=& \int_0^1{\mu p(\mu\vert D) }d\mu \\
&=& E[\mu\vert D] \\
&=& \frac{a+m}{a+m+b+l}
\end{array}
\end{equation}$$

假设我们设 $a=1,b=1$，$\mathcal{D}$ 有三个观测值，并且都为1，那么在贝叶斯分析中去预测为 $\frac{4}{5}$，直觉上至少要比最大似然函数的预测 $1$ 要靠谱。

当 $N$ 越来越大时，先验知识就会被逐渐弱化，$\lim_{N\to\infty}\frac{a+m}{a+m+b+l} \simeq \frac{m}{N}$，最大后验与最大似然具有相同的预测值。但是对于数据量较少的应用，使用最大后验往往会比最大似然要好。

而且最大后验能够充当下一次观测数据的先验知识，从而达到顺序学习(sequential learning)，能够更好的应用于大规模数据。比如我们将 $\mathcal{D}$ 划分数据集大小为 $N_0,N_1$ 的两个子数据集 $\mathcal{D_0},\mathcal{D_1}$，刚开始我们观测到数据集 $\mathcal{D_1}$，先验知识为 $p(\mu\vert a_0,b_0)$，那么对于数据集 $\mathcal{D_0}$ 的后验为

$$\begin{equation}
\begin{array}{rcl}
p(\mu\vert m_0,l_0,a_0,b_0) &=& \frac{\Gamma(a_0+b_0+l_0+m_0)}{\Gamma(a_0+m_0)\Gamma(b_0+l_0)}\mu^{a_0+m_0-1}(1-\mu)^{b_0+l_0-1} \\
&=& Beta(\mu\vert a_0+m_0,b_0+l_0)
\end{array}
\end{equation}$$

然后我们观测到数据集 $\mathcal{D_1}$，此时以 $\mathcal{D_0}$ 的后验作为其先验，那么同理我们有

$$\begin{equation}
\begin{array}{rcl}
p(\mu\vert m_1,l_1,m_0,l_0,a_0,b_0) &=& \frac{\Gamma(a_0+b_0+l_0+m_0+l_1+m_1)}{\Gamma(a_0+m_0+m_1)\Gamma(b_0+l_0+l_1)}\mu^{a_0+m_0+m_1-1}(1-\mu)^{b_0+l_0+l_1-1} \\
&=& Beta(\mu\vert a_0+m_0+m_1,b_0+l_0+l_1) \\
&=& Beta(\mu\vert a_0+m,b_0+l) \\
\end{array}
\end{equation}$$

这个优点能够使我们更容易的训练大规模数据。

在贝叶斯学习中，当我们观测的数据越来越多的时候，后验分布的不确定性就会越来越小。
**todo**

## 2. 多元变量

前面一节我们介绍的是变量只有两个状态(要么是1，要么是0)，当变量有多个状态的时候，我们可以扩展成 $\mathbf{x} = (0,\dots,1,\dots,0)^T,\sum_{k=1}^Kx_k=1,p(x_k)=\mu_k$，那么

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}\vert \mathbf{\mu})=\prod_{k=1}^K\mu_k^{x_k}
\end{array}
\end{equation}$$

其中 $\mathbf{\mu}=(\mu_1,\dots,\mu_K)^T,\sum_{k=1}^K\mu_k=1$，同之前的伯努利分布一样，对于 $\mathcal{D}=\{\mathbf{x}_1,\dots,\mathbf{x}_N\}$ 的似然函数有

$$\begin{equation}
\begin{array}{rcl}
p(\mathcal{D}\vert \mathbf{\mu}) &=& \prod_{n=1}^{N}\prod_{k=1}^K\mu_k^{x_{nk}} \\
&=& \prod_{k=1}^K\mu_k^{\sum_{n=1}^{N}(x_{nk})} \\
&=& \prod_{k=1}^K\mu_k^{m_k} \\
\end{array}
\end{equation}$$

求其最大值与二值变量是基本类似的，只是多了一个约束条件 $\sum_{k=1}^K\mu_k=1$，我们可以用[拉格朗日乘子法](http://baike.baidu.com/link?url=DAMqfYU_7yGh957FSCAEsP0FZlUuS6R1IyRCQQF7BGtAqrtj9izuAe1vbsz25N23zYPW2rjgDBgn2HZaWmRHnPoprHDseqdSXwvRzuc7hmet28u_jZaKhSEFLybEpNFI2JHWBLZzRwEuMPhivRu2YL-Z6rE95kGa5KS0iL61pnC)来进行求解

$$\begin{equation}
\begin{array}{rcl}
L(\mathbf{\mu},\lambda) &=& \sum_{k=1}^K m_k\ln\mu_k+\lambda(\sum_{k=1}^K\mu_k-1) \\
\frac{\partial{L}}{\partial{\mu_k}} &=& 0 \\
\Rightarrow \frac{m_k}{\mu_k}+\lambda &=& 0 \\
\Rightarrow \mu_k &=& -m_k/\lambda \\
\frac{\partial{L}}{\partial{\lambda}} &=& 0 \\
\Rightarrow \lambda &=& -N \\
\Rightarrow \mu_k &=& -\frac{m_k}{N} \\
\end{array}
\end{equation}$$

多项式分布

$$\begin{equation}
\begin{array}{rcl}
Mult(m_1,m_2,\dots,m_K\vert \mathbf{\mu},N) &=& \binom{N}{m_1m_2\dotsm_K}\prod_{k=1}^K\mu_k^{m_k} \\
\binom{N}{m_1m_2\dotsm_K} &=& \frac{N!}{m_1!m_2!\dots m_K!} \\
\sum_{k=1}^Km_k=N
\end{array}
\end{equation}$$

###　2.1 狄利克雷(Dirichlet)分布

多项分布的共轭先验是狄利克雷分布，如下

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{\mu}\vert \mathbf{\alpha}) &\propto& \prod_{k=1}^K\mu_k^{\alpha_k-1} \\
Dir(\mathbf{\mu}\vert \mathbf{\alpha}) &=& \frac{\Gamma(\alpha_0)}{\Gamma(\alpha_1)\dotsm\Gamma(\alpha_K)}\prod_{k=1}^K\mu_k^{\alpha_k-1} \\
\alpha_0 &=& \sum_{k=1}^K\alpha_k \\
\end{array}
\end{equation}$$

那么多项分布的后验

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{\mu}\vert \mathcal{D},\mathbf{\alpha}) &\propto& p(\mathcal{D}\vert \mathbf{\mu})p(\mathbf{\mu}\vert \mathbf{\alpha}) \propto \prod_{k=1}^K\mu_k^{\alpha_k+m_k-1} \\
p(\mathbf{\mu}\vert \mathcal{D},\mathbf{\alpha}) &=& \frac{\Gamma(\alpha_0+N)}{\Gamma(\alpha_1+m_1)\dotsm\Gamma(\alpha_K+m_K)}\prod_{k=1}^K\mu_k^{\alpha_k+m_k-1} \\
\end{array}
\end{equation}$$

以上都是离散变量的分布，下面介绍连续型变量中最常用的分布，高斯(Gaussian)分布

## 3. 高斯分布

对于单一变量：$\mathcal{N}(x\vert \mu,\sigma^2) =\frac{1}{\sqrt{2\pi \sigma^2}}\exp\{-\frac{(x-\mu)^2}{2\sigma^2}\}$

对于D维向量 $\mathbf{x}$：$\mathcal{N}(\mathbf{x}\vert \mathbf{\mu},\mathbf{\Sigma}) =\frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} }\exp\{-\frac{1}{2}(\mathbf{x}-\mathbf{\mu})^T\Sigma^{-1}(\mathbf{x}-\mathbf{\mu})\}$，其中 $\mathbf{\mu},\mathbf{\Sigma}$ 分别是D维均值向量和 $D\times D$ 协方差矩阵，$\vert \mathbf{\Sigma}\vert $ 是协方差矩阵的行列式。

对于单一变量，使其熵最大化的分布是高斯分布(PRML p54)，这个性质同样适用于多元(multivariate)高斯分布。[中心极限定理](http://baike.baidu.com/link?url=eu7GA-AAQONj-xzc01eMKpiM6zuLpBQPrSFfQXKKG0fhJ0lAJNB_7zkMYINL1Gw_3mEmh2d9XZ5uyAtyo37r-WlX8ddEsMI5sXKnOkHjbSIZdXiIsk87XohFBWLWbzhWvulkaaF2mSPLYDcyt3N7u_)告诉我们独立同分布的随机变量序列的和近似高斯分布。

![central limit theorem](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/central_limit_theorem.png?raw=true)

下面考虑一下高斯分布的几何形态

$$\begin{equation}
\begin{array}{rcl}
\Delta^2 &=& (\mathbf{x}-\mathbf{\mu})^T\mathbf{\Sigma}^{-1}(\mathbf{x}-\mathbf{\mu})
\end{array}
\end{equation}$$

概率相等的点分布在一个 $\mathbf{x}$ 空间的曲面上。 需要注意的是 $\Sigma$ 是一个对称矩阵，那么其特征向量形成一个正交向量集

$$\begin{equation}
\begin{array}{rcl}
\Sigma\mathbf{u}_i &=& \lambda_i\mathbf{u}_i \\
\mathbf{u}_i^T\mathbf{u}_j &=& I_{ij} \\
\Rightarrow \Sigma &=& \sum_{i=1}^D\lambda_i\mathbf{u}_i^T\mathbf{u}_i \\
\Rightarrow \Sigma^{-1} &=& \sum_{i=1}^D\frac{1}{\lambda_i}\mathbf{u}_i\mathbf{u}_i^T \\
\overset{y_i=\mathbf{u}_i^T(\mathbf{x}-\mathbf{\mu})}\Rightarrow \Delta^2 &=& \sum_{i=1}^D\frac{y_i^2}{\lambda_i} \\
\overset{\mathbf{y}=(y_1,\dots,y_D)^T}\Rightarrow\mathbf{y} &=& \mathbf{U}(\mathbf{x}-\mathbf{\mu})
\end{array}
\end{equation}$$

其中: $\mathbf{U}$ 第i行为 $\mathbf{u}_i^T$，$\mathbf{U}^T\mathbf{U}=\mathbf{I}$

![gaussian_geo_form](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/gaussian_geo_form.png?raw=true)

对于当前的高斯分布，协方差矩阵的特征值 $\lambda_i$ 必须严格正(全部大于0)，否则不能够合适地规范化，之后我们可能会遇到有些特征值为0的高斯分布，这些分布能够限制在更低的维度空间上。

之前我们考虑的是高斯分布的几何形态，由第一章p18我们知道 $p(y)=p_x(g(y))\vert g'(y)\vert ,x=g(y)$，那么先求高斯分布 $x$ 关于 $y$ 的导数

$$\begin{equation}
\begin{array}{rcl}
y_i &=& \mathbf{u}_i^T(\mathbf{x}-\mathbf{\mu}) \\
J_{ij} &=& \frac{\partial{x_i}}{\partial{x_j}} = U_{ij} \\
\vert \mathbf{J}\vert ^2 &=& \vert \mathbf{U}^T\mathbf{U}\vert =\vert \mathbf{I}\vert =1
\end{array}
\end{equation}$$

而对称矩阵的行列式等于其特征值的连乘： $\vert \Sigma\vert ^{1/2}=\prod_{j=1}^D\lambda_j^{1/2}$，那么

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{y}) &=& p(\mathbf{x})\vert \mathbf{J}\vert =\prod_{j=1}^D\frac{1}{\sqrt{2\pi\lambda_j}}\exp\{-\frac{y_j^2}{2\lambda_j} \}
\end{array}
\end{equation}$$

### 3.1 多元高斯分布的期望与协方差

$$\begin{equation}
\begin{array}{rcl}
E[\mathbf{x}] &=& \frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \int{\mathbf{x}\exp\{-\frac{1}{2}(\mathbf{x}-\mathbf{\mu})^T\Sigma^{-1}(\mathbf{x}-\mathbf{\mu})\}}d\mathbf{x} \\
&\overset{\mathbf{z}=\mathbf{x}-\mathbf{\mu}}=& \frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \int{(\mathbf{z}+\mathbf{\mu})\exp\{-\frac{1}{2}\mathbf{z}^T\Sigma^{-1}\mathbf{z}\}}d\mathbf{z} \\
&\overset{odd}=&\frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \int{\mathbf{\mu}\exp\{-\frac{1}{2}\mathbf{z}^T\Sigma^{-1}\mathbf{z}\}}d\mathbf{z} \\
&=& \mathbf{\mu} \\
E[\mathbf{x}\mathbf{x}^T] &=& \frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \int{\mathbf{x}\mathbf{x}^T\exp\{-\frac{1}{2}(\mathbf{x}-\mathbf{\mu})^T\Sigma^{-1}(\mathbf{x}-\mathbf{\mu})\}}d\mathbf{x} \\
&\overset{\mathbf{z}=\mathbf{x}-\mathbf{\mu}}=& \frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \int{(\mathbf{z}+\mathbf{\mu})(\mathbf{z}+\mathbf{\mu})^T\exp\{-\frac{1}{2}\mathbf{z}^T\Sigma^{-1}\mathbf{z}\}}d\mathbf{z} \\
&\overset{odd}=&\frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \int{(\mathbf{\mu}\mathbf{\mu}^T+\mathbf{z}\mathbf{z}^T)\exp\{-\frac{1}{2}\mathbf{z}^T\Sigma^{-1}\mathbf{z}\}}d\mathbf{z} \\
&=& \mathbf{\mu}\mathbf{\mu}^T+\frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \int{\mathbf{z}\mathbf{z}^T\exp\{-\frac{1}{2}\mathbf{z}^T\Sigma^{-1}\mathbf{z}\}}d\mathbf{z} \\
&\overset{y_i=\mathbf{u_i}^T\mathbf{z}}=& \mathbf{\mu}\mathbf{\mu}^T+\frac{1}{(2\pi)^{D/2 }\vert \mathbf{\Sigma}\vert ^{1/2} } \sum_{i=1}^D\sum_{j=1}^D \mathbf{u}_i\mathbf{u}_j^T\int{\exp\{-\sum_{k=1}^D\frac{y_k^2}{2\lambda_k}\}}y_iy_jd\mathbf{y} \\
&=& \mathbf{\mu}\mathbf{\mu}^T+\sum_{i=1}^D\lambda_i\mathbf{u}_i\mathbf{u}_i^T \\
&=& \mathbf{\mu}\mathbf{\mu}^T+\Sigma \\
cov[\mathbf{x}] &=& E[(\mathbf{x}-E[\mathbf{x}])(\mathbf{x}-E[\mathbf{x}])^T]\\
&=& \Sigma
\end{array}
\end{equation}$$

$\mathbf{\mu},\Sigma$共同控制了高斯分布的形态，在D维空间中，一般情况下，$\mathbf{\mu}$ 有D个参数，而 $\Sigma$ 有 $D(D+1)/2$ 个参数(对称矩阵)，那么为了决定一个高斯分布的形态我们要有 $D(D+3)/2$ 个参数。对于低维空间来说这个参数是可以接受的；但是对于非常大的 $D$，计算量就会变得十分昂贵(协方差的逆矩阵)。

一个方法是**限制**协方差矩阵的形态：1. $\Sigma=diag(\sigma_i^2)$，此时只有 $2D$ 个参数；2. $\Sigma=\sigma^2\mathbf{I}$，此时有 $D+1$ 个参数。

![confined_gaussian](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/confined_gaussian.png?raw=true)

由上图知道，虽然限制协方差矩阵的形态能够使计算协方差矩阵的逆更加快速，但是这也限制了高斯函数的分布从而影响高斯分布拟合数据的能力。

### 3.2 条件高斯分布与边缘高斯分布

条件高斯分布与边缘高斯分布的推导可以参考PRML p86。

$$\begin{equation}
\begin{array}{rcl}
\mathbf{x} &=& \begin{pmatrix}  \mathbf{x}_a \\ \mathbf{x}_b \end{pmatrix} \\
\mathbf{\mu} &=& \begin{pmatrix}  \mathbf{\mu}_a \\ \mathbf{\mu}_b \end{pmatrix} \\
\mathbf{\Sigma} &=& \begin{pmatrix}  \mathbf{\Sigma}_{aa} & \mathbf{\Sigma}_{ab} \\ \mathbf{\Sigma}_{ba} &\mathbf{\Sigma}_{bb} \end{pmatrix} \\
\mathbf{\Lambda} &=& \begin{pmatrix}  \mathbf{\Lambda}_{aa} & \mathbf{\Lambda}_{ab} \\ \mathbf{\Lambda}_{ba} &\mathbf{\Lambda}_{bb} \end{pmatrix} \\
\end{array}
\end{equation}$$

$p(\mathbf{x}) = p(\mathbf{x}_a,\mathbf{x}_b)$，当我们固定 $\mathbf{x}_b$ 就得到 $\mathbf{x}_a$ 关于 $\mathbf{x}_b$ 的条件概率

$$\begin{equation}
\begin{array}{rcl}
-\frac{1}{2}(\mathbf{x}-\mathbf{\mu})^T\mathbf{\Sigma}^{-1}(\mathbf{x}-\mathbf{\mu}) &=& -\frac{1}{2}(\mathbf{x}_a-\mathbf{\mu}_a)^T\mathbf{\Lambda}_{aa}(\mathbf{x}_a-\mathbf{\mu}_a) \\ && -\frac{1}{2}(\mathbf{x}_a-\mathbf{\mu}_a)^T\mathbf{\Lambda}_{ab}(\mathbf{x}_b-\mathbf{\mu}_b) \\ &&-\frac{1}{2}(\mathbf{x}_b-\mathbf{\mu}_b)^T\mathbf{\Lambda}_{ba}(\mathbf{x}_a-\mathbf{\mu}_a) \\
&&-\frac{1}{2}(\mathbf{x}_b-\mathbf{\mu}_b)^T\mathbf{\Lambda}_{bb}(\mathbf{x}_b-\mathbf{\mu}_b)
\end{array}
\end{equation}$$

由上面的推导我们知道，当 $\mathbf{x}_b$ 固定时，上式是关于 $\mathbf{x}_a$ 的二项式，因此 $p(\mathbf{x}_a\vert \mathbf{x}_b)$ 是干死分布。下面介绍知道一个高斯分布的指数的二项式之后，求它的期望与协方差矩阵。

$$\begin{equation}
\begin{array}{rcl}
-\frac{1}{2}(\mathbf{x}-\mathbf{\mu})^T\mathbf{\Sigma}^{-1}(\mathbf{x}-\mathbf{\mu}) &=& -\frac{1}{2}\mathbf{x}^T\Sigma^{-1}\mathbf{x}+\mathbf{x}^T\Sigma^{-1}\mathbf{\mu}+const
\end{array}
\end{equation}$$

上式的const是指与 $\mathbf{x}$ 无关的项。根据上式我们就得到 $p(\mathbf{x}_a\vert \mathbf{x}_b)$ 的均值与协方差矩阵

$$\begin{equation}
\begin{array}{rcl}
-\frac{1}{2}(\mathbf{x}-\mathbf{\mu})^T\mathbf{\Sigma}^{-1}(\mathbf{x}-\mathbf{\mu}) &=& -\frac{1}{2}(\mathbf{x}_a-\mathbf{\mu}_a)^T\mathbf{\Lambda}_{aa}(\mathbf{x}_a-\mathbf{\mu}_a) \\ && -\frac{1}{2}(\mathbf{x}_a-\mathbf{\mu}_a)^T\mathbf{\Lambda}_{ab}(\mathbf{x}_b-\mathbf{\mu}_b) \\ &&-\frac{1}{2}(\mathbf{x}_b-\mathbf{\mu}_b)^T\mathbf{\Lambda}_{ba}(\mathbf{x}_a-\mathbf{\mu}_a) \\
&&-\frac{1}{2}(\mathbf{x}_b-\mathbf{\mu}_b)^T\mathbf{\Lambda}_{bb}(\mathbf{x}_b-\mathbf{\mu}_b) \\
&=& -\frac{1}{2}\mathbf{x}_a^T\Lambda_{aa}\mathbf{x}_a+\mathbf{x}_a^T(\Lambda_{aa}\mathbf{\mu}_{aa}-\Lambda_{ab}(\mathbf{x}_b-\mathbf{\mu}_b))+const \\
\Rightarrow \Sigma_{a\vert b}^{-1} &=& \Lambda_{aa} \\
\Rightarrow \Sigma_{a\vert b}^{-1}\mathbf{\mu}_{a\vert b} &=& \Lambda_{aa}\mathbf{\mu}_{aa}-\Lambda_{ab}(\mathbf{x}_b-\mathbf{\mu}_b) \\
\Rightarrow \mathbf{\mu}_{a\vert b} &=& \mathbf{\mu}_{aa}-\Lambda_{aa}^{-1}\Lambda_{ab}(\mathbf{x}_b-\mathbf{\mu}_b)
\end{array}
\end{equation}$$

条件高斯分布 $p(\mathbf{x}_a\vert \mathbf{x}_b)$ 的均值是 $\mathbf{x}_b$ 的线性函数

至于边缘函数则可以由积分求得: $p(\mathbf{x}_a)=\int{p(\mathbf{x}_a,\mathbf{x}_b)}d\mathbf{x}_b$，与条件高斯分布的推导基本类似都是，详细请看书P88，下面只给出结果

$$\begin{equation}
\begin{array}{rcl}
E[\mathbf{x}_a] &=& \mathbf{\mu}_a \\
cov[\mathbf{x}_a] &=& \Sigma_{aa}
\end{array}
\end{equation}$$

高斯条件分布 $p(\mathbf{y}\vert \mathbf{x})$ 的均值是 $\mathbf{x}$ 的线性函数那么，

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}) &=& \mathcal{N}(\mathbf{x}\vert \mathbf{\mu},\Lambda^{-1}) \\
p(\mathbf{y}\vert \mathbf{x}) &=& \mathcal{N}(\mathbf{y}\vert \mathbf{A}\mathbf{x}+\mathbf{b},L^{-1}) \\
\Rightarrow p(\mathbf{y}) &=& \mathcal{N}(\mathbf{y}\vert \mathbf{A}\mathbf{\mu}+\mathbf{b},L^{-1}+\mathbf{A}\Lambda^{-1}\mathbf{A}^T) \\
\Rightarrow p(\mathbf{x}\vert \mathbf{y}) &=& \mathcal{N}(\mathbf{y}\vert \Sigma\{\mathbf{A}^TL(\mathbf{y}-\mathbf{b})+\Lambda\mathbf{\mu} \},\Sigma) \\
\Sigma &=& (\Sigma+\mathbf{A}L\mathbf{A})^{-1}
\end{array}
\end{equation}$$

需要注意的是，书中的推导是先求出 $p(\mathbf{x},\mathbf{y})$ ，然后再根据高斯条件分布与高斯边缘分布的结论直接得出 $p(\mathbf{y}),p(\mathbf{x}\vert \mathbf{y})$

### 3.3 高斯分布的最大似然

$\mathbf{X}=\{\mathbf{x}_1,\dots,\mathbf{x}_N\},\{\mathbf{x}_n\}$ 独立同分布。

$$\begin{equation}
\begin{array}{rcl}
\ln{p(\mathbf{X}\vert \mathbf{\mu},\mathbf{\Sigma})} &=& -\frac{ND}{2}\ln(2\pi)-\frac{N}{2}\ln\vert \Sigma\vert +\frac{1}{2}\sum_{n=1}^N(\mathbf{x}_n-\mathbf{\mu})^T\Sigma^{-1}(\mathbf{x}_n-\mathbf{\mu}) \\
\overset{\frac{\partial{\ln p}}{\partial\mathbf{\mu}}=0}\Rightarrow
0 &=& -\frac{1}{2}\sum_{n=1}^T\{\Sigma^{-1}(\mathbf{x}_n-\mathbf{\mu})+\{(\mathbf{x}_n-\mathbf{\mu})\Sigma^{-1}\}^T\} \\
\Rightarrow \mathbf{\mu}_{ML} &=& \frac{1}{N}\sum_{n=1}^N\mathbf{x}_n \\
\overset{\frac{\partial{\ln p}}{\partial{\Sigma}}=0}\Rightarrow
0 &=& -\frac{N}{2}\Sigma^{-1}-\frac{1}{2}\sum_{n=1}^T\{(\mathbf{x}_n-\mathbf{\mu})(\mathbf{x}_n-\mathbf{\mu})^T(-\Sigma^{-2})\} \\
\Rightarrow \Sigma_{ML} &=& \frac{1}{N}\sum_{n=1}^N(\mathbf{x}_n-\mathbf{\mu}_{ML})(\mathbf{x}_n-\mathbf{\mu}_{ML})^T \\
\end{array}
\end{equation}$$

由于 $\mathbf{\mu}_{ML}$ 不依赖于 $\Sigma_{ML}$ ，可以先求 $\mathbf{\mu}_{ML}$ 再求 $\Sigma_{ML}$。

$$\begin{equation}
\begin{array}{rcl}
E[\mathbf{\mu}_{ML}] &=& E[\frac{1}{N}\sum_{n=1}^N\mathbf{x}_{n}] \\
p(x,y)=p(x)p(y) &\Rightarrow& E[x+y]=E[x]+E[y] \\
E[\mathbf{\mu}_{ML}] &=& \frac{1}{N}\sum_{n=1}^NE[\mathbf{x}_n] = \mathbf{\mu} \\
p(x_m,x_n)=p(x_n)p(x_m),p(x)\sim N(x\vert \mu,\sigma^2) &\Rightarrow& E[x_nx_m]=\mu^2+\sigma^2I_{nm} \\
E[\Sigma_{ML}] &=& \frac{N-1}{N}\Sigma
\end{array}
\end{equation}$$

### 3.4 顺序估计(sequential estimation)

首先对于高斯分布的均值(mean)估计：$\mathbf{\mu}_{ML} = \frac{1}{N}\sum_{n=1}^N\mathbf{x}_n$

$$\begin{equation}
\begin{array}{rcl}
\mathbf{\mu}_{ML}^N &=& \frac{1}{N}\sum_{n=1}^N\mathbf{x}_n \\
&=& \frac{1}{N}\mathbf{x}_N+\frac{1}{N}\sum_{n=1}^{N-1}\mathbf{x}_n \\
&=& \frac{1}{N}\mathbf{x}_N+\frac{N-1}{N}\mathbf{\mu}_{ML}^{N-1} \\
&=& \mathbf{\mu}_{ML}^{N-1}+\frac{1}{N}(\mathbf{x}_N-\mathbf{\mu}_{ML}^{N-1} )\\
\end{array}
\end{equation}$$

在我们估计了 $N-1$ 个观测值的 $\mathbf{\mu}_{ML}^{N-1}$ 后又来了一个新的观测数据，我们能够很轻松的利用先前的 $\mathbf{\mu}_{ML}^{N-1}$ 来估计  $\mathbf{\mu}_{ML}^{N}$，大大减小了计算量。对于拥有 $\mathbf{\mu}_{ML}\propto \sum_{n=1}^N\mathbf{x}_n$，我们可以利用上式来进行sequential estimation。如果更一般的话，我们可以利用[Robbins-Monro算法](todo)推导更一般的形式

$$\begin{equation}
\begin{array}{rcl}
\theta^{(N)} &=& \theta^{(N-1)}+a_{N-1}\frac{\partial{}}{\partial{\theta^{(N-1)}}}\ln{p(x_N\vert \theta^{(N-1)})}
\end{array}
\end{equation}$$

如单一变量的高斯分布，$\frac{\partial{}}{\partial{\mu_{ML}}}\ln{p(x\vert \mu_{ML},\sigma^2)}=\frac{1}{\sigma^2}(x-\mu_{ML})$，此时令 $a_{N}=\sigma^2/N$

### 3.5 高斯分布的贝叶斯推理

前面我们大致介绍了在贝叶斯推理中，最重要的是选择共轭先验。在高斯分布中，共轭先验的选择相对于多项分布更复杂，主要分为以下三种情况，先考虑单一变量的高斯分布

1 $\sigma^2$ 已知，$\mu$ 未知：取高斯分布作为先验 $\mathcal{N}(\mu\vert \mu_0,\sigma^2)$

2 $\sigma^2$ 未知，$\mu$ 已知：取伽马分布作为先验

$$\begin{equation}
\begin{array}{rcl}
Gam(\lambda\vert a,b) &=& \frac{1}{\Gamma(a)}b^a\lambda^{a-1}\exp(-b\lambda)\\
E[\lambda] &=& \frac{a}{b}\\
var[\lambda] &=& \frac{a}{b^2}
\end{array}
\end{equation}$$

3 当 $\sigma^2,\mu$ 都未知，取Normal-Gamma分布做为先验

$$\begin{equation}
\begin{array}{rcl}
p(\mu,\lambda) &=& \mathcal{N}(\mu\vert \mu_0,(\beta\lambda)^{-1})Gam(\lambda\vert a,b) \\
\end{array}
\end{equation}$$

共轭先验的选取主要是考察似然函数的形式，这个推导就不详细叙述。对于D维变量的高斯分布的共轭先验，大致与单一变量的高斯分布一样

 1 $\Lambda$ 已知，$\mathbf{\mu}$ 未知：取高斯分布作为先验 $\mathcal{N}(\mathbf{\mu}\vert \mathbf{\mu_0},\Lambda)$

2 $\Lambda$ 未知，$\mu$ 已知：取Wishart分布作为先验

$$\begin{equation}
\begin{array}{rcl}
\mathcal{W}(\Lambda\vert \mathbf{W},\nu) &=& B\vert \Lambda\vert ^{(\nu-D-1)/2}\exp(-\frac{1}{2}Tr(\mathbf{W}^{-1}\Lambda ))\\
B(\mathbf{W},\nu) &=& \vert \mathbf{W}\vert ^{-\nu/2}(2^{\nu D/2}\pi^{D(D-1)/4}\prod_{i=1}^D\Gamma(\frac{\nu+1-i}{2}))^{-1}
\end{array}
\end{equation}$$

3 当 $\Lambda,\mu$ 都未知，取Normal-Wishart分布做为先验

$$\begin{equation}
\begin{array}{rcl}
p(\mu,\lambda) &=& \mathcal{N}(\mu\vert \mu_0,(\beta\Lambda)^{-1})\mathcal{W}(\Lambda\vert \mathbf{W},\nu) \\
\end{array}
\end{equation}$$

### 3.6 Student's t-distribution

$$\begin{equation}
\begin{array}{rcl}
p(x\vert \mu,a,b)&=&\int_0^\infty{\mathcal{N}(x\vert \mu,\tau^{-1})Gam(\tau\vert a,b) }d\tau] \\
\Rightarrow St(x\vert \mu,\lambda,\nu) &=& \frac{\Gamma(\nu/2+1/2)}{\Gamma(\nu/2)}(\frac{\lambda}{\pi\nu})^{1/2}[1+\frac{\lambda(x-\mu)^2}{\nu}]^{-\nu/2-1/2} \\
\end{array}
\end{equation}$$

t-分布是由无穷个具有相同 $\mu$ 不同 $\lambda$ 的高斯分布累加而成的，t-分布比高斯分布具有更长的尾巴，从而使其对离群点更加鲁棒。

![gaussian_vs_t-dist](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/gaussian_vs_t-dist.png?raw=true)

当 $\nu\to\infty$ 时，t-分布就变成了 $\mathcal{N}(x\vert \mu,\lambda^{-1})$

![t-dist_plot](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/t-dist_plot.png?raw=true)

$$\begin{equation}
\begin{array}{rcl}
St(\mathbf{x}\vert \mathbf{\mu},\Lambda,\nu) &=& \frac{\Gamma(\nu/2+D/2)}{\Gamma(\nu/2)}(\frac{\vert \Lambda\vert ^{1/2}}{(\pi\nu)^{D/2}})[1+\frac{\Delta^2}{\nu}]^{-\nu/2-D/2} \\
\Delta^2 &=& (\mathbf{x}-\mathbf{\mu})^T\Lambda(\mathbf{x}-\mathbf{\mu}) \\
E[\mathbf{x}] &=& \mathbf{\mu}, ~~~~~~~~~~~~~~~~~if ~~~~\nu>1 \\
cov[\mathbf{x}] &=& \frac{\nu}{\nu-2}\Lambda^{-1},~~if~~~~\nu>2 \\
mode[\mathbf{x}] &=& \mathbf{\mu}
\end{array}
\end{equation}$$

### 3.7 周期变量

从笛卡尔坐标系转到极坐标系。von Mises分布

$$\begin{equation}
\begin{array}{rcl}
p(\theta\vert \theta_0,m) &=& \frac{1}{2\pi I_0(m)}\exp(m\cos(\theta-\theta_0)) \\
I_0(m) &=& \frac{1}{2\pi}\int_0^{2\pi}{\exp(m\cos\theta) }d\theta
\end{array}
\end{equation}$$

### 3.8 混合高斯(Mixtures of Gaussians)

我们知道高斯分布是一个单峰分布，对于实际应用如果用单一高斯分布会存在很大的限制

![pld_faithful_plot](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/odl_faithful_plot.png?raw=true)

上图中的左子图明显不能捕捉到数据的真实分布，我们希望能得到右子图的模型。因此提出混合高斯模型

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}) &=& \sum_{k=1}^K\pi_k\mathcal{N}(\mathbf{x}\vert \mathbf{\mu}_k,\Sigma_k) \\
\sum_{k=1}^K\pi_k &=& 1
\end{array}
\end{equation}$$

下图是3个高斯分布的混合模型

![mixtures_gaussian_1](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/mixtures_gaussian_1.png?raw=true)

假如我们将混合系数 $\pi_k$ 泛化

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}) &=& \sum_{k=1}^Kp(k)p(\mathbf{x}\vert k) \\
\gamma_k(\mathbf{x}) &\equiv& p(k\vert \mathbf{x}) =\frac{p(k)p(\mathbf{x}\vert k)}{\sum_lp(l)p(\mathbf{x}\vert l)}
\end{array}
\end{equation}$$

对混合高斯模型的最大化似然函数采用[EM模型](todo)

## 4. 指数族分布

加入一个分布满足以下条件，则称其属于指数族分布

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}\vert \boldsymbol{\eta}) &=& h(\mathbf{x})g(\boldsymbol{\eta})\exp\{\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\} \\
1 &=& \int h(\mathbf{x})g(\boldsymbol{\eta})\exp\{\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\}d\mathbf{x}
\end{array}
\end{equation}$$

下面从指数族分布来考虑先前我们介绍过的一些分布

### 4.1 伯努利分布

$$\begin{equation}
\begin{array}{rcl}
p(x\vert \mu) &=& Bern(x\vert \mu) = \mu^x(1-\mu)^{(1-x)} \\
&=& \exp\{x\ln\mu+(1-x)\ln(1-u) \} \\
&=& (1-\mu)\exp\{x\ln\frac{\mu}{1-\mu} \} \\
\overset{\eta=\ln\frac{\mu}{1-\mu}}\Rightarrow \mu &=& \sigma(\eta)=\frac{1}{1+\exp(-\eta)} \\
\Rightarrow p(x\vert \eta) &=& \sigma(-\eta)\exp(\eta x)
\end{array}
\end{equation}$$

对照指数族分布的形式，我们有

$$\begin{equation}
\begin{array}{rcl}
u(x) &=& x \\
h(x) &=& 1 \\
g(\eta) &=& \sigma(-\eta)
\end{array}
\end{equation}$$

这就是为什么我在二分类的逻辑回归问题中我们用logistics sigmoid function的原因。

### 4.2 多项式分布

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}\vert \mathbf{\mu}) &=& \prod_{k=1}^K\mu_k^{x_k} = \exp\{\sum_{k=1}^Kx_k\ln\mu_k \} \\
\overset{\eta_k=\ln\mu_k}\Rightarrow  p(\mathbf{x}\vert \boldsymbol\eta) &=& \exp(\boldsymbol{\eta}^T\mathbf{x}) \\
u(x) &=& x \\
h(x) &=& 1 \\
g(\eta) &=& 1
\end{array}
\end{equation}$$

我们知道 $\eta_k$ 不是独立的，因为 $\sum_{k=1}^K\mu_k=1$，加入我们只保留 $K-1$ 个参数，那么就会得到类似伯努利分布的指数形态。

$$\begin{equation}
\begin{array}{rcl}
 \exp\{\sum_{k=1}^Kx_k\ln\mu_k \} &=& \exp\{\sum_{k=1}^{K-1}x_k\ln\mu_k+(1-\sum_{k=1}^{K-1}x_k)\ln(1-\sum_{k=1}^{K-1}\mu_k) \} \\
&=& \exp\{\sum_{k=1}^{K-1}x_k\ln{\frac{\mu_k}{1-\sum_{k=1}^{K-1}\mu_k} }+\ln(1-\sum_{k=1}^{K-1}\mu_k) \} \\
\overset{\eta_k=\ln{\frac{\mu_k}{1-\sum_{k=1}^{K-1}\mu_k} }}\Rightarrow
\mu_k &=& \frac{\exp(\eta_k)}{1+\sum_{j=1}^{K-1}\exp(\eta_j)}\\
p(\mathbf{x}\vert \boldsymbol\eta) &=& (1+\sum_{j=1}^{K-1}\exp(\eta_j))^{-1}\exp(\boldsymbol{\eta}^T\mathbf{x}) \\
u(x) &=& x \\
h(x) &=& 1 \\
g(\eta) &=& (1+\sum_{j=1}^{K-1}\exp(\eta_j))^{-1}
\end{array}
\end{equation}$$

$\mu_k = \frac{\exp(\eta_k)}{1+\sum_{j=1}^{K-1}\exp(\eta_j)}$ 即softmax函数。

### 4.3 高斯分布

$$\begin{equation}
\begin{array}{rcl}
p(x\vert \mu,\sigma^2) &=& \frac{1}{\sqrt{2\pi\sigma^2}}\exp(-\frac{(x-\mu)^2}{2\sigma^2})\\
&=&  \frac{1}{\sqrt{2\pi\sigma^2}}\exp(-\frac{x^2-2x\mu+\mu^2}{2\sigma^2}) \\
\boldsymbol{\eta} &=& \begin{pmatrix}  \mu/\sigma^2 \\ -1/2\sigma^2 \end{pmatrix}\\
u(x) &=& \begin{pmatrix}  x \\ x^2 \end{pmatrix} \\
h(x) &=& (2\pi)^{-1/2} \\
g(\eta) &=& (-2\eta_2)^{1/2}\exp(\frac{\eta_1^2}{4\eta_2})
\end{array}
\end{equation}$$

### 4.4 指数族分布的最大似然

在介绍指数族分布的最大似然之前，我们先求一下指数族分布的真实 $\boldsymbol{\eta}$

$$\begin{equation}
\begin{array}{rcl}\
\int h(\mathbf{x})g(\boldsymbol{\eta})\exp\{\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\}d\mathbf{x} &=& 1
\end{array}
\end{equation}$$

上式两边对 $\boldsymbol{\eta}$ 求导，得

$$\begin{equation}
\begin{array}{rcl}\
\nabla g(\boldsymbol{\eta}) \int h(\mathbf{x})\exp\{\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\}d\mathbf{x}+\int h(\mathbf{x})g(\boldsymbol{\eta})\exp\{\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\}\mathbf{u}(\mathbf{x})d\mathbf{x} &=& 0 \\
\Rightarrow -\nabla\ln g(\boldsymbol{\eta}) = \int h(\mathbf{x})g(\boldsymbol{\eta})\exp\{\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\}\mathbf{u}(\mathbf{x})d\mathbf{x} &=& \mathbf{E}[\mathbf{u}(\mathbf{x})]
\end{array}
\end{equation}$$

当 $\mathbf{X} = \{\mathbf{x}_1,\dots,\mathbf{x}_N \}, p(\mathbf{x}_n\vert \boldsymbol{\eta})=h(\mathbf{x}_n)g(\boldsymbol{\eta})\exp(\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\}$，那么它的似然函数为

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{X}\vert \boldsymbol{\eta}) &=& \{\prod_{n=1}^Nh(\mathbf{x}_n)\}g(\boldsymbol{\eta})^N\exp\{\sum_{n=1}^N\{\boldsymbol{\eta}^T\mathbf{u}(\mathbf{x})\}\} \\
\overset{\frac{\partial{\ln p}}{\partial{\boldsymbol{\eta}} }=0 }\Rightarrow
-\nabla\ln g(\boldsymbol{\eta}_{ML}) &=& \frac{1}{N}\sum_{n=1}^N\mathbf{u}(\mathbf{x})
\end{array}
\end{equation}$$
从而当 $N\to\infty$时，$\boldsymbol{\eta}_{ML}$ 等于真实的  $\boldsymbol{\eta}$ 。

### 4.5 共轭先验

由似然函数的形式我们得到指数族的共轭先验

$$\begin{equation}
\begin{array}{rcl}
p(\boldsymbol{\eta}\vert \mathbf{\chi},\nu) &=& f(\mathbf{\chi},\nu)g(\boldsymbol{\eta})^{\nu}\exp\{\nu \boldsymbol{\eta}^T\mathbf{\chi} \}
\end{array}
\end{equation}$$

其中 $f(\mathbf{\chi},\nu)$ 是归一化系数

$$\begin{equation}
\begin{array}{rcl}
p(\boldsymbol{\eta}\vert \mathbf{X},\mathbf{\chi},\nu) &\propto& g(\boldsymbol{\eta})^{\nu+N}\exp\{\boldsymbol{\eta}^T(\sum_{n=1}^N\mathbf{x}_n+\nu \mathbf{\chi}) \}
\end{array}
\end{equation}$$

### 4.6 无信息(noninformative)先验

由于 $p(y\vert x)\propto p(x\vert y)p(y)$ ，如果先验在某些点上概率为0，就算我们之后观测到这些点，但是后验的概率也为0。而通常情况下，我们并不知道数据的概率分布函数，因此为解决上述问题，引入了noninformative prior。(也没太理解PRML这节的组织形式，先放着之后再看)

## 5. 非参数(nonparametric)法

之前的概率分布都是一些具有特定形式的函数，并且能有少数的参数来控制这个函数的形态，但是采用这种方法会很大的局限性，因为我们选取的概率分布函数有可能不能捕捉数据的潜在概率分布。比如说高斯分布中，数据的潜在概率分布可能具有多个峰值，但是高斯分布是个单峰函数，因此在预测新的数据的时候，预测性能会很差，这种问题可以用混合高斯分布等扩展方法来解决。我们下面介绍另一个方法：非参数法。

假设观测值是从一个未知的概率分布 $p(\mathbf{x})$ 中选取的，$\mathbf{x}$ 是D维空间的点，那么对于一个很小的区域 $\mathcal{R}$ ，这个区域的概率质量为

$$\begin{equation}
\begin{array}{rcl}
P &=& \int_\mathcal{R}p(\mathbf{x})d\mathbf{x}
\end{array}
\end{equation}$$

现在假设我们从 $p(\mathbf{x})$ 中选取N个观测值，那么落在区域 $\mathcal{R}$ 的观测值个数 $K$ 服从二项分布

$$\begin{equation}
\begin{array}{rcl}
Bin(K\vert N,P) &=& \frac{N!}{K!(N-K)!}P^{K}(1-P)^{N-K} \\
E[K/N] &=& P &(E[K]=P)\\
var[K/N] &=& P(1-P)/N &(var[K]=NP(1-P)) \\
\end{array}
\end{equation}$$

当N很大的时候，由 $var[K/N] = P(1-P)/N$ 知道分布会集中分布中均值上，此时的均值 $K\simeq NP$

当 $\mathcal{R}$ 足够小，那么在这个区域上 $p(\mathbf{x})$ 近似为一个常数，那么 $P\simeq p(\mathbf{x})V$。从而有 $p(\mathbf{x})=\frac{K}{NV}$ (两个假设，在实际应用中会不会导致结果相差太大?)。这个估计公式主要由 $K,V$ 两个值决定

### 5.1 核密度估计(kernel density estimation)

固定 $V$，从数据中决定 $K$。为了简化，我们使 $\mathcal{R}$ 是待估计值 $\mathbf{x}$ 上的边长为h超立方体(hypercube)在。当 $\mathbf{x}_n$ 与 $\mathbf{x}$ 满足一定条件时，$\mathbf{x}_n$ 就落在该超立方体里。我们介绍Parzen window

$$\begin{equation}
\begin{array}{rcl}
k(\mathbf{u}) &=& \begin{cases} 1,&\vert u_i\vert \le1/2,i=1,\dots,D, \\
0,&otherwise
\end{cases}
\end{array}
\end{equation}$$

我们取 $k((\mathbf{x}-\mathbf{x}_n)/h)$，那么 $K=\sum_{n=1}^Nk(\frac{\mathbf{x}-\mathbf{x}_n}{h})$，那么对于 $p(\mathbf{x})$，我们就得到

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}) &=& \frac{1}{Nh^D}\sum_{n=1}^Nk(\frac{\mathbf{x}-\mathbf{x}_n}{h})
\end{array}
\end{equation}$$

Parzen window核函数会导致求出的 $p'(\mathbf{x})$ 人为的不连续性，而不是因为 $p(\mathbf{x})$ 本身引起的。因此采用另一个核函数——高斯核函数


$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}) &=& \frac{1}{N\sqrt{2\pi h^2}}\sum_{n=1}^N \exp(-\frac{\vert \vert \mathbf{x}-\mathbf{x}_n\vert \vert ^2}{2h^2})
\end{array}
\end{equation}$$

![gaussian_kernel](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/gaussian_kernel.png?raw=true)

我们可以看到从现在的超参数就变成了 $h$ 。

### 5.2 最邻近法(Nearest-neighbour methods)

核方法的一个局限性是，它对于大部分空间上具有相同的密度，但是有些数据集可能在某些区域密度较为集中，而另一些区域密度相对较小，那么核方法中的 $h$ 就难以选择。此时我们考虑固定 $K$，而使用数据去找到合适的 $V$.

![nearest_neighbour](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/nearest_neighbour.png?raw=true)

假设一个数据集一共有N个实例，其中有 $N_k$ 个属于类 $C_k$，$\sum_{k}N_k=N$。如果我们想分类一个新的实例 $\mathbf{x}$ ，我们以 $\mathbf{x}$ 为中心选取 $K$ 个实例。那么在这里面有 $K_k$ 个实例属于类 $C_k$。

$$\begin{equation}
\begin{array}{rcl}
p(\mathbf{x}\vert C_k) &=& \frac{K_k}{N_kV} \\
p(\mathbf{x}) &=& \frac{K}{NV} \\
p(C_k) &=& \frac{K_k}{N_k} \\
\Rightarrow p(C_k\vert \mathbf{x}) &=& \frac{p(\mathbf{x}\vert C_k)p(C_k)}{p(\mathbf{x})} = \frac{K_k}{K} \\
\end{array}
\end{equation}$$
K-近邻算法的概率解释，通俗解释就是选取与测试实例最近的 $K$ 个实例，如果这 $K$ 个实例大多数都属于某个类 $C_k$，就讲测试实例分类到 $C_k$。

![KNN_example](https://github.com/Lehyu/lehyu.cn/blob/master/image/PRML/chap2/KNN_example.png?raw=true)

从上图的例子我们知道，$K$ 值选取会决定分类算法的性能。K-近邻算法需要保存整个数据集，当数据量很大时，计算十分昂贵，所以为了解决这个问题，可以构造[树形搜索结构](todo)来加速计算。
