# 🐶 BarkOrPurr — Dog vs. Cat Image Classifier

[![Python 3.9+](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0%2B-ee4c2c.svg)](https://pytorch.org/)
[![Flask](https://img.shields.io/badge/Flask-2.0%2B-000000.svg)](https://flask.palletsprojects.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

**BarkOrPurr** is a complete, production-grade Deep Learning workflow built with **PyTorch** and **ResNet-18** transfer learning. It classifies images into two categories (Cats 🐱 vs Dogs 🐶) and features an end-to-end pipeline: dataset preparation, fine-tuning, metric evaluation (Accuracy, Confusion Matrix, Classification Report), single-image CLI inference, and an interactive **Flask Web UI** + **REST API**.

---

## 🌟 Key Features

- **Transfer Learning Architecture**: Utilizes pre-trained `ResNet-18` with custom classification head for fast convergence and high accuracy.
- **Dataset Preparation Utilities**: Automated train/val dataset split helper (`scripts/split_dataset.py`).
- **Configurable Training Pipeline**: Dynamic batching, learning rate control, fine-tuning toggles, auto compute-device detection (CUDA/CPU), and loss curve rendering.
- **Comprehensive Evaluation**: Metric reporting with Precision, Recall, F1-Score, and automated heatmap rendering for Confusion Matrices.
- **Dual Inference Interfaces**:
  - **CLI**: Fast single-image classification.
  - **Web App / REST API**: Modern Flask UI with real-time preview and `/api/predict` JSON endpoint.

---

## 📁 Repository Structure

```text
Deep Learning Project/
├── Dataset/                   # Raw or downloaded datasets
│   ├── train_set/
│   └── test_set/
├── data/                      # Processed ImageFolder dataset layout
│   ├── train/                 # Training samples (cats/, dogs/)
│   └── val/                   # Validation samples (cats/, dogs/)
├── models/                    # Saved checkpoints & evaluation plots
│   ├── best.pth               # Model weights with highest val accuracy
│   ├── last.pth               # Final epoch checkpoint
│   ├── loss.png               # Training/Validation loss & accuracy curves
│   └── confusion_matrix.png   # Confusion Matrix plot
├── scripts/                   # Utility scripts
│   └── split_dataset.py       # Auto-split raw images into train/val folders
├── src/                       # Core machine learning source code
│   ├── data.py                # PyTorch DataLoaders & image transformations
│   ├── model.py               # ResNet-18 architecture initialization
│   ├── train.py               # Training loop with validation logging
│   ├── evaluate.py            # Model evaluation & confusion matrix generator
│   ├── predict.py             # Single-image CLI prediction helper
│   └── utils.py               # Metric visualization & helper utilities
├── web/                       # Web application & REST service
│   ├── app.py                 # Flask server backend
│   ├── static/                # CSS, JavaScript, and UI assets
│   └── templates/             # HTML templates (Jinja2)
├── requirements.txt           # Python dependencies
└── README.md                  # Project documentation
```

---

## 🚀 Quick Start Guide

### 1. Environment Setup

Clone the repository and navigate to the project directory:

```bash
git clone https://github.com/your-username/BarkOrPurr.git
cd BarkOrPurr
```

Create a virtual environment and install the required dependencies:

**On Windows (PowerShell):**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install --upgrade pip
pip install -r requirements.txt
```

**On macOS / Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

---

### 2. Dataset Preparation

Organize your dataset into standard PyTorch `ImageFolder` structure (`cats` and `dogs` subdirectories):

```text
data/
├── train/
│   ├── cats/
│   └── dogs/
└── val/
    ├── cats/
    └── dogs/
```

If you have raw images in a single folder, run the dataset split script:

```bash
python scripts/split_dataset.py --src "Dataset/raw_images" --dst "data" --val-frac 0.2 --seed 42
```

---

### 3. Model Training

Train the `ResNet-18` classification model:

```bash
python src/train.py --data-dir data --epochs 10 --batch-size 32 --lr 0.001
```

**Optional Training Arguments:**
- `--fine-tune`: Unfreeze all ResNet layers for end-to-end fine-tuning.
- `--device`: Specify computation device (`cuda` or `cpu`).
- `--output-dir`: Set checkpoint save directory (default: `models`).

Upon completion, weights and metric curves will be automatically saved to `models/`:
- `models/best.pth`
- `models/last.pth`
- `models/loss.png`

---

### 4. Model Evaluation

Compute overall validation accuracy, print detailed classification reports (Precision, Recall, F1-Score), and generate a confusion matrix heatmap:

```bash
python src/evaluate.py --data-dir data --checkpoint models/best.pth
```

*Output visualization will be saved to `models/confusion_matrix.png`.*

---

### 5. Single Image Prediction (CLI)

Perform quick command-line inference on any single image:

```bash
python src/predict.py --image path/to/sample_image.jpg --checkpoint models/best.pth
```

---

### 6. Web UI & REST API Service

Launch the Flask web application:

```bash
python web/app.py
```

Access the interactive web application in your browser at:
**`http://127.0.0.1:5000`**

#### REST API Usage

Submit an image via `POST` multipart form data to get JSON prediction results:

**Using cURL:**
```bash
curl -X POST -F "file=@data/val/cats/cat.4001.jpg" http://127.0.0.1:5000/api/predict
```

**Response Example:**
```json
{
  "success": true,
  "label": "cats",
  "confidence": 0.9984
}
```

**Using Python `requests`:**
```python
import requests

url = "http://127.0.0.1:5000/api/predict"
with open("sample.jpg", "rb") as img:
    response = requests.post(url, files={"file": img})
print(response.json())
```

---

## 🛠️ Built With

- **[PyTorch](https://pytorch.org/)** — Deep learning framework
- **[Torchvision](https://pytorch.org/vision/stable/index.html)** — Computer vision models & transforms
- **[Flask](https://flask.palletsprojects.com/)** — Web framework & REST API server
- **[Scikit-Learn](https://scikit-learn.org/)** — Evaluation metrics & classification reporting
- **[Matplotlib](https://matplotlib.org/)** — Loss curve & confusion matrix plotting
- **[Bootstrap 5](https://getbootstrap.com/)** — Web UI layout styling

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!  
Feel free to check the [issues page](https://github.com/your-username/BarkOrPurr/issues).

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git checkout -b feature/AmazingFeature`)
5. Open a Pull Request

---

## 📜 License

Distributed under the MIT License. See `LICENSE` for more information.
