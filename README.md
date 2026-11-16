# CCTV Fall Detection System

A Python-based desktop application for detecting people and possible falls in prerecorded CCTV footage. The application supports multiple camera feeds arranged in a single video grid, person tracking, incident recording, snapshot capture, and optional email notifications.

> **Disclaimer:** This is an experimental academic/demo project, not a certified industrial safety or medical system. The current fall detection method uses bounding-box proportions and can produce false positives and false negatives. All alerts require human verification.

---

## 1. Features

- Process prerecorded MP4, AVI, MOV and MKV videos.
- Support multiple CCTV camera regions in a single video.
- Detect people using YOLOv8.
- Track multiple people independently in each camera.
- Maintain tracking boxes during brief detection failures.
- Detect possible falls using temporal bounding-box analysis.
- Save incident information in an SQLite database.
- Capture snapshots of detected incidents.
- Send automatic email notifications with snapshots attached.
- Desktop interface with video playback, pause, stop and speed controls.

## 2. Technology Stack

| Technology | Purpose |
| --- | --- |
| Python | Main programming language |
| OpenCV | Video processing and image operations |
| Ultralytics YOLOv8 | Person detection |
| PySide6 | Desktop graphical interface |
| NumPy | Numerical operations |
| SQLite | Incident database |
| SMTP / Gmail | Email notifications |
| PyYAML | Configuration management |

## 3. Project Structure

```text
iotFallDetection/
│
├── main.py
├── vision.py
├── tracking.py
├── alerts.py
├── config.yaml
├── requirements.txt
├── .env
├── .gitignore
│
├── models/
│   └── yolov8s.pt
│
└── incidents/
    ├── incidents.db
    └── snapshots/
```

**File descriptions:**

- `main.py`: Launches the desktop application and manages video playback.
- `vision.py`: Handles person detection, camera processing and fall analysis.
- `tracking.py`: Maintains person IDs using Intersection over Union (IoU).
- `alerts.py`: Records incidents, saves snapshots and sends email notifications.
- `config.yaml`: Stores model, tracking, camera and alert settings.
- `.env`: Stores private email credentials.
- `models/`: Contains YOLO model weights.
- `incidents/`: Stores the database and generated incident snapshots.

The `incidents` directory is created automatically when the application runs.

## 4. Requirements

- Python 3.9 or later.
- Windows, macOS or Linux.
- A computer capable of running YOLO inference.
- YOLOv8 model weights.
- A prerecorded CCTV video.
- A Gmail account with 2-Step Verification enabled if email notifications are required.

GPU acceleration is not mandatory. CPU inference is supported, although processing speed depends on the hardware and video resolution.

## 5. Installation

### Windows

Open PowerShell in the project directory.

**Step 1: Create a virtual environment**

```powershell
python -m venv venv
```

**Step 2: Activate the environment**

```powershell
.\venv\Scripts\Activate.ps1
```

If PowerShell prevents activation, use Command Prompt:

```bat
venv\Scripts\activate.bat
```

**Step 3: Install dependencies**

```powershell
python -m pip install --upgrade pip
pip install -r requirements.txt
```

**Step 4: Add the YOLO model**

Place `yolov8s.pt` inside the `models` directory:

```text
models/yolov8s.pt
```

**Step 5: Configure the application**

Review `config.yaml` and configure email notifications if required.

### macOS and Linux

Create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
```

Install dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Place the YOLO model in `models/yolov8s.pt` and configure the application.

## 6. YOLO Model Setup

The project uses the YOLOv8 Small model (`yolov8s.pt`) for person detection.

Place the weights file here:

```text
models/yolov8s.pt
```

The model is used to identify people within each configured camera region.

You can experiment with other compatible YOLO models by changing the model path in `config.yaml`. Smaller models may offer better processing speed but can have different detection accuracy.

Before redistributing model weights, review their applicable licensing terms.

## 7. Camera Grid Configuration

The default configuration assumes a 2×2 CCTV grid.

In `config.yaml`:

```yaml
grid:
  cameras:
    - name: Camera 1
      x1: 0.0
      y1: 0.0
      x2: 0.5
      y2: 0.5

    - name: Camera 2
      x1: 0.5
      y1: 0.0
      x2: 1.0
      y2: 0.5

    - name: Camera 3
      x1: 0.0
      y1: 0.5
      x2: 0.5
      y2: 1.0

    - name: Camera 4
      x1: 0.5
      y1: 0.5
      x2: 1.0
      y2: 1.0
```

Camera coordinates are normalized:

- `x1`, `y1`: Top-left coordinates.
- `x2`, `y2`: Bottom-right coordinates.
- `0.0`: Beginning of the frame.
- `1.0`: End of the frame.

For a video containing only one camera, use:

```yaml
grid:
  cameras:
    - name: Camera 1
      x1: 0.0
      y1: 0.0
      x2: 1.0
      y2: 1.0
```

Adjust the coordinates if the CCTV feeds have borders, gaps or other layouts. Camera boundaries are configured manually; they are not automatically detected.

## 8. Email Notification Setup

The application can automatically send an email whenever a possible fall is confirmed.

It uses Gmail's SMTP service to send notifications.

### 8.1 What is a Gmail App Password?

A Gmail App Password is a special 16-character password generated by Google for an application that needs to authenticate with a supported Google service.

Your Python application needs credentials to connect to Gmail's SMTP server and send email messages.

Google generally does not allow applications to authenticate using your normal Gmail account password in this way. Instead, an App Password provides a separate credential for the application.

An App Password:

- Is different from your normal Gmail password.
- Is generated through your Google Account.
- Can be revoked separately.
- Should be treated as a secret, just like your normal password.

### 8.2 Enable 2-Step Verification

1. Sign in to your Google Account.
2. Visit <https://myaccount.google.com/security>
3. Find **How you sign in to Google**.
4. Enable **2-Step Verification**, if it is not already enabled.

### 8.3 Generate an App Password

1. Open <https://myaccount.google.com/apppasswords>
2. Sign in again if requested.
3. Enter an application name, such as `CCTV Fall Detection`.
4. Click Create.
5. Google will generate a 16-character App Password.
6. Copy it and store it securely.

Google may not provide App Passwords for every account. Availability depends on the account type and security policies. If the option is unavailable, check Google's account security requirements or use an approved alternative email service.

### 8.4 Create the `.env` File

Create a file named `.env` in the root directory, alongside `main.py`.

Example:

```env
SMTP_USERNAME=your_email@gmail.com
SMTP_PASSWORD=your_16_character_app_password
```

Replace the example values with:

- `SMTP_USERNAME`: The Gmail address used to send notifications.
- `SMTP_PASSWORD`: The 16-character App Password generated by Google.

For example:

```env
SMTP_USERNAME=example@gmail.com
SMTP_PASSWORD=abcdefghijklmnop
```

Enter the App Password without spaces or quotation marks.

**Never use your normal Gmail password here.**

### 8.5 Configure Sender and Recipient

Open `config.yaml` and locate the email settings:

```yaml
alerts:
  email:
    enabled: true
    smtp_server: smtp.gmail.com
    smtp_port: 465
    sender: "your_email@gmail.com"
    recipient: "recipient@gmail.com"
```

There are three important email addresses/credentials to understand:

| Field | Meaning |
| --- | --- |
| `SMTP_USERNAME` | Gmail account used to authenticate |
| `sender` | Email address displayed as the sender |
| `recipient` | Email address receiving the alert |

The sender should normally match the authenticated Gmail account.

The sender and recipient can be the same or different.

**Example: Sending alerts to yourself**

`.env`:

```env
SMTP_USERNAME=example@gmail.com
SMTP_PASSWORD=abcdefghijklmnop
```

`config.yaml`:

```yaml
email:
  enabled: true
  smtp_server: smtp.gmail.com
  smtp_port: 465
  sender: "example@gmail.com"
  recipient: "example@gmail.com"
```

**Example: Sending alerts to another person**

```yaml
email:
  enabled: true
  smtp_server: smtp.gmail.com
  smtp_port: 465
  sender: "example@gmail.com"
  recipient: "receiver@gmail.com"
```

The sender still needs to authenticate using the credentials in `.env`.

### 8.6 Understanding SMTP Ports

The project uses Gmail's SMTP server:

```text
smtp.gmail.com
```

Two common connection methods are supported.

**Port 465: SMTP over SSL**

```yaml
smtp_server: smtp.gmail.com
smtp_port: 465
```

This establishes an encrypted connection from the beginning. The Python implementation uses `smtplib.SMTP_SSL`.

**Port 587: SMTP with STARTTLS**

```yaml
smtp_server: smtp.gmail.com
smtp_port: 587
```

This starts an SMTP connection and then upgrades it to an encrypted connection using STARTTLS.

The application uses `smtplib.SMTP` followed by `starttls()` for this port.

Do not mix these two connection methods.

### 8.7 Disable Email Notifications

If you only want to test fall detection without sending emails:

```yaml
email:
  enabled: false
```

Incident records and snapshots can still be generated.

### 8.8 Email Security

Never upload the `.env` file to GitHub or share it publicly.

If an App Password is exposed, revoke it through your Google Account and generate a new one.

Add the following to `.gitignore`:

```gitignore
venv/
.env
__pycache__/
*.pyc
incidents/
```

This prevents private credentials and generated incident data from accidentally entering version control.

## 9. Running the Application

Activate your virtual environment and execute:

```bash
python main.py
```

The desktop application will open.

Select a video file using **Open Video**.

Available controls:

| Control | Function |
| --- | --- |
| Open Video | Select a video file |
| Pause / Play | Pause or resume playback |
| Stop | Stop video processing |
| Speed | Change playback pacing |

## 10. Configuration Reference

Important options in `config.yaml`:

| Parameter | Description |
| --- | --- |
| `model.confidence` | Minimum YOLO detection confidence |
| `model.image_size` | YOLO inference resolution |
| `model.device` | Inference device selection |
| `video.loop` | Restart video after reaching the end |
| `process_every_n_frames` | Number of frames between inference runs |
| `tracking.iou_threshold` | Minimum IoU for associating detections |
| `tracking.max_missing_frames` | Allowed consecutive missing frames |
| `confirmation_frames` | Consecutive suspicious frames required for a fall alert |
| `horizontal_ratio` | Bounding-box width-to-height threshold |
| `recovery_frames` | Normal frames required to reset fall state |
| `cooldown_seconds` | Minimum interval between alerts for a person |
| `email.enabled` | Enable or disable email notifications |
| `debug.enabled` | Enable terminal debug output |

Changing these parameters can affect both processing speed and detection behavior. Test changes using representative videos.

## 11. Incident Storage

When an incident is detected, the application saves information in:

```text
incidents/
├── incidents.db
└── snapshots/
```

The SQLite database stores:

- Incident ID.
- Camera name.
- Person tracking ID.
- Timestamp.
- Snapshot path.

Snapshots are stored as JPEG images.

When email notifications are enabled, the saved snapshot is attached to the notification where available.

## 12. Troubleshooting

### Video is slow or laggy

YOLO inference can be computationally expensive, especially when processing several camera regions.

Possible adjustments:

- Reduce `model.image_size`.
- Use a smaller compatible YOLO model.
- Increase `process_every_n_frames`.
- Test with a lower-resolution video.

There is a trade-off between inference speed and detection coverage. Skipping frames can cause short events to be missed.

### Person bounding box disappears during a fall

YOLO may temporarily fail to recognize a person because of motion blur, occlusion or unusual body posture.

The tracker retains the last known box for a limited number of missing frames. However, this does not predict the person's actual position or establish that a fall occurred during the missing interval.

### Camera does not detect people

Check:

- Camera coordinates in `config.yaml`.
- Whether the person is visible within the configured region.
- YOLO confidence threshold.
- Whether the correct model weights are loaded.

### Gmail connection unexpectedly closes

A successful network port test only confirms that a TCP connection can be opened. It does not confirm that Gmail authentication or TLS negotiation will succeed.

Check:

1. `.env` is in the project root.
2. SMTP username is the correct Gmail address.
3. SMTP password is the generated App Password.
4. 2-Step Verification is enabled.
5. Port 465 uses SSL, or port 587 uses STARTTLS.
6. Firewall, VPN, antivirus or network policies are not interfering with SMTP.

The application prints the stage at which email sending failed, which can help identify the problem.

Never share your `.env` contents when requesting technical support.

### Missing Python module

Activate your virtual environment and run:

```bash
pip install -r requirements.txt
```

### YOLO model not found

Check that the model exists at:

```text
models/yolov8s.pt
```

Also verify that the path in `config.yaml` matches the actual file location.

## 13. Current Limitations

The current implementation is a prototype and has several limitations:

- Fall detection is based on bounding-box proportions rather than actual human pose estimation.
- Sitting, bending or lying down can produce false alerts.
- Some falls may not be detected.
- IoU tracking can lose or switch IDs when people overlap or move quickly.
- Camera regions require manual configuration.
- CPU-only inference may result in slow video playback.
- The last known bounding box is not a predicted person location.

## 14. Future Improvements

Possible improvements include:

- Human pose estimation using YOLO pose models.
- Temporal fall classification using pose sequences.
- More robust tracking using ByteTrack.
- Improved inference and video-display concurrency.
- Automatic CCTV grid calibration.
- Incident history and review interface.
- Unit testing and structured logging.

## 15. Privacy and Responsible Use

Use CCTV footage only when you have appropriate authorization and comply with applicable privacy and data-protection requirements.

Incident snapshots and videos may contain identifiable people. Store them securely and avoid publishing them without permission.

The application should be treated as an experimental detection aid. A human should review alerts before taking action.

---

**Project:** CCTV Fall Detection System  
**Language:** Python  
**Interface:** PySide6  
**Detection:** Ultralytics YOLOv8  
**Storage:** SQLite  
**Notifications:** Gmail SMTP
