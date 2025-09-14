# Eye-Controlled Mouse (MediaPipe + OpenCV + PyAutoGUI)

This project implements a simple eye-controlled mouse using MediaPipe Face Mesh to track iris landmarks and simple blink detection to trigger clicks.

## What's included
- `eye_control_mouse.py` — the main Python script (run locally)
- `README_EyeControlledMouse.md` — this detailed explanation and usage guide

## Requirements
Install the required Python packages (tested on Python 3.8+):

```bash
pip install opencv-python mediapipe pyautogui numpy
```

Notes:
- On macOS you must allow the Terminal / Python app Accessibility permission to control the mouse and allow camera usage.
- If you run into `pyautogui` permission issues, follow platform-specific instructions (System Preferences → Security & Privacy on macOS).

## How it works (high-level)
1. **Face Mesh detection** using MediaPipe (with `refine_landmarks=True`), which provides precise facial landmarks including iris points.
2. **Iris center estimation**: average the 4 iris landmarks per eye (indices used: right iris 474–477, left iris 469–472). The script computes a screen coordinate from these normalized landmark positions and moves the cursor to that location.
3. **Smoothing**: a short buffer (deque) averages recent positions to reduce jitter.
4. **Blink detection**: a simple heuristic checks the vertical distance between upper and lower eyelid landmarks (right: 159 & 145, left: 386 & 374). When the normalized vertical distance falls below a threshold, it's considered a blink → triggers a click.
5. **Calibration (optional)**: the `--calibrate` mode records open/closed eye samples and computes a threshold for that user/lighting/distance, then saves it to `calibration.json` for future runs.

## Run
```bash
python eye_control_mouse.py
# or to calibrate first:
python eye_control_mouse.py --calibrate
```

## Tuning parameters to improve reliability
- `smoothing` (in code / saved config): increase to reduce jitter (at cost of responsiveness). Try 3–8.
- `blink_threshold`: default is `~0.02` (normalized). If you see false positives/negatives, re-run calibration or adjust manually (try 0.015–0.03).
- `click_cooldown`: time in seconds to prevent multiple clicks (default 0.5s). Increase if you get multiple clicks.

## Troubleshooting
- **No camera / wrong camera**: change `cv2.VideoCapture(0)` → try `1` or other indices.
- **Cursor jitter**: increase `smoothing` or resize the captured frame smaller (already set to 640×480).
- **Clicks happen too often**: increase `click_cooldown` or adjust `blink_threshold` with calibration.
- **No face detected**: ensure good lighting, camera is pointed at face, and distance is reasonable (not too close/far).
- **Permissions on macOS**: enable Camera and Accessibility for whichever app runs the script (Terminal, Python, IDE).

## Uploading to Google Drive & making it accessible (step-by-step)
1. Go to https://drive.google.com and sign in with your Google account.
2. Click **New → File upload** and select the `eye_control_mouse.py` (or the ZIP package).
3. Once uploaded, right-click the file in Drive and choose **Get link**.
4. Under "General access", change from "Restricted" to **Anyone with the link**.
5. Choose the role **Viewer** (or Editor if you want others to change the file).
6. Click **Copy link** and then **Done**. You can now share that link — anyone with it can access the file.

## Security & Privacy
- The script runs locally and uses your webcam; no frames are uploaded anywhere by this code.
- Be cautious when granting "Anyone with the link" access — only do this if you intend the file to be publicly accessible.

## Next improvements (advanced)
- Map gaze to screen more robustly (homography / calibration grid) for true gaze mapping.
- Use eye aspect ratio (EAR) for more robust blink detection and separate left/right eye click actions.
- Add dwell-click (hover for N seconds) or gesture-mode toggles to reduce accidental clicks.
- Use a Kalman filter for smoother cursor tracking.

## License / Attribution
Generated with assistance from ChatGPT (GPT-5 Thinking mini). Use at your own risk.
