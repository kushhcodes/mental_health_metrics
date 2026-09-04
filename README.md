# Mental Health Metrics

**A full-stack ML web app that models student wellness from daily habits — screen time, sleep, study load, and stress — and returns a predicted mental health score in real time.**

**Live app:** [mental-health-metrics.onrender.com](https://mental-health-metrics.onrender.com)
**Repository:** [github.com/kushhcodes/mental_health_metrics](https://github.com/kushhcodes/mental_health_metrics)

> Note: This tool is for informational and educational purposes only. It is **not** a clinical assessment or diagnostic tool.

---

## Overview

Mental Health Signal takes a snapshot of a student's daily routine — screen time, phone unlocks, sleep, study hours, physical activity, and self-reported stress — and runs it through a trained regression model to produce a **0–10 wellness score**, along with a qualitative read on where that score falls.

The project is built as two decoupled pieces: a static, dependency-free frontend that talks to a Python ML backend over a REST API. It's designed to demonstrate an end-to-end ML product: data, model, API, UI, and deployment.

---

## Features

- **Interactive intake form** — profile, academic habits, digital usage, and lifestyle inputs, grouped into logical sections
- **Real-time prediction** — form data is validated client-side, sent to a FastAPI backend, and scored by a trained scikit-learn model
- **Animated score gauge** — an SVG gauge with a color gradient (red to amber to green) that fills based on the predicted score
- **Robust error handling** — distinct UI states for idle, loading, validation errors (with field-level messages mapped from the API's 422 responses), and network/server failures
- **Responsive design** — adapts from a two-column desktop layout down to a single-column mobile view
- **Accessible by default** — semantic form structure, `aria-live` result region, and reduced-motion support

---

## Architecture

```
┌─────────────────┐         POST /predict          ┌──────────────────────┐
│   Frontend       │  ───────────────────────────▶  │   FastAPI Backend     │
│  (HTML/CSS/JS)   │                                 │                        │
│                  │  ◀───────────────────────────  │  Pydantic schema      │
│  Form → Fetch    │      JSON: predicted score      │  → scikit-learn model │
└─────────────────┘                                 └──────────────────────┘
                                                                │
                                                                ▼
                                                       Trained on Kaggle
                                                       student wellness data
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | HTML5, CSS3 (custom properties, CSS Grid), Vanilla JavaScript (no framework) |
| **Backend** | Python, FastAPI, Pydantic (request/response schema validation), Uvicorn (ASGI server) |
| **Machine Learning** | scikit-learn regression model, trained offline and loaded at runtime |
| **Data** | Kaggle dataset — student social media usage & lifestyle habits |
| **Deployment** | Render (backend + static hosting) |

---

## API Reference

### `POST /predict`

Accepts a student's profile and habit data, returns a predicted mental health score.

**Request body**

```json
{
  "age": 21,
  "gender": "Female",
  "country": "India",
  "academic_level": "Undergraduate",
  "most_used_platform": "Instagram",
  "purpose_of_use": "Entertainment",
  "avg_daily_usage_hours": 5.5,
  "daily_unlocks": 80,
  "study_hours": 3.0,
  "physical_activity_hours": 1.0,
  "sleep_hours_per_night": 6.5,
  "stress_level": "High"
}
```

**Response — `200 OK`**

```json
{
  "predicted_mental_health_score": 5.42
}
```

**Response — `422 Unprocessable Entity`**

Returned when a field fails validation (e.g. out-of-range value or missing required field). The frontend parses `detail[].loc` to highlight the exact field.

```json
{
  "detail": [
    {
      "loc": ["body", "age"],
      "msg": "ensure this value is greater than or equal to 10",
      "type": "value_error.number.not_ge"
    }
  ]
}
```

---

## Project Structure

```
mental_health_metrics/
├── backend/
│   ├── main.py            # FastAPI app, /predict route
│   ├── schema.py          # Pydantic request/response models
│   ├── model.py           # Model loading + inference logic
│   ├── model.pkl           # Trained scikit-learn model artifact
│   └── requirements.txt
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js
└── README.md
```
> Adjust this tree if your actual folder/file names differ — this reflects the FastAPI-with-separate-schema-and-model-files structure.

---

## Getting Started

### Prerequisites
- Python 3.10+
- pip

### 1. Clone the repo

```bash
git clone https://github.com/kushhcodes/mental_health_metrics.git
cd mental_health_metrics
```

### 2. Backend setup

```bash
cd backend
python -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload --port 2200
```

The API will be live at `http://127.0.0.1:2200`.

### 3. Frontend setup

The frontend is static — no build step required. Serve it with any static server, or open `index.html` directly, and make sure `API_BASE` in `script.js` points at your running backend:

```js
const API_BASE = "http://127.0.0.1:2200";
```

> If you serve the frontend from a different origin/port than the backend, make sure `CORSMiddleware` is configured in `main.py` to allow that origin.

---

## Model

The prediction model is a **scikit-learn regression model** trained offline on a Kaggle dataset of student social media usage and lifestyle habits, then serialized and loaded by the FastAPI backend at startup for inference.

> Fill in: exact algorithm (e.g. `RandomForestRegressor`, `Ridge`), feature engineering steps, and evaluation metrics (R², MAE, etc.) once finalized — these are strong talking points for interviews.

---

## Deployment

Both the API and the static frontend are deployed on **Render**.

**Live URL:** [https://mental-health-metrics.onrender.com](https://mental-health-metrics.onrender.com)

> Note: Render's free tier spins down after inactivity, so the first request after idle time may take 30–60 seconds to respond.

---

## Disclaimer

This project is built for educational and portfolio purposes. It does not provide medical or psychological advice, and its output should not be used as a substitute for professional mental health support.

---

## Author

**Kush** — [@kushhcodes](https://github.com/kushhcodes)

---

## License

Distributed under the MIT License. See `LICENSE` for details.
