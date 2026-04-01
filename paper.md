# 3. Methodology

PACE operates as a structured **global-to-local adaptive enhancement pipeline**, integrating statistical feature extraction, parameter inference, spatial modulation, and perceptual multi-signal blending.

---

## 3.1 Pipeline Overview

The pipeline consists of four sequential stages:

1. **Global feature extraction**  
2. **Adaptive parameter computation**  
3. **Local strength modulation**  
4. **Multi-signal adaptive blending**

---

## 3.2 Distribution Features

Let \( L = \{L_i\}_{i=1}^{N} \) denote luminance values.

**Mean**
```math
\mu = \frac{1}{N} \sum_{i=1}^{N} L_i
```

**Variance**
```math
\sigma^2 = \max\left(0,\; \frac{1}{N} \sum L_i^2 - \mu^2 \right)
```

**Standard Deviation**
```math
\sigma = \sqrt{\sigma^2}
```

**Skewness**
```math
\text{Skewness} = \frac{1}{N (\sigma + \epsilon)^3} \sum (L_i - \mu)^3
```

**Kurtosis**
```math
\text{Kurtosis} = \frac{1}{N (\sigma + \epsilon)^4} \sum (L_i - \mu)^4
```

**Entropy**
```math
H = - \sum p_k \log_2(p_k + \epsilon)
```

**Normalized Entropy**
```math
H_{norm} = \frac{H}{\log_2 K}
```

**Dynamic Range**
```math
D = P_{95} - P_{5}
```

**Shadow Ratio**
```math
R_s = \frac{1}{N} \sum \mathbf{{1}}(L_i < 0.2)
```

**Highlight Ratio**
```math
R_h = \frac{1}{N} \sum \mathbf{{1}}(L_i > 0.8)
```

---

## 3.3 Adaptive Parameter Computation

```math
C_{need} = (1 - H_{norm})(1 - D)
```

```math
S_{conf} = \frac{E_d}{1 + N_r}
```

```math
I_{imb} = |R_s - R_h|
```

```math
\alpha = \frac{0.5 I_{imb} + 0.3 C_{need} + 0.4 S_{conf}}{0.5 + 0.5 I_{imb} + 0.3 C_{need} + 0.4 S_{conf}}
```

---

## 3.4 Local Adaptive Strength

```math
G = \max(|g_x|,|g_y|) + 0.25 \min(|g_x|,|g_y|)
```

```math
S = \frac{\mu_G \cdot C}{1 + N_l}
```

```math
\alpha(x,y) = \mathrm{{clip}}(\alpha [1 + 1.2(C_{{loc}}-0.5)], 0.05, 2\alpha)
```

---

## 3.5 Multi-Signal Blending

```math
\alpha' = \alpha (0.8 + 0.8 \alpha_{{map}})
```

```math
R = \log(L_{{small}}) - \log(L_{{medium}})
```

```math
\Delta = \Delta_c + \eta \cdot \Delta_d \cdot M_d \cdot M_s \cdot M_e
```

```math
L_{{enh}} = \mathrm{{clip}}(L + \Delta' \cdot G_e \cdot M_l \cdot G_c, 0, 1)
```

---

## 3.6 Algorithm

**Algorithm 1: PACE Enhancement**

**Input:** RGB image \( I \)  
**Output:** Enhanced image \( I_{{enh}} \)

1. Convert \( I \) to OKLab and extract luminance \( L \)  
2. Compute global distribution features  
3. Estimate adaptive parameters  
4. Apply CLAHE for base enhancement  
5. Compute spatially adaptive strength map  
6. Apply multi-signal blending  
7. Reconstruct enhanced RGB image  

**Return:** \( I_{{enh}} \)