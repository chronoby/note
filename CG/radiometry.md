# Radiometry 辐射度量学

**辐射度量学**是衡量电磁辐射强弱的学科

## 主要概念

### Radiant Energy

- **Radiant Energy** (辐射能) Q: 电磁辐射的能量

$$
Q[J=Joule]
$$

### radiant flux (power)

- **radiant flux** (辐射通量) or **radiant power** (辐射功率) is the radiant energy emitted, reflected, transmitted or received, per unit time

$$
\Phi \equiv \frac{\mathrm{d}Q}{\mathrm{d}t}[W=Watt][lm=lumen]^*
$$

### Radiant Intensity

- Radiant Intensity (辐射强度): the radiant flux emitted, reflected, transmitted or received, per unit solid angle

$$
I \equiv \frac{\mathrm{d}\Phi}{\mathrm{d}\Omega}[W/sr][lm/sr=cd=candela]^*
$$

### Irradiance

Irradiance(辐照度): the radiant flux (power) received by a surface per unit area

$$
E(x) \equiv \frac{\mathrm{d}\Phi(x)}{\mathrm{d}A}[W/m^2][lm/m^2=lux]^*
$$

### Radiance

Radiance(辐射度): the radiant flux emitted, reflected, transmitted or received by a given surface, per unit solid angle per unit projected area

$$
L(p,\omega) \equiv \frac{\mathrm{d^2}\Phi(p, \omega)}{\mathrm{d}\omega\mathrm{d}A\cos\theta}[W/(sr \cdot m^2)][cd/m^2=nit]^*
$$

## Fresnel Term

- 菲涅尔项描述了有多少光被反射和折射
- 可以用 Schlick's approximation 来简化计算

## BRDF

Bidirectional Reflectance Distribution Function(BRDF) 描述了入射光线在每个出射方向上反射了多少能量，具体定义为：材质表面某一点上出射辐射度（Radiance）和入射辐照度（Irradiance）的比值

$$
f_r(\omega_i \rightarrow \omega_r) = \frac{\mathrm{d}L_r(\omega_r)}{\mathrm{d}E_i(\omega_i)}= \frac{\mathrm{d}L_r(\omega_r)}{L_i(\omega_i)\cos\theta_i\mathrm{d}\omega_i}[l/sr]
$$

## The Reflection Equation

$$
L_r(p,\omega_r) = \int_{H^2} f_r(p,\omega_i \rightarrow \omega_r) L_i(p,\omega_i)\cos\theta_i\mathrm{d}\omega_i
$$

## The Rendering Equation

$$
L_o(p,\omega_o) = L_e(p,\omega_o) + \int_{\Omega^+} f_r(p,\omega_i,\omega_o) L_i(p,\omega_i)(n \cdot\omega_i)\mathrm{d}\omega_i
$$
