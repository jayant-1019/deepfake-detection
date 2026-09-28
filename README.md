# 🎬 Deepfake Detection

A comprehensive Python project for detecting deepfake videos using machine learning. This repository includes model training pipelines, a FastAPI backend, and both mobile and desktop prediction applications.

## 📋 Table of Contents
- [Overview](#overview)
- [Project Structure](#project-structure)
- [Tech Stack](#tech-stack)
- [Quick Start](#quick-start)
- [Core Modules](#core-modules)
- [Usage](#usage)
- [Deployment](#deployment)

---

## 🎯 Overview

This project provides an end-to-end workflow for:
- **Dataset Exploration & Preparation**: Analyze and preprocess video data
- **Feature Extraction**: Extract MediaPipe facial landmarks from frames
- **Model Training**: Train an ensemble deepfake detection model
- **Inference Deployment**: Deploy predictions via API and web/mobile interfaces

The system uses an ensemble machine learning approach with a conservative decision policy that marks ambiguous predictions for manual review.

---

## 📁 Project Structure

```
deepfake-detection/
├── README.md                          # Main project documentation
├── config.py                          # Central configuration management
├── requirements.txt                   # Python dependencies (core)
│
├── 🔵 TRAINING PIPELINE
│  ├── 01_explore_dataset.py          # Dataset statistics & visualization
│  ├── 02_prepare_dataset.py          # Data preprocessing & augmentation
│  ├── 03_extract_frames.py           # Video → facial landmarks extraction
│  ├── final_training.py              # Model training entry point
│  └── data/                          # (ignored) Training data & processed outputs
│
├── 🌐 API & INFERENCE
│  ├── app_api/
│  │  ├── main.py                     # FastAPI server (port 8000)
│  │  ├── requirements.txt            # API dependencies
│  │  └── rebuild_deployment_model.py # Model refresh utility
│  │
│  └── deployment_models/              # Production inference artifacts
│     ├── enhanced_ensemble_model.pkl  # Trained model
│     ├── scaler.pkl                   # Feature scaling
│     └── README.md                    # Deployment docs
│
├── 📱 MOBILE APP
│  ├── mobile_app/
│  │  ├── package.json                # Node.js dependencies
│  │  ├── README.md                   # Mobile app setup
│  │  └── src/                        # Expo React Native components
│  │
│  └── (Shares API with desktop app)
│
├── 🐳 DEPLOYMENT
│  └── Dockerfile                      # Railway container configuration
│
└── 📊 OUTPUTS (ignored by Git)
   ├── data/processed/                # Model artifacts & processed data
   ├── logs/                          # Training logs
   ├── deepfake_env/                  # Virtual environment
   └── model outputs/                 # Inference results
```

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|-----------|
| **Backend API** | FastAPI, Uvicorn |
| **ML/Data** | Python, scikit-learn, OpenCV, MediaPipe |
| **Mobile** | Expo, React Native |
| **Desktop UI** | HTML/CSS/JavaScript (served by FastAPI) |
| **Deployment** | Docker, Railway |
| **DevOps** | Git (with .gitignore for large files) |

**Language Composition:**
- Python: 77.7% (core logic & ML)
- HTML: 17.4% (desktop UI)
- JavaScript: 4.4% (mobile app)
- Dockerfile: 0.5% (container config)

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8+ (for training & API)
- Node.js LTS (for mobile app)
- Git

### Setup

#### 1️⃣ Core Setup (Training & API)
```powershell
# Clone and navigate
git clone https://github.com/jayant-1019/deepfake-detection.git
cd deepfake-detection

# Create virtual environment
python -m venv deepfake_env
.\deepfake_env\Scripts\Activate.ps1

# Install dependencies
python -m pip install -r requirements.txt
python -m pip install -r app_api/requirements.txt
```

#### 2️⃣ Run the API Server
```powershell
python -m uvicorn app_api.main:app --host 0.0.0.0 --port 8000
```
- **Desktop UI**: Open `http://127.0.0.1:8000` in your browser
- **API Docs**: Open `http://127.0.0.1:8000/docs` for interactive API documentation

#### 3️⃣ Run the Mobile App (Optional)
```powershell
cd mobile_app
npm install
npm start
```
Then:
1. Open **Expo Go** on your phone (same Wi-Fi network)
2. Enter your laptop's IPv4 address + port: `http://192.168.1.5:8000`

---

## 📚 Core Modules

### Training Pipeline

| Script | Purpose |
|--------|---------|
| `01_explore_dataset.py` | Load dataset, compute statistics, generate visualizations |
| `02_prepare_dataset.py` | Clean, balance, and augment video data for training |
| `03_extract_frames.py` | Extract frames from videos, run MediaPipe facial landmark detection |
| `final_training.py` | Train ensemble model on extracted features |
| `config.py` | Centralized configuration (paths, hyperparameters, model settings) |

### API & Prediction

| Component | Purpose |
|-----------|---------|
| `app_api/main.py` | FastAPI server with `/predict` endpoint and web UI |
| `app_api/rebuild_deployment_model.py` | Retrain production model on current data splits |
| `deployment_models/` | Minimal artifacts for container deployment (pickle files only) |

---

## 💡 Usage

### Training a Model

```powershell
# 1. Explore your dataset
python 01_explore_dataset.py

# 2. Prepare and preprocess data
python 02_prepare_dataset.py

# 3. Extract facial landmarks
python 03_extract_frames.py

# 4. Train the ensemble model
python final_training.py
```

Output: `data/processed/enhanced_ensemble_model.pkl` and `scaler.pkl`

### Making Predictions

#### Via API (JSON)
```bash
curl -X POST "http://127.0.0.1:8000/predict" \
  -F "video=@your_video.mp4"
```

#### Via Desktop Web UI
Open `http://127.0.0.1:8000` in your browser and upload a video.

#### Via Mobile App
1. Start API: `python -m uvicorn app_api.main:app --host 0.0.0.0 --port 8000`
2. Run mobile app and configure API address
3. Upload video and get real-time prediction

#### Via Python Script
```python
from app_api.main import predict_video

result = predict_video("path/to/video.mp4")
print(result)  # {"prediction": "REAL" or "FAKE", "confidence": 0.95}
```

### Production Decision Policy

The model uses a **conservative approach**:
- **High confidence** (>0.8): Returns "REAL" or "FAKE"
- **Low confidence** (0.3–0.8): Returns **"Needs manual review"** (ambiguous cases)

---

## 🐳 Deployment

### Local Deployment (Railway/Docker)

The repository includes a `Dockerfile` for containerized deployment.

#### Steps:

1. **Push to GitHub**
   ```bash
   git push origin main
   ```

2. **Create Railway Project**
   - Log in to [Railway.app](https://railway.app)
   - Create new project → Connect GitHub repo

3. **Configure Service**
   - Let Railway auto-detect the Dockerfile
   - In settings, generate a public domain for port `8080`
   - Ensure `deployment_models/` contains the model artifacts

4. **Access Your Deployment**
   ```
   https://your-railway-domain.railway.app
   ```

5. **Mobile App Configuration**
   - Replace local API address with Railway HTTPS URL

#### Container Contents
- `enhanced_ensemble_model.pkl` + `scaler.pkl` (inference only)
- FastAPI application
- Web UI for predictions
- No training data (kept locally to reduce image size)

---

## 📝 Notes

### Large Files
The repository intentionally ignores large directories via `.gitignore`:
- `data/` — Dataset and processed training outputs
- `logs/` — Training and inference logs
- `deepfake_env/` — Virtual environment
- Model outputs and temporary files

### Updating the Deployment Model

After retraining locally:

```powershell
python -m app_api.rebuild_deployment_model

# Copy artifacts to deployment directory
Copy-Item "data/processed/enhanced_ensemble_model.pkl" "deployment_models/"
Copy-Item "data/processed/scaler.pkl" "deployment_models/"

# Commit and push
git add deployment_models/
git commit -m "Update production model"
git push origin main
```

---

## 📧 Support & Contribution

- **Issues**: Use GitHub Issues for bug reports and feature requests
- **Discussions**: Start a discussion for questions and ideas
- **Wiki**: See the repository wiki for additional documentation

---

**Created**: May 2026 | **Updated**: June 2026
