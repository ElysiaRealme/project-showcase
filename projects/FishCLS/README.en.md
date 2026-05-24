# FishCLS

[中文](README.md)

Private task: Fish4Knowledge image classification with PyTorch and Gradio.

FishCLS is a PyTorch transfer-learning project for Fish4Knowledge-style image classification. `main.py` now merges both experiment modes into one WebUI: the default tabs provide DenseNet169, ResNet18, and ShuffleNetV2 training/evaluation/inference, while the comparison tab provides the ResNet18 vs ShuffleNetV2 experiment workflow.

## Features

- Splits `dataset/` into train/validation/test subsets.
- Supports DenseNet169, ResNet18, and ShuffleNetV2 backbones.
- Saves best checkpoints to `artifacts/best_<model>.pth` and writes TensorBoard logs.
- Provides Gradio tabs for training, evaluation, and inference.
- Includes the former ResNet18 vs ShuffleNetV2 comparison workflow in the same WebUI, producing training curves, a metrics table, confusion matrices, Top-k accuracy, and an inference snapshot.
- Includes `generate_prediction_figures.py` and `replot_from_checkpoints.py` for report-ready figures.

## Layout

```text
.
├─ main.py                         # single Gradio WebUI for training/evaluation/inference and comparison
├─ generate_prediction_figures.py  # prediction figure generation
├─ replot_from_checkpoints.py      # rebuild plots from checkpoints
├─ exp_table.py                    # DenseNet169 experiment helper
├─ requirements.txt
├─ environment.yml
├─ pyproject.toml
└─ LICENSE
```

`dataset/`, `artifacts/`, model weights, TensorBoard logs, and HAR debug captures are local data/artifacts and are excluded from Git.

## Data Format

The dataset must follow `torchvision.datasets.ImageFolder`:

```text
dataset/
├─ class_a/
│  ├─ image_001.jpg
│  └─ image_002.jpg
└─ class_b/
   └─ image_001.jpg
```

## Quick Start

```powershell
uv sync
uv run python main.py
```

Open the Gradio URL printed in the terminal. The WebUI contains `Train`, `Evaluate`, `Inference`, and `ResNet18 vs ShuffleNetV2 Comparison` tabs.

## Dependency Options

```powershell
# venv / pip
python -m venv .venv
.\.venv\Scripts\activate
pip install -r requirements.txt

# conda
conda env create -f environment.yml
conda activate fishcls

# uv
uv sync
```

For CUDA-enabled PyTorch, adjust the PyTorch packages according to the official PyTorch installation selector.

## License

MIT License. See [LICENSE](LICENSE).
