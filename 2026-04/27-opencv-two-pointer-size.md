# OpenCV: Introduction & Two-Finger Size Detector
> 📅 2026-04-27

---

*Dear diary,*

*I wanted to try computer vision. Not because of a course, not because someone showed me something cool, just because I wanted to know if I could make my laptop see. That felt like enough of a reason. I picked OpenCV, got it running, pointed my webcam at my hands, and spent a while just understanding what was happening at the pixel level. Then I built something concrete: a tool that detects your two index fingers and measures the distance between them in pixels. It works. I ran it, held up both fingers, and watched a number on screen update in real time as I moved my hands apart. Genuinely one of the more satisfying moments I've had at a keyboard.*

---

## Overview

Two parts to this session. First: getting familiar with OpenCV and computer vision concepts from scratch, no prior knowledge, just curiosity. Second: building a working tool that uses hand tracking to detect both index fingertips and measure the pixel distance between them in real time.

## Part One: Introduction to OpenCV

### What OpenCV is

OpenCV (Open Source Computer Vision Library) is a Python-accessible library for real-time image and video processing. It handles everything from reading webcam frames to detecting objects, faces, and, with the right extensions, hands.

### Core concepts covered

**Frames and the video loop** - A webcam feed is just a sequence of image frames read in a loop. Every iteration of the loop captures a new frame, processes it, and renders it to screen. The speed of that loop determines how "live" the output feels.

**Colour spaces** - OpenCV reads images in BGR by default, not RGB. This matters immediately when anything looks wrong colour-wise. Converting between BGR, RGB, and HSV (hue-saturation-value) is routine.

**MediaPipe** - Used alongside OpenCV for hand tracking. MediaPipe provides a pre-trained hand landmark model that identifies 21 keypoints per hand - joints, knuckles, fingertips - without needing to train anything from scratch.

**Landmark indexing** - Each of the 21 hand landmarks has a fixed index. The index fingertip is landmark `8`. Knowing this is the whole key to the second part.

### Setup

```bash
pip install opencv-python mediapipe
```

Basic webcam loop:

```python
import cv2

cap = cv2.VideoCapture(0)

while True:
    ret, frame = cap.read()
    cv2.imshow("feed", frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

---

## Part Two - Two-Finger Pixel Size Detector

### What it does

Detects both hands via webcam, isolates the index fingertip (landmark `8`) on each, and calculates the Euclidean pixel distance between them in real time. The measurement updates live on screen as you move your hands.

### How it works

1. Each frame is flipped horizontally (mirror mode, more natural for the user)
2. MediaPipe processes the frame and returns hand landmarks if hands are detected
3. For each detected hand, landmark `8` coordinates are extracted
4. Once both hands are detected, the pixel distance between the two fingertips is calculated
5. A line is drawn between the two points, and the distance value is rendered on screen

### Core code

```python
import cv2
import mediapipe as mp
import math

mp_hands = mp.solutions.hands
hands = mp_hands.Hands(max_num_hands=2)
mp_draw = mp.solutions.drawing_utils

cap = cv2.VideoCapture(0)

while True:
    ret, frame = cap.read()
    frame = cv2.flip(frame, 1)
    rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    result = hands.process(rgb)

    fingertips = []

    if result.multi_hand_landmarks:
        for hand_landmarks in result.multi_hand_landmarks:
            mp_draw.draw_landmarks(frame, hand_landmarks, mp_hands.HAND_CONNECTIONS)
            h, w, _ = frame.shape
            tip = hand_landmarks.landmark[8]
            cx, cy = int(tip.x * w), int(tip.y * h)
            fingertips.append((cx, cy))

    if len(fingertips) == 2:
        x1, y1 = fingertips[0]
        x2, y2 = fingertips[1]
        distance = math.sqrt((x2 - x1)**2 + (y2 - y1)**2)
        cv2.line(frame, (x1, y1), (x2, y2), (255, 255, 0), 2)
        cv2.putText(frame, f"{int(distance)}px", ((x1 + x2) // 2, (y1 + y2) // 2),
                    cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 0), 2)

    cv2.imshow("Two Finger Distance", frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
```

### Result

Fully working. Both fingertips detected and tracked across movement. Distance updates in real time. Line renders between the two points. Holding fingers close together reads low; spreading them apart increases the count visibly and immediately.

## Issues Encountered

- BGR vs RGB caught me off once, colours looked wrong until the `cvtColor` conversion was in place
- MediaPipe requires the RGB frame for processing even though OpenCV captures in BGR; easy fix, easy to miss
- `max_num_hands=2` has to be set explicitly, defaults to one hand only
- Landmark coordinates from MediaPipe are normalised (0 to 1), not pixel values - multiplying by frame width/height is required to get actual coordinates

## Current Limitations

- Pixel distance only - no real-world unit conversion yet (would need a calibration reference object)
- Lighting sensitive - low light degrades landmark detection noticeably
- Occasionally picks up the wrong fingertip if a hand is partially out of frame

## Next Steps

- Calibrate against a known object size to convert px distance to centimetres
- Extend to all five fingertips per hand - could track full hand spread
- Gesture detection - pinch, point, open palm as discrete states
- Explore face landmark detection as the next MediaPipe model to try

## Reflection

Computer vision felt intimidating before this. It turns out the entry point is just: run a loop, read a frame, find a number, draw something on screen. MediaPipe does the hard part; the hand model is already trained and already works. What this session actually taught me is that most of the interesting work in computer vision is knowing *which landmark to look at and what to do with it*. The maths, once you're down to pixel coordinates, is just Pythagoras.
