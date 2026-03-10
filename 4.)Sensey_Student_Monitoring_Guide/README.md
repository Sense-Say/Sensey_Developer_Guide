#  SENSEY: Intelligent 3D Classroom Assistant
### **Final Production Release (v1.0)**

SENSEY is a high-performance, heterogeneous AI system designed to assist **blind teachers** in monitoring classroom environments. By fusing the **Hailo-8 NPU**, **OAK-D Lite VPU**, and **Raspberry Pi 5 CPU**, the system provides real-time 3D pose estimation, student identification, and natural voice feedback—all while operating 100% offline.

---

##  System Architecture

To maintain high performance without thermal throttling or undervoltage, SENSEY distributes tasks across dedicated hardware:

*   **Hailo-8 NPU:** Executes the heavy mathematical lifting for **YOLOv8-Pose** (17-point skeletons).
*   **OAK-D Lite VPU:** Handles **Stereo Depth calculation** (Z-axis) and **High-Speed Face Detection**.
*   **Raspberry Pi 5 CPU:** Manages **Behavioral Logic**, **Text-to-Speech (Piper)**, and **Identity Matching**.

---

## Hardware Requirements

| Component | Specification |
| :--- | :--- |
| **Microcomputer** | Raspberry Pi 5 (8GB Recommended) |
| **AI Accelerator** | Raspberry Pi AI Kit (Hailo-8L) or Hailo-8 Drive |
| **3D Sensor** | OAK-D Lite (USB 3.0 Connection) |
| **Speaker** | 3.5mm Jack or USB Audio (For Piper TTS) |
| **Trigger** | Momentary Push Button (Connected to **GPIO 26** & **GND**) |
| **Mounting** | Wearable/Chest-mounted at **1.2m Height** |

---

## Software Installation

### 1. Initial Environment
The system must be run within a virtual environment. Assuming you have already installed the Hailo Software Suite:
```bash
cd /home/raspberrypi/hailo-apps
source venv_hailo_apps/bin/activate
```

### 2. Automated Setup
The `sensey_setup.py` script automates all library installations and resource downloads (Piper binary, Voice models, and AI Blobs).

1.  Place `sensey_setup.py` in `/home/raspberrypi/`.
2.  Run the orchestrator:
    ```bash
    python3 /home/raspberrypi/sensey_setup.py
    ```

---

## Final File Distribution

| Directory | Important Files |
| :--- | :--- |
| **`~/Student Monitoring/`** | `standalone_poseversion2.py` (Master), `cpu_process_screenshot.py` (Identity), `action_logic.py` (Math Rules) |
| **`~/TTS-STT-AUDIO/`** | `fast_face.blob` (Detector), `en_US-lessac-medium.onnx` (Voice) |
| **`~/TTS-STT-AUDIO/piper/`** | `piper` (Offline TTS Engine) |
| **`~/Documents/`** | `cpu_face_enrollment.py` (Enrollment GUI) |

---

## The SENSEY Workflow

### Phase 1: Enrollment (The "Brain")
Before monitoring, the system must learn student faces.
1.  Run `python3 "/home/raspberrypi/Documents/cpu_face_enrollment.py"`.
2.  Follow the **Vocal Guide**: Look Straight, Left 45°, Right 45°, Up, and Down.
3.  Click **Generate Encodings**.

### Phase 2: Monitoring (The "Eyes")
1.  Run `python3 "/home/raspberrypi/Student Monitoring/standalone_poseversion2.py"`.
2.  The device will announce: *"Classroom monitoring is ready."*
3.  The 4:3 widescreen feed ($1344 \times 1008$) ensures maximum vertical visibility of desks and hands.

### Phase 3: Identification (The "Voice")
1.  When a report is needed, press the **Physical Button (GPIO 26)**.
2.  The system captures a **1080p high-resolution snapshot**.
3.  The CPU matches faces to body boxes and updates the name map.
4.  **Audio Feedback:** Piper speaks the report (e.g., *"Edward and 2 Students are conducting National Anthem."*).

---

## Technical Trivia & Logic

*   **1.2m Baseline:** All "Set B" behavioral rules use the teacher's height to calculate **Absolute Ground Height**. A hand is only "Raised" if the wrist is physically $> 1.45\text{m}$ from the floor.
*   **4-Point Fusion:** The **National Anthem** gesture is detected by calculating the geometric center of both shoulders and both wrists, creating a dynamic "Heart Zone."
*   **Infinity Lock:** By setting `setManualFocus(0)`, the system ensures the focal length remains constant, preventing "Math Drift" in 3D depth calculations.
*   **Greedy Match:** During snapshots, if two students look like "Edward," the system gives the name to the student with the higher confidence score and searches for a different name for the other.
*   **Offline Privacy:** Piper TTS and Local Encodings ensure no student data or teacher voice is ever transmitted over the internet.

***
**Project SENSEY is a stable, professional AI solution ready for classroom deployment.**
