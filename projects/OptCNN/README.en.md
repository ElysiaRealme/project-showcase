# OptCNN

[简体中文](README.md) | English

OptCNN is a HAM10000 skin-lesion classification experiment project. It includes a custom CNN, a Residual Shuffle CNN, and a ResNet18 transfer-learning baseline, plus scripts for training, evaluation, inference, ONNX export, and parameter counting.

## Features

- Loads HAM10000 metadata and image folders to build training and validation datasets.
- `train.py` supports model training, validation splits, and TensorBoard logging.
- `predict.py` reports accuracy, classification metrics, and a confusion matrix.
- `convert.py` exports ONNX and can validate the export with ONNX Runtime.
- `tools/` includes FID300 preparation and quick-check scripts.

## Quick Start

```powershell
uv sync
uv run python train.py
```

Prepare the HAM10000 dataset before training, then point script arguments or `TrainingConfig` to the real path.

## Installation

### uv

```powershell
uv sync
uv run python train.py
```

### Native venv

```powershell
python -m venv .venv
.\.venv\Scripts\activate
python -m pip install -r requirements.txt
python train.py
```

### Conda

```bash
conda env create -f environment.yml
conda activate optcnn
python train.py
```

## Usage

| Command | Purpose |
| --- | --- |
| `uv run python train.py` | Train the default CNN model |
| `uv run python predict.py --checkpoint artifacts/best_model.pt` | Evaluate a checkpoint on the dataset |
| `uv run python convert.py --checkpoint artifacts/best_model.pt --output artifacts/model.onnx` | Export ONNX |
| `uv run python count_parameters.py` | Count model parameters |
| `uv run python resnet18.py train --help` | Inspect ResNet18 transfer-learning options |

## Configuration and Data

- `data/`: HAM10000, FID300, and other datasets; ignored.
- `artifacts/`: model weights, ONNX exports, and checkpoints; ignored.
- `runs/`: TensorBoard logs; ignored.
- `docs/`: experiment notes and curation materials copied from the original workspace.

## Project Structure

| Path | Description |
| --- | --- |
| `models.py` | CNN and Residual Shuffle CNN definitions |
| `train.py` | Main training entrypoint |
| `validation.py` | Metrics and visualization utilities |
| `predict.py` | Inference evaluation |
| `convert.py` | ONNX export |
| `resnet18.py` | ResNet18 transfer-learning baseline |
| `tools/` | Dataset preparation and quick-check tools |

## Development and Verification

```powershell
uv lock --check -p 3.11
python -B -m py_compile models.py train.py validation.py predict.py convert.py count_parameters.py resnet18.py
```

## License

This project root includes a `LICENSE` file. Confirm that it matches the intended GitHub delivery boundary before publishing.
