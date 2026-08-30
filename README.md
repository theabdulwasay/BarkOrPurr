# BarkOrPurr — Pet Image Classifier

BarkOrPurr is a polished, beginner-friendly PyTorch project that classifies images as dogs or cats. It uses transfer learning with `resnet18`, provides a reproducible training pipeline, evaluation utilities (confusion matrix / classification report), and a small Flask web UI + JSON API for on-demand predictions.

Key goals:
- Simple, well-documented starter suitable for learning and demoing transfer learning.
- End-to-end flow: prepare dataset → train → evaluate → serve predictions.
- Lightweight UI for quick manual testing and prototyping.

--

## Features

- Transfer learning via `torchvision.models.resnet18` (replace final `fc` layer).
- ImageFolder-based data pipeline with configurable transforms (`src/data.py`).
- Training script with progress logging, checkpointing (`models/best.pth`, `models/last.pth`) and plotting (`models/loss.png`).
- Evaluation helpers to compute confusion matrix and classification report (sklearn).
- Flask web UI (`web/app.py`) with a beautiful frontend and `/api/predict` for integration.

--

## Quick Start (Windows)

1. Open a PowerShell terminal and set your working directory to the project root:

```powershell
cd "C:\Users\MuhammadIbrarDyChief\Downloads\Deep Learning Project"
```

2. Create and activate a virtual environment, then install dependencies:

```powershell
python -m venv venv
venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
```

3. Prepare your dataset in ImageFolder layout (example):

```
data/
	train/
		cats/
		dogs/
	val/
		cats/
		dogs/
```

You can use the helper script to split a raw folder of images:

```powershell
python scripts/split_dataset.py --src "data/original" --dst data --val-frac 0.2 --seed 42
```

4. Run a quick smoke-training (1 epoch) to verify everything works:

```powershell
python src/train.py --data-dir data --epochs 1 --batch-size 32
```

--

## Training (recommended)

Train longer for better accuracy. Example:

```powershell
python src/train.py --data-dir data --epochs 10 --batch-size 32 --lr 1e-3
```

Outputs saved to `models/`:
- `best.pth` — checkpoint with best validation accuracy
- `last.pth` — final checkpoint
- `loss.png` — training/validation loss plot

Notes:
- The code auto-detects CUDA if available. On CPU, ignore any `pin_memory` warnings.

--

## Evaluation & Confusion Matrix

Use the model to evaluate the validation set and produce a confusion matrix and classification report. The project includes `src/predict.py` to load the model and predict single images — you can adapt it to loop over `torchvision.datasets.ImageFolder` and compute metrics with `sklearn.metrics`.

Suggested script (concept):

```python
from torchvision import datasets
from src.predict import load_model, predict_image_bytes
from sklearn.metrics import confusion_matrix, classification_report

model, CLASSES = load_model('models/best.pth')
val_ds = datasets.ImageFolder('data/val', transform=...)
# loop, collect y_true/y_pred, then compute and save confusion matrix image
```

If you want, I can add a ready-to-run `src/evaluate.py` that produces `models/confusion_matrix.png` and prints a classification report.

--

## Web UI & JSON API

Start the Flask app from the project root:

```powershell
python web/app.py
```

Open the UI: http://127.0.0.1:5000/

API endpoint:
- `POST /api/predict` — multipart form field `file`; returns JSON `{ "label": "cats|dogs", "prob": 0.997 }`.

Example `curl` call:

```powershell
curl -F "file=@data/val/cats/cat.1.jpg" http://127.0.0.1:5000/api/predict
```

Troubleshooting:
- If you see `ModuleNotFoundError: No module named 'src'`, ensure you run the app from the project root (`python web/app.py`). `web/app.py` already adds the root to `sys.path` but the working directory matters for asset loading.
- If `ERR_CONNECTION_REFUSED`, make sure the Flask server is running and port 5000 is free.

--

## Frontend

The UI uses Bootstrap and custom CSS/JS located in `web/static/` and a Jinja2 template at `web/templates/index.html`. Features include image preview, a scenic cat/dog background toggle, and an API button that returns JSON predictions without page reload.

--

## Deployment & Docker (optional)

I can add a `Dockerfile` if you want to containerize the app. Minimal approach: create a small image that installs Python, copies the repo, installs `requirements.txt`, and runs `web/app.py` via `gunicorn` or Flask for development.

--

## Contributing

Contributions welcome — good first tasks:
- Add `src/evaluate.py` to compute and save confusion matrix + classification report.
- Add model export (TorchScript/ONNX) for faster inference.
- Improve frontend styling or add feedback UI for batch uploads.

When contributing, open a PR and include a short description and screenshots (if UI changes).

--

## License

This project is provided for learning/demo purposes. Use freely for non-commercial experimentation. If you want a specific license, tell me and I'll add one (MIT recommended for open-source demos).

--

If you want, I will:
- add `src/evaluate.py` now and run it, or
- create a `Dockerfile` and `docker-compose.yml` for quick deployment.
Which should I do next?
