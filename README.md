# REAL TIME OBJECT DETECTION AND ALERT SYSTEM
<div align="justify">
A powerful, real-time security system using YOLOv8, OpenCV, and Twilio/Email alerts, that automatically detects and monitors objects within a defined area, and alerts when they move outside the boundary.
</div>

## 📷 LIVE OBJECT DETECTION WITH SMART ALERTS 🚨
<div align="justify">
    This system enhances traditional surveillance by automating the detection of motion and sending SMS and Email alerts when any unauthorized object moves outside the specified zone in the webcam feed.
</div>


## KEY FEATURES


✅ Real-time object detection using YOLOv8

✅ User-defined boundary setup (draw using your mouse 🖱️)

✅ Sends SMS alert using Twilio

✅ Sends Email alert using SMTP

✅ Efficient for use in security zones, restricted areas, etc.


## INSTALLATIONS:
Ensure you have Python 3.8+ installed.
```
pip install opencv-python torch smtplib twilio ultralytics
```

## PROJECT STRUCTURE


<div style="font-family: monospace;">
real-time-alert-system/<br>
&nbsp;&nbsp;├── 📄 <b>main.py</b>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Main application<br>
&nbsp;&nbsp;├── 📄 <b>requirements.txt</b>&nbsp;&nbsp;# All dependencies<br>
&nbsp;&nbsp;├── 📄 <b>README.md</b>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Project documentation<br>
&nbsp;&nbsp;├── 📸 <b>sample.jpg</b>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Sample snapshot of detection<br>
</div>

## USAGE

**1.** Clone the repository:

```
git clone https://github.com/Janarthanan2/Real-Time-Object-Detection-and-Alert-System.git
```

**2.** Download or train a YOLOv8 model and rename it to model.pt
(You can use a pretrained model from Ultralytics)

**3.** Run the app:
```
python main.py
```

**4.** Draw a rectangle boundary on the video screen with your mouse.

**5.** If an object moves outside the rectangle, you will get:

   ✅ SMS alert (via Twilio)

   ✅ Email alert (via SMTP Gmail)



## SAMPLE CODE:
```py
# Load YOLOv8 model
model = YOLO('model.pt')

# Define boundary on video feed with mouse
cv2.setMouseCallback('Object Detection', mouse_callback)

# Check if detected object exits the defined area
if object_exits_boundary:
    send_sms_alert()
    send_email_alert()
```

## ALERT EXAMPLE:

SMS:
```
"Alert: Object moved out of the defined boundary!"
```

Email:
```
Subject: Alert: Object Moved Outside Boundary
Body: An object has moved out of the defined range!
```

