---
title: 'MAMBO-G: Magnitude-Aware Mitigation for Boosted Guidance'
description: 'Introducing a novel strategy to mitigate instability in boosted guidance for diffusion models, improving both quality and speed.'
pubDate: 'Dec 17 2025'
pinned: true
heroImage: '/Matrix-omini/mambo-images/header.png'
tags:
  - diffusion-models
  - generative-ai
  - research
  - mambo-g
---

# MAMBO-G: Magnitude-Aware Mitigation for Boosted Guidance

**Authors:** Shangwenzhu et al.

![MAMBO-G Header](/Matrix-omini/mambo-images/header.png)

## Abstract

Classifier-Free Guidance (CFG) is a crucial technique in modern text-to-image and text-to-video generation, significantly improving the alignment between generated content and text prompts. However, as model scales increase (e.g., SD3, Lumina, WAN2.1), strong guidance often leads to instability, manifesting as oversaturated colors, unnatural structures, and artifacts.

We introduce **MAMBO-G** (Magnitude-Aware Mitigation for Boosted Guidance), a novel adaptive guidance strategy designed to address these challenges. By analyzing the dynamics of guidance in high-dimensional latent spaces, we identify that initial guidance updates often point in a generic direction, leading to "overshoot" when strong guidance is applied early. MAMBO-G mitigates this by dynamically adjusting the guidance scale based on a "Magnitude-Aware Ratio," ensuring stability without compromising text alignment.

## Key Challenges

Recent large-scale diffusion and flow-matching models face specific challenges with CFG:

1.  **Instability at High Guidance:** Strong guidance, while necessary for prompt adherence, often collapses the generation process into artifacts or oversaturated images.
2.  **The "Overshoot" Phenomenon:** In high-dimensional spaces, the initial noise ($t=1$) is statistically independent of the target data. The guidance update at this stage tends to be a generic direction determined solely by the text prompt. Applying large CFG scales here forces the generation trajectory to deviate significantly from the data manifold.

![Interval Comparison](/Matrix-omini/mambo-images/interval.png)

## The MAMBO-G Solution

MAMBO-G proposes a magnitude-aware adaptive guidance strategy. The core innovation lies in defining a **Magnitude-Aware Ratio ($r_t$)**:

$$
r_t(\mathbf{x}_t, t) = \frac{\|\mathbf{v}_{\text{cond}}(\mathbf{x}_t, t) - \mathbf{v}_{\text{uncond}}(\mathbf{x}_t, t)\|}{\|\mathbf{v}_{\text{uncond}}(\mathbf{x}_t, t)\|}
$$

This ratio measures the relative strength of the "external guidance force" against the "internal denoising force" (specifically using the **unconditional prediction as the base**). A high ratio indicates a potential risk of overshoot.

Based on this ratio, MAMBO-G applies an adaptive damping factor to the guidance scale:

$$
w(r_t) = 1 + w_{\max} \cdot \exp(-\alpha r_t)
$$

This ensures that guidance is suppressed when the risk is high (typically at the very beginning of sampling) and restored as the image structure becomes clearer.

## Key Features & Results

-   **Enhanced Stability:** Effectively suppresses overshoot in early sampling stages, reducing oversaturation and structural collapse.
-   **Accelerated Inference:** Achieves high-quality generation in fewer steps. Experiments show comparable quality to standard CFG with significantly fewer steps (e.g., 3x speedup on SD3.5, 4x on Lumina).
-   **Plug-and-Play Compatibility:** MAMBO-G does not require model retraining. It can be easily integrated into existing DiT (Diffusion Transformer) architectures and samplers.

## Conclusion

MAMBO-G offers a theoretically grounded and practically effective solution to the instability problems of Classifier-Free Guidance in high-dimensional generative models. By making guidance magnitude-aware, it unlocks the full potential of large-scale models, enabling faster and higher-quality generation.

---

*This project is supported by National Natural Science Foundation of China and National Key Research Projects.*
