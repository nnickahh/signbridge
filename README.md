# signbridge

[![python](https://img.shields.io/badge/python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![opencv](https://img.shields.io/badge/opencv-real--time_vision-5C3EE8?logo=opencv&logoColor=white)](https://opencv.org/)
[![mediapipe](https://img.shields.io/badge/mediapipe-21_3d_hand_landmarks-0097A7?logo=google&logoColor=white)](https://developers.google.com/mediapipe)
[![category](https://img.shields.io/badge/domain-assistive_computer_vision-10b981)](#)

**signbridge** is an assistive real-time sign language to text translator built with **python**, **opencv**, and **mediapipe**. it processes live webcam streams to track 21 3d hand landmarks in real time, classifies hand geometry into gestures and phrases, and renders dynamic on-screen subtitle translations.

---

## system architecture

```mermaid
flowchart LR
    Cam["webcam feed (opencv videocapture)"] --> Pre["frame normalization (bgr → rgb)"]
    Pre --> MP["mediapipe hands (21 3d landmarks)"]
    MP --> Feat["geometric feature extractor (angles & euclidean vectors)"]
    Feat --> Clf["gesture classifier & temporal smoothing"]
    Clf --> HUD["opencv skeletal hud & live subtitle overlay"]
```

### core pipeline components

1. **live video ingestion (`opencv`)**: captures low-latency camera frames via `cv2.VideoCapture`, handles horizontal mirroring, and normalizes coordinate spaces for display.
2. **21-point 3d hand tracking (`mediapipe hands`)**: extracts 21 3d knuckle, joint, and fingertip coordinates $(x, y, z)$ per hand in real time with sub-frame landmark locking.
3. **geometric gesture classification**: computes wrist-relative euclidean distances and inter-phalangeal joint angles so gesture recognition remains invariant to hand scale and screen position.
4. **dynamic subtitle overlay**: renders the 21-point hand skeleton, confidence telemetry, and translated text subtitles directly onto the live video viewport.

---

## key features

- **real-time 21 3d landmark tracking**: tracks all 21 hand landmarks (wrist, thumb, index, middle, ring, and pinky joints) at 30–60 fps on standard consumer webcams.
- **scale-invariant gesture recognition**: normalizes landmark vectors relative to wrist-to-middle-mcp distance to maintain accuracy across varying camera distances.
- **temporal debouncing & confidence gating**: filters transient frame jitter during sign transitions before committing a translated word or phrase to the subtitle bar.
- **zero-gpu accessibility**: designed to run smoothly on standard cpu hardware for everyday assistive communication.

---

## getting started

### prerequisites
- **python 3.10+**
- a connected webcam

### installation

1. **clone the repository**:
   ```powershell
   git clone https://github.com/nnickahh/signbridge.git
   cd signbridge
   ```

2. **create & activate a virtual environment**:
   ```powershell
   python -m venv .venv
   .venv\Scripts\activate
   ```

3. **install dependencies**:
   ```powershell
   pip install opencv-python mediapipe numpy
   ```

### running signbridge

```powershell
python main.py
```

- position your hand clearly in front of the webcam.
- the **opencv hud** will lock onto all 21 hand landmarks, display the real-time confidence score, and overlay translated subtitles at the bottom of the frame.
- press `q` or `esc` to close the video stream cleanly.

---

## hand landmark reference (mediapipe 21-point topology)

| landmark ids | anatomical region | role in gesture classification |
| :--- | :--- | :--- |
| `0` | `wrist` | root coordinate origin for translation normalization |
| `1 – 4` | `thumb (cmc, mcp, ip, tip)` | thumb abduction/opposition state |
| `5 – 8` | `index_finger (mcp, pip, dip, tip)` | index extension & pointing vector |
| `9 – 12` | `middle_finger (mcp, pip, dip, tip)` | scale reference vector & middle flexion |
| `13 – 16` | `ring_finger (mcp, pip, dip, tip)` | ring flexion angle & spread |
| `17 – 20` | `pinky (mcp, pip, dip, tip)` | pinky extension & boundary orientation |

---

## author

**nick fong** ([@nnickahh](https://github.com/nnickahh))
