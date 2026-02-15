# AI-Powered Physiotherapy Web Application -- Requirements Document

**Team Name:** Ghrtham
**Team Leader:** Rudray Mehra
**Hackathon:** AI for Bharat Hackathon (Powered by AWS)
**Problem Statement:** Design an AI solution that improves efficiency, understanding, or support within healthcare or life-sciences ecosystems.

---

## 1. Project Overview

The healthcare industry faces a significant gap in physiotherapy access due to increasing patient volume and limited availability of physiotherapists. This project proposes an AI-powered physiotherapy web application that enables patients to perform prescribed physiotherapy exercises remotely using a standard webcam. The system uses computer vision and deep learning to analyze a patient's body posture and movements during an exercise session.

Instead of requiring real-time supervision, the platform performs **post-session analysis**, where body pose data is processed using a temporal neural network (LSTM) to classify exercises, evaluate execution quality, detect common posture errors, and generate a performance score. A detailed summary report is then shared with the physiotherapist, allowing them to monitor progress and intervene only when necessary.

This approach helps bridge the gap between the growing number of physiotherapy patients and limited healthcare professionals, improving accessibility, scalability, and consistency in rehabilitation care while maintaining medical oversight.

---

## 2. How It Solves the Problem

- Allows physiotherapists to track multiple patients remotely, reducing the need for frequent in-person visits.
- Helps patients perform exercises correctly by identifying posture mistakes and risky movements.
- Shows clear progress over time, helping doctors make better treatment decisions.
- Makes physiotherapy more accessible and affordable using just a basic camera-enabled device, especially for patients in smaller cities.

---

## 3. Objectives

- Enable patients to perform physiotherapy sessions remotely using a web browser.
- Analyze patient posture and movement using AI-based pose estimation (TensorFlow.js + MoveNet) running directly in the browser.
- Classify physiotherapy exercises and score execution quality using LSTM-based post-session analysis.
- Generate automated post-session reports for doctors.
- Reduce dependency on real-time physiotherapist supervision.
- Improve accessibility, scalability, and consistency in physiotherapy care.
- Provide an assistive AI that supports physiotherapists rather than replacing them.

---

## 4. Target Users

### 4.1 Patients
- Perform physiotherapy exercises at home using any webcam-enabled device.
- Follow guided exercise videos.
- View summarised feedback, recovery trends, and session history.

### 4.2 Physiotherapists / Doctors
- Review detailed post-session reports and progress analytics.
- Monitor patient progress remotely.
- Receive red-flag alerts for risky or poor performance to prioritise critical cases.

### 4.3 System Administrators
- Manage users and roles.
- Maintain system configurations and models.

---

## 5. Functional Requirements

### 5.1 User Authentication & Authorization
- User registration and login.
- Role-based access control (Patient, Doctor, Admin).
- Secure authentication using **AWS Cognito + JWT + HTTPS**.

---

### 5.2 Remote Guided Physiotherapy Sessions
- Patients can perform prescribed exercises at home using any webcam-enabled device.
- Guided videos help ensure correct execution while removing the need for frequent clinic visits.
- Webcam access through browser using **WebRTC**.
- Capture pose data during the exercise session.

---

### 5.3 AI-Based Posture & Movement Analysis
- Extract human body keypoints from webcam video using **TensorFlow.js + MoveNet** directly in the browser.
- Computer vision extracts body pose data during sessions.
- Deep learning models (LSTM) analyse full exercise sequences to detect posture errors, evaluate movement quality, and generate performance scores.
- Store extracted pose data for post-session processing.

---

### 5.4 Post-Session Analysis
- Perform analysis after session completion (non-real-time / post-session).
- Body pose data processed using LSTM model on **AWS SageMaker**.
- Classify exercises, evaluate execution quality, detect common posture errors.
- Generate a performance score.

---

### 5.5 Patient Progress Tracking Dashboard
- Patients can view summarised feedback, recovery trends, and session history.
- Helps patients stay motivated and consistent throughout their rehabilitation journey.

---

### 5.6 Clinician Dashboard with Automated Reports & Risk Alerts
- Physiotherapists receive detailed session reports and progress analytics.
- Red-flag alerts for risky or poor performance.
- Allows clinicians to prioritise critical cases and intervene only when necessary.
- Track patient improvement over time.

---

### 5.7 Report Generation
- Automatically generate a structured post-session report.
- Include:
  - Exercise name
  - Performance score
  - Detected posture errors
  - Improvement suggestions
- Reports shared with physiotherapist via clinician dashboard.
- Patients can view summarised feedback.

---

### 5.8 Notifications
- Notify doctors when a session is completed.
- Alert doctors if performance scores fall below a threshold (red-flag alerts).

---

## 6. Non-Functional Requirements

### 6.1 Performance
- Post-session analysis should complete within acceptable time limits.
- System should support multiple concurrent users.
- Browser-side pose estimation should run on commodity hardware without blocking the UI.

---

### 6.2 Scalability
- Built on **AWS** for reliable storage, model inference, and user management.
- Seamless scalability as user volume grows.
- Support onboarding of new exercises and models.
- Scalable and cost-effective solution for large patient populations.

---

### 6.3 Security
- Encrypted communication using **HTTPS**.
- Authentication via **AWS Cognito + JWT**.
- Secure **role-based access** for doctors and patients.
- Secure data handling and storage.

---

### 6.4 Reliability
- Fault-tolerant backend services.
- Graceful handling of webcam or network failures.

---

### 6.5 Usability
- Simple and intuitive UI for patients with minimal technical knowledge.
- Clear visual feedback and guided exercise instructions.
- Accessible on modern browsers without additional installations.
- Works with just a basic camera-enabled device.

---

## 7. Technical Requirements

### 7.1 Frontend
- **React.js + WebRTC** -- for browser-based guided physiotherapy sessions using webcam access.

---

### 7.2 Pose Estimation
- **TensorFlow.js + MoveNet** -- to extract body keypoints directly in the browser.

---

### 7.3 AI / ML
- **Python + PyTorch/TensorFlow** (LSTM models on **AWS SageMaker**) -- for session-level posture analysis and performance scoring.

---

### 7.4 Backend APIs
- **Spring Boot** deployed on **AWS EC2 / ECS** -- handles authentication, session management, and report generation.

---

### 7.5 Storage & Database
- **Amazon S3** for pose/session data.
- **Amazon RDS (PostgreSQL)** for user and medical records.

---

### 7.6 Authentication & Security
- **AWS Cognito + JWT + HTTPS** -- secure role-based access for doctors and patients.

---

### 7.7 Deployment & Scaling
- **Docker + AWS (ECS/EC2)** -- enabling scalable inference and application hosting.

---

## 8. Estimated Implementation Cost

- **Development Cost:** Minimal (student / in-house development using open-source tools)
- **Cloud Infrastructure:** ~3,000 -- 6,000 INR per month (basic compute, storage, APIs)
- **AI Model Training & Testing:** ~2,000 -- 5,000 INR (one-time, cloud compute usage)
- **Total Estimated Cost:** ~5,000 -- 15,000 INR for prototype and early-stage deployment

---

## 9. Assumptions & Constraints

- Users have access to a webcam-enabled device.
- Internet connectivity is required to upload session data.
- Initial system supports a limited set of physiotherapy exercises.
- AI predictions assist doctors but do not replace medical judgment (assistive AI).

---

## 10. Future Enhancements

- Real-time feedback during exercises.
- Support for wearable sensor integration.
- Multi-language support.
- AI-driven personalized exercise recommendations.
- Mobile application support.

---

## 11. Conclusion

This system aims to bridge the gap between patients and physiotherapists by leveraging AI-based pose analysis (MoveNet) and deep learning (LSTM) built on AWS. The solution improves accessibility, reduces workload on healthcare professionals, and enables data-driven physiotherapy monitoring in a scalable, secure, and cost-effective manner -- making physiotherapy accessible using just a basic camera-enabled device, especially for patients in smaller cities.
