# Volume Rendering Theory

体像可以看作由无数微小颗粒组成的集合。当光线进入体像时，会与这些微小颗粒发生相互作用，如吸收、散射、激发等现象。然而，在实际渲染过程中，直接以微小颗粒进行精确的光纤传输建模是不现实的，因为这将带来极其庞大的计算量。因此，我们采取折衷的方法，通过建模光线在体像中平均行为的方式，来近似描述这些复杂的交互过程。

## Radiance Transfer Theory

#### $(\boldsymbol{\omega} \cdot \nabla)$

$(\boldsymbol{\omega} \cdot \nabla)L$ 表示 $L$ 在 $\boldsymbol{\omega}$ 上的方向导数，也等价于 $\nabla_{\boldsymbol{\omega}}L$ 或 $\nabla L \cdot \boldsymbol{\omega}$。根据光线参数化 $\mathbf{x} = \mathbf{x}_{0} + t\boldsymbol{\omega}$，有：
$$
(\boldsymbol{\omega} \cdot \nabla)L = \underbrace{\nabla L}_{\mathbb{R}^{3 \times 1}} \cdot \boldsymbol{\omega} = \underbrace{\frac{\partial L}{\partial \mathbf{x}}}_{\mathbb{R}^{1 \times 3}} \frac{\partial \mathbf{x}}{\partial t} = \frac{\mathrm{d}L}{\mathrm{d}t}
$$

### Absorption

当光线与微粒发生碰撞时，光线的辐射能被微粒吸收，并转化为微粒的内能。这种吸收过程对光线辐射度的变化可表示为以下方程：
$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = - \sigma_{a}(\mathbf{x}) L(\mathbf{x}, \boldsymbol{\omega})
$$

### Emission

$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = \sigma_{a}(\mathbf{x}) L_{e}(\mathbf{x}, \boldsymbol{\omega})
$$
其中 $\sigma_{a}(\mathbf{x})L_{e}(\mathbf{x}, \boldsymbol{\omega})$ 表示微粒的发射能量。实际上这里只需要 $L_{e}(\mathbf{x}, \boldsymbol{\omega})$ 即可表示微粒的发射能量，与 $\sigma_{a}(\mathbf{x})$ 相乘是为了达成形式上的一致性。

### Out-Scattering

$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = - \sigma_{s}(\mathbf{x}) L(\mathbf{x}, \boldsymbol{\omega})
$$

### In-Scattering

$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = \sigma_{s}(\mathbf{x}) \int_{\mathbb{S}^{2}} f_{p}(\mathbf{x}, \boldsymbol{\omega}, \boldsymbol{\omega}') L(\mathbf{x}, \boldsymbol{\omega}') \mathrm{d}\boldsymbol{\omega}' = \sigma_{s}(\mathbf{x}) L_{s}(\mathbf{x}, \boldsymbol{\omega})
$$

### Radiance Transfer Equation

$$
(\boldsymbol{\omega} \cdot \nabla) L(\mathbf{x}, \boldsymbol{\omega}) = \underbrace{-\sigma_{t}(\mathbf{x})L(\mathbf{x}, \boldsymbol{\omega})}_{\text{Losses}} + \underbrace{\sigma_{a}(\mathbf{x})L_{e}(\mathbf{x}, \boldsymbol{\omega}) + \sigma_{s}(\mathbf{x})L_{s}(\mathbf{x}, \boldsymbol{\omega})}_{\text{Gains}}
$$

## Volume Rendering Equation

Volume Rendering Equation 是 Radiance Transfer Equation 的空间积分形式。为了方便分析，首先将其改写为与 $t$ 有关的形式：
$$
\frac{\mathrm{d}}{\mathrm{d}t}L(t) = -\sigma_{t}(\mathbf{x})L(t) + \sigma_{a}(\mathbf{x})L_{e}(t) + \sigma_{s}(\mathbf{x})L_{s}(t)
$$
记光线的起始点为 $t = d$（起始点可以是光源、表面或其他体像位置），则有：
$$
L(t) = L(d)\exp\left(-\int_{d}^{t}\sigma_{t}(\mathbf{x}_{s}) \mathrm{d}s\right) + \int_{d}^{t}[\sigma_{a}(\mathbf{x}_{s})L_{e}(s) + \sigma_{s}(\mathbf{x}_{s})L_{s}(s)] \exp\left(-\int_{s}^{t}\sigma_{t}(\mathbf{x}_{u}) \mathrm{d}u\right) \mathrm{d}s
$$
在实际光线追踪过程中，需要注意其光线的方向与上述公式中光线的方向相反。因此，应反转光线的方向，将光线参数化为 $\mathbf{x} = \mathbf{x}_{0} - t\boldsymbol{\omega}$。将这一改变带入上面的公式：
$$
L(t) = 
$$

## Tracking

### Closed-from Tracking

对于只有吸收和外散射的场景，而且消光系数 $\sigma_{t}(\mathbf{x})$ 为常数的场景，
