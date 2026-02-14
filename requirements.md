# AI-Powered Physiotherapy Web Application – Requirements Document

## 1. Project Overview

The healthcare industry faces a significant gap in physiotherapy access due to increasing patient volume and limited availability of physiotherapists. This project proposes an AI-powered physiotherapy web application that enables patients to perform prescribed physiotherapy exercises remotely using a webcam, with a **browser-first, production-grade AI stack**.

The system leverages real-time pose estimation in the browser (e.g., MoveNet via TensorFlow.js) and temporal deep learning models (e.g., Temporal Convolutional Networks, TCNs) to analyze patient movements post-session, evaluate exercise quality, and generate detailed performance summaries for physiotherapists. This reduces the need for constant supervision while ensuring accurate monitoring and progress tracking.

The initial clinical focus is **post-surgical joint replacement (TKA/THA) and general orthopedic rehabilitation**, where standardized protocols and clear range-of-motion targets make pose-based assessment highly effective.

---

## 2. Objectives

- Enable patients to perform physiotherapy sessions remotely using a web browser.
- Analyze patient posture and movement using AI-based pose estimation running directly in the browser.
- Classify physiotherapy exercises over time using temporal sequence models and score execution quality (repetitions, range of motion, form).
- Generate automated post-session reports for doctors.
- Reduce dependency on real-time physiotherapist supervision.
- Improve accessibility, scalability, and consistency in physiotherapy care.
- Ensure the system is designed for healthcare-grade security, privacy, and interoperability (HIPAA-ready, FHIR-ready design).

---

## 3. Target Users

### 3.1 Patients
- Perform physiotherapy exercises at home.
- Receive guided exercise instructions.
- View session summaries and progress over time.

### 3.2 Physiotherapists / Doctors
- Review post-session reports.
- Monitor patient progress remotely.
- Identify incorrect movements or deterioration.

### 3.3 System Administrators
- Manage users and roles.
- Maintain system configurations and models.

---

## 4. Functional Requirements

### 4.1 User Authentication & Authorization
- User registration and login.
- Role-based access control (Patient, Doctor, Admin).
- Secure session management using JWT.

---

### 4.2 Physiotherapy Session Management
- Ability for patients to start and end a physiotherapy session.
- Webcam access through browser for session recording.
- Display of exercise instructions via video or animations.
- Capture pose data during the exercise session.

---

### 4.3 Pose Estimation & Data Capture
- Extract human body keypoints from webcam video using a pose estimation model.
- Store extracted pose data instead of raw video by default to preserve privacy.
- Capture temporal pose sequences for the entire exercise session.

---

### 4.4 AI-Based Exercise Analysis
- Classify the performed exercise using a trained deep learning model.
- Evaluate correctness and quality of movements.
- Score exercises based on posture accuracy, consistency, range of motion, and adherence to prescribed protocols.
- Detect common errors in exercise execution and unsafe movements.
- Count repetitions using angle-based state machines per exercise.

---

### 4.5 Post-Session Analysis
- Perform analysis after session completion (non-real-time).
- Aggregate frame-level predictions into session-level metrics.
- Identify trends such as fatigue or improvement across sessions (e.g., ROM progression, adherence, pain vs. performance).

---

### 4.6 Report Generation
- Automatically generate a structured post-session report.
- Include:
  - Exercise name
  - Performance score
  - Detected errors
  - Improvement suggestions
- Provide reports to doctors via a dashboard.
- Allow patients to view summarized feedback.

---

### 4.7 Doctor Dashboard
- View patient session history.
- Access detailed reports and analytics.
- Track patient improvement over time.
- Flag sessions requiring medical attention.

---

### 4.8 Notifications
- Notify doctors when a session is completed.
- Alert doctors if performance scores fall below a threshold.

---

## 5. Non-Functional Requirements

### 5.1 Performance
- Post-session analysis should complete within acceptable time limits.
- System should support multiple concurrent users.
- Browser-side pose estimation and basic feedback should run in real time on commodity hardware without blocking the UI.

---

### 5.2 Scalability
- Modular architecture to allow independent scaling of AI services.
- Support onboarding of new exercises and models.
- Support scaling to multiple clinics and therapists, with separation of organization data.

---

### 5.3 Security
- Encrypted communication (HTTPS).
- Secure storage of sensitive health data.
- Compliance with healthcare data privacy principles (e.g., HIPAA-ready architecture where applicable).
- Explicit patient consent for video or data usage.
- Fine-grained access control and audit logging for all access to patient data.

---

### 5.4 Reliability
- Fault-tolerant backend services.
- Graceful handling of webcam or network failures.

---

### 5.5 Usability
- Simple and intuitive UI for patients with minimal technical knowledge.
- Clear visual feedback and instructions.
- Accessible on modern browsers without additional installations.

---

## 6. Technical Requirements

### 6.1 Frontend
- Web-based application using a modern React framework (e.g., Next.js with App Router).
- Webcam access using browser APIs.
- Pose estimation using JavaScript-based ML libraries (e.g., TensorFlow.js with MoveNet Thunder/Lightning).
- Temporal feature extraction and basic exercise logic (rep counting, ROM estimation) running in the browser, optionally using Web Workers to avoid blocking the UI.
- Interactive dashboards for patients and doctors (progress, adherence, outcomes).

---

### 6.2 Backend
- REST-based API architecture (e.g., FastAPI).
- Secure authentication and authorization (JWT-based sessions, role-based access control).
- Session, report, and user management services.
- Background processing for heavier analytics or aggregation (e.g., Celery/RQ with Redis or equivalent).

---

### 6.3 Machine Learning
- Pose estimation model for feature extraction (e.g., MoveNet via TensorFlow.js in the browser).
- Custom-trained temporal deep learning model (e.g., Temporal Convolutional Network, TCN) for movement analysis and exercise classification.
- Rule-based and/or DTW-style scoring components for detailed form and quality assessment.
- Model versioning and retraining capability, with the ability to introduce new exercises and update models safely.

---

### 6.4 Data Storage
- Store pose keypoints (or derived features), session metadata, and reports (e.g., in PostgreSQL with JSON/JSONB support).
- Optional secure storage for raw videos when required, using encrypted object storage and strict access controls.
- Support for storing aggregated metrics and selected keyframes rather than full raw sequences where appropriate, to balance fidelity and storage costs.

---

## 7. Assumptions & Constraints

- Users have access to a webcam-enabled device.
- Internet connectivity is required to upload session data.
- Initial system supports a limited set of physiotherapy exercises.
- AI predictions assist doctors but do not replace medical judgment.

---

## 8. Future Enhancements

- Real-time feedback during exercises.
- Support for wearable sensor integration.
- Multi-language support.
- AI-driven personalized exercise recommendations.
- Mobile application support.

---

## 9. Conclusion

This system aims to bridge the gap between patients and physiotherapists by leveraging AI-based pose analysis and deep learning. The solution improves accessibility, reduces workload on healthcare professionals, and enables data-driven physiotherapy monitoring in a scalable and secure manner.
