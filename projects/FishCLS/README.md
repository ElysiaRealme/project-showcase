# FishCLS

[English](README.en.md)

Private task: Fish4Knowledge image classification with PyTorch and Gradio.

FishCLS 是一个基于 PyTorch 迁移学习的鱼类图像分类项目，面向 Fish4Knowledge 风格的 `ImageFolder` 数据集。`main.py` 已融合两个实验模式：默认页提供 DenseNet169、ResNet18、ShuffleNetV2 的训练/评估/推理；同一个 WebUI 中的对比实验页提供 ResNet18 vs ShuffleNetV2 对比流程。

## 功能

- 自动按 train/val/test 比例划分 `dataset/` 数据。
- 支持 DenseNet169、ResNet18、ShuffleNetV2 三种骨干网络。
- 训练时保存最佳权重到 `artifacts/best_<model>.pth`，并写入 TensorBoard 日志。
- Gradio 页面提供训练、评估和推理三个工作区。
- 同一 WebUI 内置 ResNet18 vs ShuffleNetV2 对比实验，生成训练曲线、指标表、混淆矩阵、Top-k 准确率和推理快照。
- 附带 `generate_prediction_figures.py` 和 `replot_from_checkpoints.py` 用于生成报告图和重绘结果。

## 目录

```text
.
├─ main.py                         # 单一 Gradio WebUI 入口，含训练/评估/推理与对比实验
├─ generate_prediction_figures.py  # 预测结果图生成
├─ replot_from_checkpoints.py      # 从 checkpoint 重绘报告图
├─ exp_table.py                    # DenseNet169 实验入口
├─ requirements.txt
├─ environment.yml
├─ pyproject.toml
└─ LICENSE
```

`dataset/`、`artifacts/`、权重文件、TensorBoard 日志和 HAR 调试文件属于本地数据/产物，已从 Git 范围排除。

## 数据准备

数据集需使用 `torchvision.datasets.ImageFolder` 结构：

```text
dataset/
├─ class_a/
│  ├─ image_001.jpg
│  └─ image_002.jpg
└─ class_b/
   └─ image_001.jpg
```

## 快速开始

```powershell
uv sync
uv run python main.py
```

启动后按终端输出访问 Gradio 地址。页面内包含 `Train`、`Evaluate`、`Inference` 和 `ResNet18 vs ShuffleNetV2 Comparison` 四个标签页。

## 依赖方式

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

如需 CUDA 版本 PyTorch，请按本机 CUDA 版本参考 PyTorch 官方安装命令调整依赖。

## License

MIT License. See [LICENSE](LICENSE).
