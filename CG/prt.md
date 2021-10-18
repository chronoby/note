# Precomputed Radiance Transfer

尽管渲染方程十分优雅、简洁，但直接求解是不切实际的，因为积分域包含了无穷的方向。Precomputed Radiance Transfer(PRT)是一个计算实时全局光照的尝试，他把问题分成了两部分，一部分在预计算步骤解决，另一部分实时计算。通过一些简化和近似，PRT 等式可以直接从渲染方程推导出来。

## PTR

PRT 又两个主要的假设：
- 场景中的所有物体不是发光体
- 光源是无穷远的(使入射光独立于入射点的位置)

$$
L_o(p,\omega_o) = \int_{\Omega^+} L_i(p,\omega_i)\rho(p,\omega_i,\omega_o)V(p)max(0,n \cdot\omega_i)\mathrm{d}\omega_i
$$

另一个 PRT 的假设是仅考虑漫反射(disfuse/Lambertian reflectance)，这种情况下，$\rho$ 是反射系数/Pi(常数)。我们可以将原方程写为

$$
L_o(p,\omega_o) = \rho \int_{\Omega^+} L_i(p,\omega_i) T(p, \omega_o, \omega_i)\mathrm{d}\omega_i
$$

可见，传输部分和光照部分是相互独立的，可以分开计算。而 T 可以预计算。

## 基函数

给定一个函数$f(x)$，基函数 $\beta$ 是一个用来投影和重构的函数的无限集。函数 &f& 到任何函数 $\beta$ 的投影用 $c_i$ 来衡量

$$
c_i = \int f(x)\beta_i(x)\mathrm{d}x
$$

原函数 $f$ 可以写为

$$
f(x) = \lim\limits_{n\to\infty}\sum\limits_{i=1}^{n}c_i\beta_i(x)
$$

只取有限个(n个)项

$$
f(x) = \sum\limits_{i=1}^{n}c_i\beta_i(x)
$$

对于 PRT，我们使用正交的基函数，即任意两个不同基函数的积分为 0。同时提供了一个非常好的性质：

$$
c_k = \int f(x)\beta_k(x)\mathrm{d}x\\
d_k = \int g(x)\beta_k(x)\mathrm{d}x\\
\int f(x)g(x)\mathrm{d}x=\int \sum\limits_{k=1}^{n}c_k\beta_k(x) \sum\limits_{k=1}^{n}d_k\beta_k(x)\mathrm{d}x\\
=\sum\limits_{k=1}^{n}c_kd_k\int\beta_k(x)\beta_k(x)\mathrm{d}x\\
=\sum\limits_{k=1}^{n}c_kd_k
$$

在 PRT 中，我们使用的正交基函数为 Spherical Harmonics(球面谐波函数)

这提供了一个有用的机制去分解渲染方程：

1. 将 $L$ 和 $T$ 投影到正交基(预处理阶段完成)
2. 将积分近似为点积(运行时进行计算)
