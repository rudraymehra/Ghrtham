# System Design Document
## AI-Powered Physiotherapy Web Application

---

## 1. Design Overview

This document describes the system design and architecture of an AI-powered physiotherapy web application that enables patients to perform physiotherapy exercises remotely using a webcam. The system performs real-time pose estimation in the browser and post-session analysis using temporal deep learning techniques, then generates performance summaries for physiotherapists.

The design focuses on a browser-first ML approach, modularity, scalability, privacy, and ease of integration with clinical workflows. The initial domain focus is post-surgical joint replacement (TKA/THA) and general orthopedic rehabilitation, where standardized protocols and clear range-of-motion goals make pose-based assessment highly suitable.



---

## 7. Security Design

- Role-based access control (RBAC) for patients, physiotherapists, and administrators.
- JWT-based authentication integrated with secure user management.
- Encrypted data transmission using HTTPS (TLS 1.2+).
- Encryption at rest for databases and object storage.
- Consent-based storage of sensitive data, with explicit control over video retention.
- Limited and configurable retention of raw video data; preference for pose/feature storage.
- Audit logging for all access to and modification of patient-related data.

---

## 8. Scalability & Deployment Design

- Modular microservice-oriented architecture.
- Containerized deployment using Docker.
- Independent scaling of AI/ML services.
- Browser-first inference for pose estimation to offload real-time compute from the backend.
- Cloud-based object storage for large data (e.g., occasional raw videos, model artifacts).

---

## 9. Error Handling & Reliability

- Graceful handling of webcam or pose detection failures.
- Retry mechanisms for AI service calls.
- Centralized logging and monitoring.

---

## 10. Design Constraints

- Web-based execution limits real-time processing.
- Initial system supports a limited set of exercises.
- AI predictions assist but do not replace medical expertise.

---

## 11. Future Enhancements

- Real-time corrective feedback.
- Transformer-based temporal models.
- Wearable sensor integration.
- Mobile application support.

---

## 12. Conclusion

This design outlines a scalable, secure, and modular architecture for an AI-powered physiotherapy system that leverages pose estimation and deep learning to enhance remote rehabilitation while maintaining clinical oversight.

---

## 2. High-Level Architecture

+-----------------------------+
| Web Browser (Next.js App)  |
| - Webcam + UI              |
| - TF.js MoveNet + features |
| - Rep/ROM logic            |
+--------------+--------------+
               |
               | HTTPS / REST / WebSockets
               v
+-----------------------------+
| Backend Server (FastAPI)   |
| - Auth & RBAC              |
| - Sessions, Reports        |
| - Background Jobs          |
+--------------+--------------+
               |
               | Async / REST / Workers
               v
+-----------------------------+
| Analytics / ML Services    |
| - TCN-based analysis       |
| - Aggregation, scoring     |
+--------------+--------------+
               |
               v
+-----------------------------+
| Database & Storage         |
| - PostgreSQL (JSONB)       |
| - Object Storage (videos)  |
+-----------------------------+

---

## 3. Component Design

### 3.1 Frontend (Web Application)

**Responsibilities:**
- Access webcam using browser APIs.
- Capture physiotherapy session data.
- Perform pose estimation on video frames using TensorFlow.js (e.g., MoveNet Thunder/Lightning).
- Perform lightweight feature extraction (angles, distances) and rep/ROM logic in the browser.
- Use Web Workers (with OffscreenCanvas where available) to keep ML inference off the main UI thread.
- Upload pose data and/or derived features for post-session analysis.
- Display exercise instructions, real-time feedback, and dashboards for reports and analytics.

**Key Modules:**
- Authentication Module
- Session Recorder
- Pose Estimation Engine
- Patient Dashboard
- Doctor Dashboard

---

### 3.2 Backend Services

**Responsibilities:**
- User authentication and role-based authorization.
- Manage physiotherapy sessions.
- Coordinate AI analysis workflows.
- Store and retrieve reports.
- Trigger notifications to doctors.

**Core Services:**
- Authentication Service
- Session Management Service
- Report Service
- Notification Service

---

### 3.3 AI / Machine Learning Service

**Responsibilities:**
- Analyze temporal pose data from sessions.
- Classify physiotherapy exercises.
- Score exercise execution quality.
- Detect incorrect postures and movements.

**Model Architecture:**
- Pose Estimation: MoveNet (Thunder/Lightning) via TensorFlow.js in the browser (primary), with optional server-side batch processing if needed.
- Feature Engineering: Joint angles, distances, velocities, normalized by torso length and orientation.
- Temporal Model: Temporal Convolutional Network (TCN) for sequence modeling and exercise classification.
- Scoring: Combination of model outputs and rule-based/DTW-style alignment against reference patterns.
- Output:
  - Exercise classification
  - Quality score (0–100) and key metrics (ROM, control, tempo)
  - Error classification (optional)

---

## 4. Data Flow Design

### 4.1 Session Execution Flow

Patient logs in

Selects prescribed exercise

Webcam captures video frames

Pose keypoints extracted per frame

Pose sequence uploaded to backend

AI service performs post-session analysis

Performance report generated

Doctor notified

yaml
Copy code

---

### 4.2 Pose Data Processing Pipeline

Webcam Frame
|
Pose Estimation
|
Keypoints (x, y, confidence)
|
Feature Engineering
|
Temporal Pose Sequence
|
LSTM Model
|
Predictions & Scores

yaml
Copy code

---

## 5. Machine Learning Design

### 5.1 Feature Engineering

The following features are derived from pose keypoints:
- Joint angles (knee, elbow, hip, shoulder)
- Relative distances between joints
- Velocity and acceleration of joints
- Symmetry and alignment metrics

---

### 5.2 Model Input & Output

**Input Shape:**
(batch_size, time_steps, feature_dimension)

yaml
Copy code

**Outputs:**
- Exercise label (classification)
- Performance score (regression)
- Error type (optional classification)

---

### 5.3 Training Pipeline

- Collection of physiotherapy exercise datasets.
- Pose extraction and normalization.
- Data augmentation (noise injection, temporal scaling).
- Supervised training of LSTM model.
- Model validation and periodic retraining.

---

## 6. Database Design

### 6.1 Core Entities

- User
- Session
- PoseData
- Report
- Exercise

---

### 6.2 Simplified Schema

User(id, name, email, role)
Session(id, user_id, exercise_id, timestamp)
PoseData(session_id, frame_no, keypoints)
Report(id, session_id, score, summary)
Exercise(id, name, description)
