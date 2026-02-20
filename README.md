# *Less Is More, but Where?*<br>Dynamic Token Compression via LLM-Guided Keyframe Prior

This repository is the official PyTorch implementation of [DyToK](https://openreview.net/pdf/b4a33413bf051e6a2476bdf1b3f34a1cab246ff1.pdf).

## 📚 TABLE OF CONTENTS

1. [Motivation](#-motivation)
2. [Method](#-method)
3. [News](#-news)
4. [TODO](#-todo)
5. [Installation](#-installation)
6. [Quick Start](#-quick-start)
7. [Reproducing Results](#-reproducing-results)
8. [Development](#️-development)
9. [Acknowledgements](#️-acknowledgements)
10. [Citation](#-citation)

## 🎯 Motivation

![Unveiling the keyframe prior in VLLMs](assets/motivation.png)
**Unveiling the keyframe prior in VLLMs.** We visualize the averaged attention from the final text token to visual tokens across all layers for each frame. The top-8 frames by attention scores are shown chronologically, with ground truth (GT) keyframes highlighted in red. We observe that *even when the model answers incorrectly, its attention still pinpoints relevant frames, revealing a strong task-dependent keyframe prior.*

## 🌈 Method

![Illustration of DyToK](assets/method.png)
**Illustration of DyToK.** We adaptively compress video tokens through two synergistic components:

1. **Temporal Importance Estimation** leverages cross-modal attention from a lightweight assistant model to identify keyframes;
2. **Dynamic Frame-Level Compression** that proportionally allocates token budgets to preserve salient content.

## 🎉 News

* **[2026.01.26]**  Our training-free inference acceleration framework [FlashVID](https://github.com/Fanziyang-v/FlashVID) has been accepted at **ICLR 2026**.
* **[2025.12.06]**  Released code for integrating DyToK with encoder feature-based pruning methods.
* **[2025.09.18]**  Our paper has been accepted at **NeurIPS 2025**.

## 🔥 TODO

* [x] Initialize Project.
* [ ] Release code for integrating DyToK with LLM attention-based pruning methods.
* [ ] Add support for Qwen3-VL.

## 📦 Installation

DyToK's code is extremely concise and works out of the box. Just install and go!

#### 1. Quick Install

Install the latest stable version directly from [PyPI](https://pypi.org/project/dytok/0.1.0/):

```bash
pip install dytok
```

#### 2. Development Install

Clone the repository and install in editable mode:

```bash
git clone https://github.com/yu-lin-li/DyToK.git
cd DyToK
pip install -e .
```

## 🚀 Quick Start

Integrating DyToK takes just **two lines of code**:

```python
from dytok import visionzip
visionzip(model, dytok=True, use_tiny=True, tiny_model=tiny_model)
```

Try it out with our demo script using LLaVA-OneVision:

```bash
python playground/llavaov_infer.py
```

## 📊 Reproducing Results

All experiments in the paper are based on [LMMs-Eval](https://github.com/EvolvingLMMs-Lab/lmms-eval). Follow these steps to reproduce our results.

#### 1. Setup Environment

```bash
# Create virtual environment
conda create -n dytok python=3.10
conda activate dytok

# Install base models (e.g., LLaVA-OneVision)
pip install git+https://github.com/LLaVA-VL/LLaVA-NeXT.git

# Install DyToK
git clone https://github.com/yu-lin-li/DyToK.git
cd DyToK
pip install -e .

# Install LMMs-Eval streamlined for DyToK
cd eval
pip install -e .
pip install flash-attn==2.6.3  # optional
```

> 💡 Note: Our `eval/` directory contains a minimal, DyToK-focused version of LMMs-Eval. For full functionality, install the [official LMMs-Eval]((https://github.com/EvolvingLMMs-Lab/lmms-eval?tab=readme-ov-file#installation)) separately and integrate DyToK as described in [Development](#️-development).

#### 2. Evaluation

Reproduce DyToK-enhanced VisionZip results on LLaVA-OneVision:

```bash
bash eval/scripts/dytok_visionzip_tiny_32_ov.sh
```

## 🛠️ Development

#### 1. Repository Structure

```bash
.
├── assets/
├── dytok/                    # Core DyToK logic
│   └── visionzip/            # DyToK-enhanced VisionZip
├── eval/
│   ├── lmms_eval/            # Evaluation toolkit
│   │   └── models/           # DyToK-integrated models
│   └── scripts/              # Evaluation scripts
├── playground/               # Demo inference scripts
│   └── llavaov_infer.py
├── pyproject.toml
└── README.md
```

#### 2. Adapt DyToK to Your Own Method

DyToK is designed as a **plug-and-play module**. To integrate it into your token compression method:

* Look for code blocks explicitly annotated to isolate DyToK-specific logic from the base method, as shown below:

```python
# ! ———— DyToK Begin ————
...
# ! ———— DyToK End ————
```

* Migrate the enclosed logic into your method.

> ✅ Pro Tip: Use the [Better Comments](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments) extension in [VSCode](https://code.visualstudio.com/) to highlight DyToK annotations in red!

#### 3. Integrate with Your Own LMMs-Eval

To add DyToK support to your local LMMs-Eval:

```bash
cp eval/lmms_eval/models/*.py <YOUR_LMMS_EVAL_PATH>/models/
```

Then register the model in `<YOUR_LMMS_EVAL_PATH>/models/__init__.py`:

```python
# Add the DyToK model entry to AVAILABLE_MODELS
AVAILABLE_MODELS = {
    # existing models ...
    "llava_onevision_dytok": "Llava_OneVision_DyToK"
}
```

## ❤️ Acknowledgements

Our work builds upon the codebase of [VisionZip](https://github.com/dvlab-research/VisionZip), [DyCoke](https://github.com/KD-TAO/DyCoke), [FastV](https://github.com/pkunlp-icler/FastV), [LLaVA-NeXT](https://github.com/LLaVA-VL/LLaVA-NeXT), [Qwen2.5-VL](https://github.com/QwenLM/Qwen3-VL), and [LMMs-Eval](https://github.com/EvolvingLMMs-Lab/lmms-eval). We sincerely thank the authors for their remarkable contributions.

## 📜 Citation

If you find DyToK useful in your research, please cite our paper:

```bibtex
@article{li2025less,
  title={Less Is More, but Where? Dynamic Token Compression via LLM-Guided Keyframe Prior},
  author={Li, Yulin and Gui, Haokun and Fan, Ziyang and Wang, Junjie and Kang, Bin and Chen, Bin and Tian, Zhuotao},
  journal={arXiv preprint arXiv:2512.06866},
  year={2025}
}
```
