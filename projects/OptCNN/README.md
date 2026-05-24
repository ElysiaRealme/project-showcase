# OptCNN

简体中文 | [English](README.en.md)

OptCNN 是 HAM10000 皮肤病变分类实验项目，包含自定义 CNN、Residual Shuffle CNN 和 ResNet18 迁移学习基线，并提供训练、评估、推理、ONNX 导出与参数量统计脚本。

## 功能

- 读取 HAM10000 metadata 与图片目录，构建训练/验证数据集。
- `train.py` 支持模型训练、验证划分和 TensorBoard 日志。
- `predict.py` 输出准确率、分类报告和混淆矩阵。
- `convert.py` 导出 ONNX，并可用 ONNX Runtime 做一次校验。
- `tools/` 提供 FID300 数据准备与快速检查脚本。

## 快速开始

```powershell
uv sync
uv run python train.py
```

训练前请准备 HAM10000 数据，并按脚本参数或 `TrainingConfig` 指向实际路径。

## 安装

### uv

```powershell
uv sync
uv run python train.py
```

### 原生 venv

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

## 使用

| 命令 | 用途 |
| --- | --- |
| `uv run python train.py` | 训练默认 CNN 模型 |
| `uv run python predict.py --checkpoint artifacts/best_model.pt` | 使用权重做数据集评估 |
| `uv run python convert.py --checkpoint artifacts/best_model.pt --output artifacts/model.onnx` | 导出 ONNX |
| `uv run python count_parameters.py` | 统计模型参数量 |
| `uv run python resnet18.py train --help` | 查看 ResNet18 迁移学习参数 |

## 配置与数据

- `data/`：HAM10000、FID300 等数据集，已忽略。
- `artifacts/`：模型权重、ONNX 和检查点，已忽略。
- `runs/`：TensorBoard 日志，已忽略。
- `docs/`：实验笔记和整理说明。

## 项目结构

| 路径 | 说明 |
| --- | --- |
| `models.py` | CNN 与 Residual Shuffle CNN 定义 |
| `train.py` | 主训练入口 |
| `validation.py` | 指标记录与可视化 |
| `predict.py` | 推理评估 |
| `convert.py` | ONNX 导出 |
| `resnet18.py` | ResNet18 迁移学习基线 |
| `tools/` | 数据准备和快速检查工具 |

## 开发与验证

```powershell
uv lock --check -p 3.11
python -B -m py_compile models.py train.py validation.py predict.py convert.py count_parameters.py resnet18.py
```

## 许可

本项目根目录包含 `LICENSE`。提交到 GitHub 前请确认该许可与项目交付边界一致。
