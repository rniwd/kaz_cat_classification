# Kazakh News Classification — Knowledge Distillation & Quantization

Source code for the paper:

> **"Evaluating the Impact of Knowledge Distillation and Weight Quantization on Transformer-Based Model Optimization for the Kazakh Language"**  
> Assel Ospan, Madina Mansurova, Aisha Sailau, Nazerke Galymkyzy, Akniet Kalzhan  
> Al-Farabi Kazakh National University, Almaty, Kazakhstan

---

## Overview

This repository provides the full training pipeline for Kazakh-language news classification using:

- **XLM-RoBERTa Large** as a high-capacity teacher (560M params)
- **KazRoBERTa** as a compact student via soft-target knowledge distillation (83M params, **6.7× smaller**)
- **INT8 / NF4 quantization** for memory-efficient deployment

| Method | Macro F1 | Accuracy | Size (MB) |
|--------|----------|----------|-----------|
| Teacher (XLM-R Large, FP16) | 0.6700 | 0.6547 | 1120 |
| Student (KazRoBERTa, FP16) | 0.6701 | 0.6548 | 167 |
| Student INT8 | 0.6700 | 0.6540 | 124 |
| Student NF4 | 0.6510 | 0.6420 | 103 |

---

## Repository structure

```
├── kazakh_news_classification.ipynb   # Full pipeline notebook
├── README.md                          # This file
└── requirements.txt                   # Python dependencies
```

---

## Quick start

### 1. Clone & install

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
pip install -r requirements.txt
```

### 2. Run the notebook

Open `kazakh_news_classification.ipynb` in Jupyter or Google Colab.

```bash
jupyter notebook kazakh_news_classification.ipynb
```

The notebook is self-contained and walks through all stages:

| Section | Description |
|---------|-------------|
| **0** | Install dependencies |
| **1** | Imports & reproducibility |
| **2** | Dataset loading & preprocessing |
| **3** | Teacher training (XLM-RoBERTa Large) |
| **4** | Knowledge distillation → KazRoBERTa |
| **5** | Quantization benchmark (FP16 / INT8 / NF4) |
| **6** | Inference on custom text |
| **7** | Quick start with the published HF model |

---

## Hugging Face authentication (for dataset access)

The dataset is loaded from `AishaSailau/news_kk_v2` on Hugging Face.  
If the dataset is private, you need a Hugging Face token.

**Set the token as an environment variable — never hard-code it in the notebook:**

```bash
# Linux / macOS
export HF_TOKEN=hf_your_token_here

# Windows PowerShell
$env:HF_TOKEN = "hf_your_token_here"
```

Then in the notebook, uncomment and run:

```python
from huggingface_hub import login
import os
login(token=os.environ.get("HF_TOKEN"))
```

---

## Data & model availability

| Resource | URL |
|----------|-----|
| Dataset (CSV) | [GitHub](https://github.com/nazerkegalymkyzy97/data/blob/main/Desktop/dataset-kaz/df20.csv) |
| Fine-tuned model | [Hugging Face — Rniwd/Kaz_Roberta_fine_tuned](https://huggingface.co/Rniwd/Kaz_Roberta_fine_tuned) |

---

## Hardware requirements

| Stage | Minimum VRAM | Recommended |
|-------|-------------|-------------|
| Teacher training | 24 GB | A100 40 GB |
| Distillation | 16 GB | A100 40 GB |
| INT8 / NF4 inference | 8 GB | any modern GPU |

> Google Colab Pro+ (A100) works for the full pipeline.  
> For inference only (Section 7), a T4 (16 GB) is sufficient.

---

## Hyperparameters

| Parameter | Teacher | Student |
|-----------|---------|---------|
| Base model | `xlm-roberta-large` | `kz-transformers/kaz-roberta-conversational` |
| Max sequence length | 128 | 128 |
| Batch size | 32 | 64 |
| Gradient accumulation | 4 | 2 |
| Learning rate | 5e-5 | 3e-5 |
| Max steps | 3000 | 2000 |
| Warmup steps | 300 | 200 |
| Distillation temperature | — | 4.0 |
| Distillation alpha (KD weight) | — | 0.7 |
| Optimizer | AdamW | AdamW |
| Weight decay | 0.01 | 0.01 |

---

## Citation

If you use this code or dataset, please cite:

```bibtex
@inproceedings{ospan2025kazakh,
  title     = {Evaluating the Impact of Knowledge Distillation and Weight Quantization
               on Transformer-Based Model Optimization for the Kazakh Language},
  author    = {Ospan, Assel and Mansurova, Madina and Sailau, Aisha and
               Galymkyzy, Nazerke and Kalzhan, Akniet},
  year      = {2025},
  address   = {Almaty, Kazakhstan},
  note      = {Al-Farabi Kazakh National University}
}
```

---

## Acknowledgements

Funded by the Science Committee of the Ministry of Science and Higher Education of the Republic of Kazakhstan, grant **BR24993001** — "Creation of a large language model (LLM) to maintain the implementation of Kazakh language and increase the technological progress".
