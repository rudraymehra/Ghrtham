# System Design Document
## AI-Powered Physiotherapy Web Application

**Team Name:** Ghrtham
**Team Leader:** Rudray Mehra
**Hackathon:** AI for Bharat Hackathon (Powered by AWS)
**Problem Statement:** Design an AI solution that improves efficiency, understanding, or support within healthcare or life-sciences ecosystems.

---

## 1. Design Overview

This document describes the system design and architecture of an AI-powered physiotherapy web application that enables patients to perform prescribed physiotherapy exercises remotely using a standard webcam. The system uses computer vision and deep learning to analyze a patient's body posture and movements during an exercise session.

Instead of requiring real-time supervision, the platform performs **post-session analysis**, where body pose data is processed using a temporal neural network (LSTM) to classify exercises, evaluate execution quality, detect common posture errors, and generate a performance score. A detailed summary report is then shared with the physiotherapist, allowing them to monitor progress and intervene only when necessary.

This approach helps bridge the gap between the growing number of physiotherapy patients and limited healthcare professionals, improving accessibility, scalability, and consistency in rehabilitation care while maintaining medical oversight.

---

## 2. How It Differs From Existing Solutions

- Works **entirely through a web browser** using a standard webcam (no wearables).
- Focuses on **post-session AI analysis** rather than fragile real-time feedback.
- Provides **objective, data-driven performance scoring** instead of subjective observation.
- Generates **automated reports for doctors**, reducing their manual workload.

---

## 3. USP of the Proposed Solution

- **No additional hardware required** -- works with a standard webcam.
- **Post-session AI analysis** ensures higher accuracy and reliability.
- **Objective performance scoring** based on movement data.
- **Doctor-centric reporting**, not just patient-facing feedback.
- **Scalable and cost-effective** solution for large patient populations.
- **Assistive AI** that supports physiotherapists rather than replacing them.

---

## 4. High-Level Architecture

```
+-----------------------------+
| Web Browser (React.js)     |
| - Webcam + WebRTC          |
| - TF.js MoveNet (Pose)    |
+--------------+--------------+
               |
               | HTTPS / REST
               v
+-----------------------------+
| Backend API (Spring Boot)  |
| - Auth (AWS Cognito + JWT) |
| - Sessions, Reports        |
+--------------+--------------+
               |
               v
+-----------------------------+
| AI / ML Service            |
| - Pose + LSTM              |
| - AWS SageMaker            |
+--------------+--------------+
               |
               v
+-----------------------------+
| Database & Storage         |
| - Amazon RDS (PostgreSQL)  |
| - Amazon S3                |
+-----------------------------+
```

### ML Pipeline

```
Webcam Frames
      |
Pose Estimation (TensorFlow.js + MoveNet)
      |
Feature Extraction
      |
LSTM Model (AWS SageMaker)
      |
Predictions & Scores
```

---

## 5. Component Design

### 5.1 Frontend (Web Application)

**Technology:** React.js + WebRTC

**Responsibilities:**
- Access webcam using WebRTC browser APIs.
- Capture physiotherapy session data.
- Perform pose estimation on video frames using TensorFlow.js + MoveNet to extract body keypoints directly in the browser.
- Upload pose data for post-session analysis.
- Display guided exercise videos/instructions.
- Display dashboards for reports and analytics.

**Key Modules:**
- Authentication Module
- Session Recorder (WebRTC-based)
- Pose Estimation Engine (TensorFlow.js + MoveNet)
- Patient Dashboard (feedback, trends, session history)
- Doctor Dashboard (reports, analytics, alerts)

---

### 5.2 Backend Services

**Technology:** Spring Boot deployed on AWS EC2 / ECS

**Responsibilities:**
- User authentication and role-based authorization (AWS Cognito + JWT).
- Manage physiotherapy sessions.
- Coordinate AI analysis workflows.
- Store and retrieve reports.
- Trigger notifications and risk alerts to doctors.

**Core Services:**
- Authentication Service (AWS Cognito)
- Session Management Service
- Report Generation Service
- Notification / Alert Service

---

### 5.3 AI / Machine Learning Service

**Technology:** Python + PyTorch/TensorFlow, LSTM models on AWS SageMaker

**Responsibilities:**
- Analyze temporal pose data from sessions.
- Classify physiotherapy exercises.
- Score exercise execution quality.
- Detect incorrect postures and risky movements.

**Model Architecture:**
- **Pose Estimation:** TensorFlow.js + MoveNet -- extracts body keypoints directly in the browser.
- **Feature Engineering:** Joint angles, distances, velocities derived from keypoints.
- **Temporal Model:** LSTM (Long Short-Term Memory) network for session-level posture analysis and performance scoring.
- **Output:**
  - Exercise classification
  - Quality/performance score
  - Posture error detection

---

## 6. Data Flow Design

### 6.1 Session Execution Flow

1. Patient logs in
2. Selects prescribed exercise
3. Guided video plays; webcam captures frames via WebRTC
4. Pose keypoints extracted per frame (TensorFlow.js + MoveNet in browser)
5. Pose sequence uploaded to backend
6. AI service (LSTM on SageMaker) performs post-session analysis
7. Performance report generated
8. Doctor notified / report available on clinician dashboard

---

### 6.2 Pose Data Processing Pipeline

```
Webcam Frame
      |
Pose Estimation (MoveNet)
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
```

---

## 7. Machine Learning Design

### 7.1 Feature Engineering

The following features are derived from pose keypoints:
- Joint angles (knee, elbow, hip, shoulder)
- Relative distances between joints
- Velocity and acceleration of joints
- Symmetry and alignment metrics

---

### 7.2 Model Input & Output

**Input Shape:**
`(batch_size, time_steps, feature_dimension)`

**Outputs:**
- Exercise label (classification)
- Performance score (regression)
- Error type (posture error classification)

---

### 7.3 Training Pipeline

- Collection of physiotherapy exercise datasets.
- Pose extraction and normalization.
- Data augmentation (noise injection, temporal scaling).
- Supervised training of LSTM model on AWS SageMaker.
- Model validation and periodic retraining.

---

## 8. Database Design

### 8.1 Core Entities

- User
- Session
- PoseData
- Report
- Exercise

---

### 8.2 Simplified Schema

```
User(id, name, email, role)
Session(id, user_id, exercise_id, timestamp)
PoseData(session_id, frame_no, keypoints)
Report(id, session_id, score, summary)
Exercise(id, name, description)
```

**Storage:**
- **Amazon RDS (PostgreSQL):** User data, medical records, session metadata, reports.
- **Amazon S3:** Pose/session data, model artifacts.

---

## 9. Security Design

- Role-based access control (RBAC) for patients, physiotherapists, and administrators.
- Authentication via **AWS Cognito + JWT**.
- Encrypted data transmission using **HTTPS**.
- Secure role-based access for doctors and patients.
- Encryption at rest for databases and object storage.
- Audit logging for all access to patient-related data.

---

## 10. Scalability & Deployment Design

- **Containerized deployment** using Docker.
- Deployed on **AWS (ECS / EC2)** enabling scalable inference and application hosting.
- Independent scaling of AI/ML services via **AWS SageMaker**.
- Browser-first inference for pose estimation to offload real-time compute from the backend.
- **Amazon S3** for large data storage (pose data, model artifacts).
- Seamless scalability as user volume grows.

---

## 11. Error Handling & Reliability

- Graceful handling of webcam or pose detection failures.
- Retry mechanisms for AI service calls.
- Centralized logging and monitoring.

---

## 12. Design Constraints

- Web-based execution limits real-time processing; system focuses on post-session analysis.
- Initial system supports a limited set of exercises.
- AI predictions assist but do not replace medical expertise (assistive AI).

---

## 13. Estimated Implementation Cost

- **Development Cost:** Minimal (student / in-house development using open-source tools)
- **Cloud Infrastructure:** ~3,000 -- 6,000 INR per month (basic compute, storage, APIs)
- **AI Model Training & Testing:** ~2,000 -- 5,000 INR (one-time, cloud compute usage)
- **Total Estimated Cost:** ~5,000 -- 15,000 INR for prototype and early-stage deployment

---

## 14. Future Enhancements

- Real-time corrective feedback.
- Transformer-based temporal models.
- Wearable sensor integration.
- Mobile application support.

---

## 15. Conclusion

This design outlines a scalable, secure, and modular architecture for an AI-powered physiotherapy system built on AWS that leverages pose estimation (MoveNet) and deep learning (LSTM) to enhance remote rehabilitation. The system supports physiotherapists rather than replacing them, providing objective performance scoring and automated doctor-centric reporting while making physiotherapy more accessible and affordable using just a basic camera-enabled device.
