# SignBridge

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![OpenCV](https://img.shields.io/badge/OpenCV-Real--Time_Vision-5C3EE8?logo=opencv&logoColor=white)](https://opencv.org/)
[![MediaPipe](https://img.shields.io/badge/MediaPipe-21_3D_Hand_Landmarks-0097A7?logo=google&logoColor=white)](https://developers.google.com/mediapipe)
[![Category](https://img.shields.io/badge/Domain-Assistive_Computer_Vision-10b981)](#)

**SignBridge** is an assistive real-time Sign Language to Text translator built with **Python**, **OpenCV**, and **MediaPipe**. It processes live webcam streams to track 21 3D hand landmarks in real time, classifies hand geometry into gestures and phrases, and renders dynamic on-screen subtitle translations.

---

## System Architecture

```mermaid
flowchart LR
    Cam["Webcam Feed (OpenCV VideoCapture)"] --> Pre["Frame Normalization (BGR → RGB)"]
    Pre --> MP["MediaPipe Hands (21 3D Landmarks)"]
    MP --> Feat["Geometric Feature Extractor (Angles & Euclidean Vectors)"]
    Feat --> Clf["Gesture Classifier & Temporal Smoothing"]
    Clf --> HUD["OpenCV Skeletal HUD & Live Subtitle Overlay"]
```

### Core Pipeline Components

1. **Live Video Ingestion (`OpenCV`)**: Captures low-latency camera frames via `cv2.VideoCapture`, handles horizontal mirroring, and normalizes coordinate spaces for display.
2. **21-Point 3D Hand Tracking (`MediaPipe Hands`)**: Extracts 21 3D knuckle, joint, and fingertip coordinates $(x, y, z)$ per hand in real time with sub-frame landmark locking.
3. **Geometric Gesture Classification**: Computes wrist-relative Euclidean distances and inter-phalangeal joint angles so gesture recognition remains invariant to hand scale and screen position.
4. **Dynamic Subtitle Overlay**: Renders the 21-point hand skeleton, confidence telemetry, and translated text subtitles directly onto the live video viewport.

---

## Key Features

- **Real-Time 21 3D Landmark Tracking**: Tracks all 21 hand landmarks (wrist, thumb, index, middle, ring, and pinky joints) at 30–60 FPS on standard consumer webcams.
- **Scale-Invariant Gesture Recognition**: Normalizes landmark vectors relative to wrist-to-middle-MCP distance to maintain accuracy across varying camera distances.
- **Temporal Debouncing & Confidence Gating**: Filters transient frame jitter during sign transitions before committing a translated word or phrase to the subtitle bar.
- **Zero-GPU Accessibility**: Designed to run smoothly on standard CPU hardware for everyday assistive communication.

---

## Getting Started

### Prerequisites
- **Python 3.10+**
- A connected webcam

### Installation

1. **Clone the Repository**:
   ```powershell
   git clone https://github.com/nnickahh/signbridge.git
   cd signbridge
   ```

2. **Create & Activate a Virtual Environment**:
   ```powershell
   python -m venv .venv
   .venv\Scripts\activate
   ```

3. **Install Dependencies**:
   ```powershell
   pip install opencv-python mediapipe numpy
   ```

### Running SignBridge

```powershell
python main.py
```

- Position your hand clearly in front of the webcam.
- The **OpenCV HUD** will lock onto all 21 hand landmarks, display the real-time confidence score, and overlay translated subtitles at the bottom of the frame.
- Press `q` or `ESC` to close the video stream cleanly.

---

## Hand Landmark Reference (MediaPipe 21-Point Topology)

| Landmark IDs | Anatomical Region | Role in Gesture Classification |
| :--- | :--- | :--- |
| `0` | `WRIST` | Root coordinate origin for translation normalization |
| `1 – 4` | `THUMB (CMC, MCP, IP, TIP)` | Thumb abduction/opposition state |
| `5 – 8` | `INDEX_FINGER (MCP, PIP, DIP, TIP)` | Index extension & pointing vector |
| `9 – 12` | `MIDDLE_FINGER (MCP, PIP, DIP, TIP)` | Scale reference vector & middle flexion |
| `13 – 16` | `RING_FINGER (MCP, PIP, DIP, TIP)` | Ring flexion angle & spread |
| `17 – 20` | `PINKY (MCP, PIP, DIP, TIP)` | Pinky extension & boundary orientation |

---

## Author

**Nick Fong** ([@nnickahh](https://github.com/nnickahh))
