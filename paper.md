# PACE Methodology

PACE operates as a structured **global-to-local adaptive enhancement pipeline** combining statistical feature extraction, parameter inference, spatial modulation, and multi-signal perceptual blending.

---

## 3.1 Pipeline Overview

The pipeline consists of four distinct stages:

1. **Global feature extraction:** Statistical analysis of the luminance channel.
2. **Adaptive parameter computation:** Deriving gain and control constants.
3. **Local strength modulation:** Spatial adjustment based on image content.
4. **Multi-signal adaptive blending:** Final reconstruction and perceptual refinement.

---

## 3.2 Distribution Features

Let $L = \{L_i\}_{i=1}^{N}$ denote the luminance values of the input image.

$$
\mu = \\frac{1}{N} \\sum_{i=1}^{N} L_i \\tag{1}
$$

$$
\\sigma^2 = \\max\\left(0,\\; \\frac{1}{N} \\sum L_i^2 - \\mu^2 \\right) \\tag{2}
$$

$$
\\sigma = \\sqrt{\\sigma^2} \\tag{3}
$$

$$
\\text{Skewness} = \\frac{1}{N (\\sigma + \\epsilon)^3} \\sum (L_i - \\mu)^3 \\tag{4}
$$

$$
\\text{Kurtosis} = \\frac{1}{N (\\sigma + \\epsilon)^4} \\sum (L_i - \\mu)^4 \\tag{5}
$$

$$
H = - \\sum p_k \\log_2(p_k + \\epsilon) \\tag{6}
$$

$$
H_{\\text{norm}} = \\frac{H}{\\log_2 K} \\tag{7}
$$

$$
D = P_{95} - P_{5} \\tag{8}
$$

$$
R_s = \\frac{1}{N} \\sum \\mathbf{1}(L_i < 0.2) \\tag{9}
$$

$$
R_h = \\frac{1}{N} \\sum \\mathbf{1}(L_i > 0.8) \\tag{10}
$$

---

## 3.3 Adaptive Parameter Computation

The following parameters govern the global enhancement behavior:

$$
C_{\\text{need}} = (1 - H_{\\text{norm}})(1 - D) \\tag{11}
$$

$$
S_{\\text{conf}} = \\frac{E_d}{1 + N_r} \\tag{12}
$$

$$
I_{\\text{imb}} = |R_s - R_h| \\tag{13}
$$

$$
\\alpha = \\frac{0.5 I_{\\text{imb}} + 0.3 C_{\\text{need}} + 0.4 S_{\\text{conf}}}{0.5 + 0.5 I_{\\text{imb}} + 0.3 C_{\\text{need}} + 0.4 S_{\\text{conf}}} \\tag{14}
$$

---

## 3.4 Local Adaptive Strength

Spatial modulation is applied to ensure local details are preserved:

$$
G = \\max(|g_x|,|g_y|) + 0.25 \\min(|g_x|,|g_y|) \\tag{15}
$$

$$
S = \\frac{\\mu_G \\cdot C}{1 + N_l} \\tag{16}
$$

$$
\\alpha(x,y) = \\text{clip}(\\alpha [1 + 1.2(C_{\\text{loc}}-0.5)], 0.05, 2\\alpha) \\tag{17}
$$

---

## 3.5 Multi-Signal Blending

The final luminance is reconstructed through multi-signal integration:

$$
\\alpha' = \\alpha (0.8 + 0.8 \\alpha_{\\text{map}}) \\tag{18}
$$

$$
R = \\log(L_{\\text{small}}) - \\log(L_{\\text{medium}}) \\tag{19}
$$

$$
\\Delta = \\Delta_c + \\eta \\cdot \\Delta_d \\cdot M_d \\cdot M_s \\cdot M_e \\tag{20}
$$

$$
L_{\\text{enh}} = \\text{clip}(L + \\Delta' \\cdot G_e \\cdot M_l \\cdot G_c, 0, 1) \\tag{21}
$$

---

## 3.6 Algorithm

### Algorithm 1: PACE Enhancement

**Input:** RGB image $I$  
**Output:** Enhanced image $I_{\\text{enh}}$

1. **Color Space Conversion:** Convert $I$ to **OKLab** and extract the luminance channel $L$.
2. **Global Stats:** Compute global features using Equations (1) through (10).
3. **Inference:** Compute adaptive parameters $\\alpha$ and $C_{\\text{need}}$.
4. **Contrast:** Apply initial **CLAHE** (Contrast Limited Adaptive Histogram Equalization).
5. **Local Map:** Compute the local alpha map $\\alpha(x,y)$.
6. **Fusion:** Apply multi-signal blending to generate $L_{\\text{enh}}$.
7. **Reconstruction:** Recombine with chromatic channels and convert back to RGB.

**Return** $I_{\\text{enh}}$