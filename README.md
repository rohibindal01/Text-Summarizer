# 📝 Text Summarizer — End-to-End NLP Pipeline

An end-to-end text summarization application built with a modular ML pipeline, exposing a REST API for training and inference.

---

## 📖 About the Project

This project implements an **automated text summarization system** using a transformer-based model. It is designed with a clean, modular pipeline architecture that handles everything from raw data ingestion to model evaluation and real-time prediction. The application is served via a **FastAPI** web server and is structured to be production-ready with proper logging, configuration management, and staged training pipelines.

The core goal is to accept arbitrary input text and return a concise, meaningful summary using a fine-tuned sequence-to-sequence model.

---

## 🚀 Basic Flow of the Project

```
Raw Data
   │
   ▼
Stage 1 — Data Ingestion
   │   Downloads/loads the dataset from the source
   │
   ▼
Stage 2 — Data Transformation
   │   Tokenizes and preprocesses the data for training
   │
   ▼
Stage 3 — Model Training
   │   Fine-tunes the summarization model on the processed data
   │
   ▼
Stage 4 — Model Evaluation
   │   Evaluates model performance (e.g., ROUGE scores)
   │
   ▼
Saved Model Artifacts
   │
   ▼
FastAPI Server  ──► POST /predict  ──► PredictionPipeline ──► Summary Output
                ──► GET  /train   ──► Triggers main.py (full pipeline)
```

**Two entry points:**
- **`main.py`** — Runs all four training stages sequentially end-to-end.
- **`app.py`** — Launches the FastAPI server for training triggers and live inference.

---

### Pipeline Stages

| Stage | File | Responsibility |
|-------|------|----------------|
| 1 | `stage_1_data_ingestion_pipeline.py` | Downloads and stores the raw dataset |
| 2 | `stage_2_data_transformation_pipeline.py` | Tokenizes and prepares data for the model |
| 3 | `stage_3_model_trainer_pipeline.py` | Fine-tunes the transformer model |
| 4 | `stage_4_model_evaluation.py` | Evaluates the model using metrics like ROUGE |
| ∞ | `predicition_pipeline.py` | Runs inference on new user-provided text |

---

## 🛠️ Technologies Used

### Core ML / NLP
| Technology | Purpose |
|------------|---------|
| 🤗 Hugging Face Transformers | Pre-trained summarization model (e.g., PEGASUS / BART) |
| 🤗 Hugging Face Datasets | Dataset loading and management |
| PyTorch | Deep learning backend |
| ROUGE Score | Model evaluation metric |

### API & Serving
| Technology | Purpose |
|------------|---------|
| FastAPI | REST API framework for training and prediction endpoints |
| Uvicorn | ASGI server to run the FastAPI application |
| Starlette | Underlying framework (responses, redirects) |

### Infrastructure & Utilities
| Technology | Purpose |
|------------|---------|
| Python 3.x | Core programming language |
| Jinja2 | Templating (integrated via FastAPI) |
| PyYAML | Parsing `config.yaml` and `params.yaml` |
| Logging (custom) | Stage-wise logging across all pipeline steps |
| OS / Sys | System-level pipeline execution (`os.system`) |

---

## 🔌 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/` | Redirects to `/docs` (Swagger UI) |
| `GET` | `/train` | Triggers the full training pipeline (`main.py`) |
| `POST` | `/predict` | Accepts input text and returns a summarized output |

### Example — Prediction Request

```bash
curl -X POST "http://localhost:8080/predict" \
     -H "Content-Type: application/json" \
     -d '{"text": "Your long article or paragraph goes here..."}'
```

---

## ⚙️ How to Run

### 1. Install Dependencies
```bash
pip install -r requirements.txt
```

### 2. Run the Full Training Pipeline
```bash
python main.py
```

### 3. Start the API Server
```bash
python app.py
```
Server will be available at: `http://localhost:8080`

### 4. Access Swagger Docs
Navigate to `http://localhost:8080/docs` for the interactive API documentation.

---

## 📊 Logging

Each pipeline stage produces structured logs via the custom logger at `src/textSummarizer/logging`. Logs clearly mark the **start** and **completion** of every stage, and exceptions are captured and re-raised with full tracebacks for debugging.

Example log output:
```
[INFO] stage Data Ingestion stage initiated
[INFO] Stage Data Ingestion stage Completed
[INFO] stage Data Transformation stage initiated
...
[INFO] >>>>>> stage Model Evaluation stage completed <<<<<<
```

---

## 📌 Notes

- The `text` variable declared at the top of `app.py` (`"What is Text Summarization?"`) serves as a default placeholder and can be replaced by dynamic user input via the `/predict` endpoint.
- The training endpoint uses `os.system("python main.py")`, which runs all four pipeline stages synchronously. For production use, consider offloading this to a background task worker (e.g., Celery).
