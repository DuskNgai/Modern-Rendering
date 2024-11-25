# Volume Rendering Theory

体像可以看作由无数微小颗粒组成的集合。当光线进入体像时，会与这些微小颗粒发生相互作用，如吸收、散射、激发等现象。然而，在实际渲染过程中，直接以微小颗粒进行精确的光纤传输建模是不现实的，因为这将带来极其庞大的计算量。因此，我们采取折衷的方法，通过建模光线在体像中平均行为的方式，来近似描述这些复杂的交互过程。

## Radiance Transfer Theory

#### Meaning of $(\boldsymbol{\omega} \cdot \nabla)$

$(\boldsymbol{\omega} \cdot \nabla)L$ 表示 $L$ 在 $\boldsymbol{\omega}$ 上的方向导数，也等价于 $\nabla_{\boldsymbol{\omega}}L$ 或 $\nabla L \cdot \boldsymbol{\omega}$。根据光线参数化 $\mathbf{x} = \mathbf{x}_{0} + t\boldsymbol{\omega}$，有：
$$
(\boldsymbol{\omega} \cdot \nabla)L = \underbrace{\nabla L}_{\mathbb{R}^{3 \times 1}} \cdot \boldsymbol{\omega} = \underbrace{\frac{\partial L}{\partial \mathbf{x}}}_{\mathbb{R}^{1 \times 3}} \frac{\partial \mathbf{x}}{\partial t} = \frac{\mathrm{d}L}{\mathrm{d}t}
$$

### Absorption

当光线与微粒发生碰撞时，光线的辐射能被微粒吸收，转化为微粒的内能。
$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = - \sigma_{a}(\mathbf{x}) L(\mathbf{x}, \boldsymbol{\omega})
$$

### Emission

在光线传播的路径上，微粒可能会自行或者受激发射辐射能。
$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = \sigma_{a}(\mathbf{x}) L_{e}(\mathbf{x}, \boldsymbol{\omega})
$$
其中 $\sigma_{a}(\mathbf{x})L_{e}(\mathbf{x}, \boldsymbol{\omega})$ 表示微粒的发射能量。实际上这里只需要 $L_{e}(\mathbf{x}, \boldsymbol{\omega})$ 即可表示微粒的发射能量，与 $\sigma_{a}(\mathbf{x})$ 相乘是为了达成形式上的一致性。

### Out-Scattering

当光线与微粒发生碰撞时，光线的辐射能被微粒散射，能量辐射到其他方向上。
$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = - \sigma_{s}(\mathbf{x}) L(\mathbf{x}, \boldsymbol{\omega})
$$

### In-Scattering

在光线传播的路径上，其他方向的辐射能量会被微粒散射，转移到当前方向上。
$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = \sigma_{s}(\mathbf{x}) \int_{\mathbb{S}^{2}} f_{p}(\mathbf{x}, \boldsymbol{\omega}, \boldsymbol{\omega}') L(\mathbf{x}, \boldsymbol{\omega}') \mathrm{d}\boldsymbol{\omega}' = \sigma_{s}(\mathbf{x}) L_{s}(\mathbf{x}, \boldsymbol{\omega})
$$

### Radiance Transfer Equation

$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = \underbrace{-\sigma_{t}(\mathbf{x})L(\mathbf{x}, \boldsymbol{\omega})}_{\text{Losses}} + \underbrace{\sigma_{a}(\mathbf{x})L_{e}(\mathbf{x}, \boldsymbol{\omega}) + \sigma_{s}(\mathbf{x})L_{s}(\mathbf{x}, \boldsymbol{\omega})}_{\text{Gains}}
$$

## Volume Rendering Equation

Volume Rendering Equation 是 Radiance Transfer Equation 的空间积分形式。为了便于分析，我们首先将其改写为关于 $t$ 的微分形式：
$$
\frac{\mathrm{d}}{\mathrm{d}t}L(t) = -\sigma_{t}(\mathbf{x}_{t})L(t) + \sigma_{a}(\mathbf{x}_{t})L_{e}(t) + \sigma_{s}(\mathbf{x}_{t})L_{s}(t),
$$
光线一般从光源、表面或者其他体像表面发射出来。设光线的起点为 $\mathbf{x}_{d}$，终点为 $\mathbf{x}_{0}$。在实际光线追踪实现中，通常定义光线的参数化方向与上述公式中的光线方向相反，即光线沿负方向传播，将光线重新参数化为 $\mathbf{x} = \mathbf{x}_{0} - t\boldsymbol{\omega}$。将此参数化方式代入上述公式，得到：
$$
L(0) = L(d)\exp\left(-\int_{0}^{d}\sigma_{t}(\mathbf{x}_{t}) \mathrm{d}t\right) + \int_{0}^{d}\left[\sigma_{a}(\mathbf{x}_{t})L_{e}(t) + \sigma_{s}(\mathbf{x}_{t})L_{s}(t)\right] \exp\left(-\int_{0}^{t}\sigma_{t}(\mathbf{x}_{s}) \mathrm{d}s\right) \mathrm{d}t.
$$
改写为坐标的形式为：
$$
L(\mathbf{x}_{0}, \boldsymbol{\omega}) = L(\mathbf{x}_{d}, \boldsymbol{\omega})\exp\left(-\int_{0}^{d}\sigma_{t}(\mathbf{x}_{t})\mathrm{d}t\right) + \int_{0}^{d}\left[\sigma_{a}(\mathbf{x}_{t})L_{e}(\mathbf{x}_{t}, \boldsymbol{\omega}) + \sigma_{s}(\mathbf{x}_{t})L_{s}(\mathbf{x}_{t}, \boldsymbol{\omega})\right] \exp\left(-\int_{0}^{t}\sigma_{t}(\mathbf{x}_{s}) \mathrm{d}s\right) \mathrm{d}t
$$

### Transmittance

在上述解析解中，可以发现一个指数衰减项。我们将其定义为透射率：
$$
T(t) = \exp\left(-\int_{0}^{t}\sigma_{t}(\mathbf{x}_{s}) \mathrm{d}s\right)
$$
改写为坐标的形式为：
$$
T(\mathbf{x}_{0}, \mathbf{x}_{t}) = \exp\left(-\int_{0}^{t}\sigma_{t}(\mathbf{x} - s\boldsymbol{\omega}) \mathrm{d}s\right)
$$
带入解析解，得到：
$$
L(\mathbf{x}_{0}, \boldsymbol{\omega}) = T(\mathbf{x}_{0}, \mathbf{x}_{d})L(\mathbf{x}_{d}, \boldsymbol{\omega}) + \int_{0}^{d} T(\mathbf{x}_{0}, \mathbf{x}_{t})\left[\sigma_{a}(\mathbf{x}_{t})L_{e}(\mathbf{x}_{t}, \boldsymbol{\omega}) + \sigma_{s}(\mathbf{x}_{t})L_{s}(\mathbf{x}_{t}, \boldsymbol{\omega})\right] \mathrm{d}t
$$

### Monte Carlo Integration

上述解析解的计算量较大，可以使用 Monte Carlo 方法进行近似计算。对应的估计器为：
$$
\langle L(\mathbf{x}_{0}, \boldsymbol{\omega}) \rangle = \underbrace{\frac{T(\mathbf{x}_{0}, \mathbf{x}_{d})}{p(d)}}_{\text{todo}}L(\mathbf{x}_{d}, \boldsymbol{\omega}) + \frac{T(\mathbf{x}_{0}, \mathbf{x}_{t})}{p(t)} \left[\sigma_{a}(\mathbf{x}_{t})L_{e}(\mathbf{x}_{t}, \boldsymbol{\omega}) + \sigma_{s}(\mathbf{x}_{t})L_{s}(\mathbf{x}_{t}, \boldsymbol{\omega})\right]
$$

## Distance Sampling

Monte Carlo 积分中的 PDF 选取是高效计算积分的关键。

### Free-path Sampling

一种选取方式是

### Closed-from Tracking

对于只有吸收和外散射的场景，而且消光系数 $\sigma_{t}(\mathbf{x})$ 为常数的场景，此时透射率有解析形式：
$$
T(t) = \exp\left(-\sigma_{t}t\right)
$$
因此 CDF 为：
$$
F(t) = 1 - \exp\left(-\sigma_{t}t\right)
$$
PDF 为：
$$
p(t) = \sigma_{t}\exp\left(-\sigma_{t}t\right)
$$
逆采样得到的距离为：
$$
t = -\frac{\ln(1 - \xi)}{\sigma_{t}}
$$

