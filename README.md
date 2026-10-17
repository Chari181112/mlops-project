# ScoreSense – Student Marks Prediction (MLOps System)

An end-to-end Machine Learning Operations (MLOps) project that demonstrates converting an exploratory Machine Learning Jupyter Notebook into a production-ready, modular, and containerized Flask web application, verified with unit testing, Dockerized via Gunicorn, and deployed to Amazon Web Services (AWS) EC2.

[![GitHub Repository](https://img.shields.io/badge/GitHub-Student--marks--predection-blue?logo=github)](https://github.com/gopir1085-cyber/Student-marks-predection)
[![Python Version](https://img.shields.io/badge/Python-3.11-brightgreen?logo=python)](https://www.python.org/)
[![Framework](https://img.shields.io/badge/Framework-Flask-black?logo=flask)](https://flask.palletsprojects.com/)
[![ML Library](https://img.shields.io/badge/ML-Scikit--Learn-orange?logo=scikitlearn)](https://scikit-learn.org/)
[![Container](https://img.shields.io/badge/Container-Docker-blue?logo=docker)](https://www.docker.com/)

---

## 📋 Table of Contents
1. [Project Overview](#-project-overview)
2. [Technologies Used](#-technologies-used)
3. [Project Folder Structure](#-project-folder-structure)
4. [Installation & Setup](#-installation--setup)
5. [Local Execution Instructions](#-local-execution-instructions)
6. [Docker Build & Run](#-docker-build--run)
7. [API Documentation & Testing](#-api-documentation--testing)
8. [AWS EC2 Deployment Overview](#-aws-ec2-deployment-overview)
9. [GitHub Repository Setup & Push Guide](#-github-repository-setup--push-guide)

---

## 📖 Project Overview

ScoreSense predicts final examination marks based on three key educational parameters:
1. **Hours Studied**: Daily average study duration (0–24 hrs).
2. **Attendance**: Classroom attendance percentage (0–100%).
3. **Previous Marks**: Baseline examination score (0–100%).

The system features:
* **Preprocessing & Training**: A reproducible pipeline using `StandardScaler` and `LinearRegression` trained on 500 student records ($R^2 \approx 0.948$, $\text{MAE} \approx 3.204$).
* **Serialized Model**: Exported as `model.pkl` for low-latency inference without retraining.
* **Web User Interface**: Clean, centered responsive card design with real-time feedback.
* **REST API**: Automated JSON endpoints for programmatic integration.
* **Automated Testing**: Comprehensive Pytest suite for API and input validation.

---

## 💻 Technologies Used

* **Programming Language**: Python 3.11
* **Machine Learning**: Scikit-Learn, NumPy, Pandas, Joblib
* **Web Framework**: Flask, Jinja2
* **WSGI Production Server**: Gunicorn
* **Testing**: Pytest
* **Containerization**: Docker
* **Frontend**: Semantic HTML5, Modern CSS3, JavaScript
* **Cloud Platform**: AWS EC2 (Ubuntu 22.04/24.04 LTS)

---

## 📂 Project Folder Structure

```text
scoresense-student-marks/
├── app.py                      # Flask web application and REST API endpoints
├── train_model.py              # Model training, evaluation, and serialization script
├── student_marks_model.ipynb   # Jupyter Notebook for exploratory data analysis
├── model.pkl                   # Trained model pipeline (StandardScaler + LinearRegression)
├── students.csv                # Generated synthetic student dataset (500 records)
├── requirements.txt            # Python dependencies
├── Dockerfile                  # Container build instructions with Gunicorn
├── .dockerignore               # Files excluded from the Docker build context
├── .gitignore                  # Files and secrets excluded from Git tracking
├── README.md                   # Complete documentation
├── templates/
│   └── index.html              # HTML user interface with prediction form
├── static/
│   ├── style.css               # Card-based responsive CSS styling
│   └── js/
│       └── app.js              # Client-side input validation and submit controller
└── tests/
    └── test_app.py             # Automated unit and integration tests (pytest)
```

---

## ⚙️ Installation & Setup

### 1. Clone the Repository
```bash
git clone https://github.com/gopir1085-cyber/Student-marks-predection.git
cd Student-marks-predection
```

### 2. Create and Activate a Virtual Environment

**Windows PowerShell:**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**macOS / Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

---

## 🚀 Local Execution Instructions

### Run the Flask Application
```bash
python app.py
```
Open your web browser and navigate to:
```text
http://127.0.0.1:5000
```

### Run Automated Tests
```bash
python -m pytest -v
```

---

## 🐳 Docker Build & Run

### 1. Build the Docker Image
```bash
docker build -t scoresense:1.0 .
```

### 2. Verify the Image
```bash
docker images
```

### 3. Run the Container
```bash
docker run -d --name scoresense-app -p 5000:5000 scoresense:1.0
```

### 4. Verify Container Status & Logs
```bash
docker ps
docker logs scoresense-app
```

### 5. Access Containerized App
* Web UI: `http://localhost:5000`
* Health Check: `curl http://localhost:5000/health`

### 6. Stop and Clean Up
```bash
docker stop scoresense-app
docker rm -f scoresense-app
```

---

## 🛠️ API Documentation & Testing

### 1. Health Endpoint
* **URL**: `/health`
* **Method**: `GET`
* **Response**:
```json
{
  "features": ["hours_studied", "attendance", "previous_marks"],
  "model": "LinearRegression",
  "status": "ok"
}
```

### 2. Prediction API Endpoint
* **URL**: `/api/predict`
* **Method**: `POST`
* **Content-Type**: `application/json`

**Example Request Payload:**
```json
{
  "hours_studied": 6.0,
  "attendance": 85.0,
  "previous_marks": 70.0
}
```

**Example Successful Response (HTTP 200 OK):**
```json
{
  "predicted_marks": 70.3
}
```

**Example Error Response (HTTP 400 Bad Request):**
```json
{
  "error": "hours_studied must be between 0 and 24"
}
```

**Testing via cURL:**
```bash
curl -X POST http://127.0.0.1:5000/api/predict \
     -H "Content-Type: application/json" \
     -d "{\"hours_studied\": 6, \"attendance\": 85, \"previous_marks\": 70}"
```

**Testing via PowerShell:**
```powershell
$body = @{
    hours_studied = 6
    attendance = 85
    previous_marks = 70
} | ConvertTo-Json

Invoke-RestMethod -Uri "http://127.0.0.1:5000/api/predict" -Method Post -ContentType "application/json" -Body $body
```

---

## ☁️ AWS EC2 Deployment Overview

1. **Launch EC2 Instance**: Ubuntu 22.04/24.04 LTS (`t2.micro` or `t3.micro`).
2. **Configure Security Group**:
   * Port `22` (SSH) — Remote administration.
   * Custom TCP Port `5000` (`0.0.0.0/0`) — Application access.
3. **Connect & Setup Docker**:
   ```bash
   ssh -i "scoresense-key.pem" ubuntu@YOUR_EC2_PUBLIC_IP
   sudo apt update && sudo apt install -y docker.io
   sudo systemctl enable --now docker
   sudo usermod -aG docker ubuntu
   ```
4. **Deploy Application**:
   * Transfer project files using `scp`.
   * Build the Docker image on the instance: `sudo docker build -t scoresense:1.0 .`
   * Run container: `sudo docker run -d --name scoresense-app --restart unless-stopped -p 5000:5000 scoresense:1.0`
5. **Verify Access**: Open `http://YOUR_EC2_PUBLIC_IP:5000` in your browser.

---

## 🐙 GitHub Repository Setup & Push Guide

To push this repository to GitHub under `gopir1085-cyber/Student-marks-predection`:

```powershell
# 1. Initialize Git in the project root
git init

# 2. Add all tracked project files
git add .

# 3. Commit the changes
git commit -m "Initial commit: ScoreSense Student Marks Prediction"

# 4. Set the default branch to main
git branch -M main

# 5. Link to your GitHub remote repository
git remote add origin https://github.com/gopir1085-cyber/Student-marks-predection.git

# 6. Push to GitHub
git push -u origin main
```

---

## 📄 License
This project is open-source and available for educational and academic use.
