# Pose Estimation & Classification using OpenCV + MediaPipe

Detect human body landmarks in images and live webcam video, then classify the pose into **Warrior II**, **T-Pose**, or **Tree Pose** using joint-angle heuristics. Built with Google's MediaPipe Pose model and OpenCV.

## Table of Contents

- [Overview](#overview)
- [Demo](#demo)
- [Features](#features)
- [Technologies Used](#technologies-used)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [How It Works](#how-it-works)
- [Evaluation](#evaluation)
- [Limitations](#limitations)
- [Future Improvements](#future-improvements)

## Overview

This project uses [MediaPipe](https://github.com/google-ai-edge/mediapipe)'s pre-trained Pose model to detect 33 body landmarks in an image or video frame, then applies a rule-based classifier — built on the angles between key joints — to recognize three poses: **Warrior II**, **T-Pose**, and **Tree Pose**. It works on static images and in real time from a webcam.

## Demo
Input 
<img width="625" height="810" alt="image" src="https://github.com/user-attachments/assets/bbe19749-6a3f-45b5-949d-6f8d3caf3574" />


Output
<img width="625" height="810" alt="image" src="https://github.com/user-attachments/assets/58ef4a50-20b0-42f0-aec1-5f4c8b98fd8a" />

## Features

- Detects and tracks 33 body keypoints per frame
- Works on static images and real-time webcam video
- Classifies poses (Warrior II, T-Pose, Tree Pose) from joint angles
- Visualizes the skeleton overlay and a 3D landmark plot
- Lightweight — no model training required

## Technologies Used

- **Python 3.12**
- **OpenCV** — image/video I/O and drawing
- **MediaPipe** — pre-trained pose landmark model
- **NumPy** — numerical operations
- **Matplotlib** — visualizing results inline in the notebook

## Project Structure

```
pose-estimation/
├── pose_estimation.ipynb   # Main notebook: detection + classification + webcam demo
├── images/                 # Sample input images (not tracked in git — see below)
├── requirements.txt
└── README.md
```

> Sample images aren't included in this repo. Add your own to `images/` using the filenames referenced in the notebook (`sample.jpg`, `sample1.jpg`, `warriorIIpose.jpg`, `treepose3.jpg`, `tpose.jpg`, `unknown.jpg`), or update the filenames in the notebook to match your own images.

## Getting Started

### Prerequisites

- Python 3.9+
- A webcam (only required for the real-time demo)

### Installation

1. Clone the repository:
   ```sh
   git clone https://github.com/your-username/pose-estimation.git
   cd pose-estimation
   ```
2. Install dependencies:
   ```sh
   pip install -r requirements.txt
   ```
   or directly:
   ```sh
   pip install opencv-python mediapipe numpy matplotlib
   ```
3. Add your sample images to an `images/` folder in the project root.
4. Launch the notebook:
   ```sh
   jupyter notebook pose_estimation.ipynb
   ```
5. Run all cells in order — later cells depend on functions and variables defined earlier.

### Minimal standalone example

For a quick real-time demo outside the notebook:

```python
import cv2
import mediapipe as mp

mp_pose = mp.solutions.pose
pose = mp_pose.Pose()
mp_drawing = mp.solutions.drawing_utils

cap = cv2.VideoCapture(0)
while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break
    image = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    results = pose.process(image)
    image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
    if results.pose_landmarks:
        mp_drawing.draw_landmarks(image, results.pose_landmarks, mp_pose.POSE_CONNECTIONS)
    cv2.imshow('Pose Estimation', image)
    if cv2.waitKey(10) & 0xFF == ord('q'):
        break
cap.release()
cv2.destroyAllWindows()
```

## How It Works

1. Load an image, or capture a frame from the webcam.
2. Convert the frame from BGR to RGB (MediaPipe expects RGB input).
3. Run MediaPipe's Pose model to extract 33 body landmarks.
4. Draw the skeleton and landmarks on the frame.
5. Calculate angles between specific joint triplets (e.g. shoulder–elbow–wrist).
6. Compare those angles against expected ranges for each supported pose to assign a label.
7. Display or overlay the result.

## Evaluation

The system is evaluated informally based on:

- **Detection accuracy** — how well the predicted keypoints align with actual body parts
- **Classification accuracy** — whether the correct pose label is assigned
- **Processing speed** — frames per second achievable in the real-time demo
- **Robustness** — behavior under varying lighting and partial occlusion

## Limitations

- The angle-based classifier only recognizes the three poses it was explicitly tuned for; anything else is labeled "Unknown Pose."
- Heuristic thresholds were tuned by hand and may need adjusting for different camera angles, body proportions, or clothing.
- Real-time performance depends on hardware; higher `model_complexity` values trade speed for accuracy.

## Future Improvements

- Replace the hand-tuned angle thresholds with a trained classifier (e.g. an MLP) to support more poses and improve robustness.
- Improve performance on low-resolution or low-light input.
- Add repetition counting for fitness-tracking use cases.
- Package as a web or mobile app.

