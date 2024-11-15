# REAL TIME OBJECT DETECTION AND ALERT SYSTEM
# DESCRIPTION:
<div align="justify">
The demand for efficient and trustworthy security systems has become increasingly important in consumer and business environments. These systems are designed to enhance real-time monitoring and threat detection across various environments, such as public spaces, transportation hubs, and critical infrastructure. Traditional methods of surveillance rely on human beings monitoring the scene 24/7 which can be inefficient, resource intensive, and it is often difficult for someone to maintain constant attention.  To help address this project explains Real-Time Object Detection and Alert System which utilizes high-level object detection of motion activity to improve security. The system is designed to automatically detect an object and monitor the action within a defined area and alert when unauthorized movement is detected.
</div>

# INSTALLATIONS:
```
pip install opencv-python torch smtplib twilio ultralytics
```

# SAMPLE CODE:
```py
import cv2
import torch
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from twilio.rest import Client
from ultralytics import YOLO

# Load model
model = YOLO('model.pt')

# Alert functions
def send_sms_alert():
    account_sid = 'xxxe8a2ad0d3787e018ad2b944xxxxxxxx'
    auth_token = 'xxx2759270d0c1cd335db0c45dxxxxxx'
    client = Client(account_sid, auth_token)
    message = client.messages.create(
        body="Alert: Object moved out of the defined boundary!",
        from_='+15803664963',
        to='+918438373774'
    )
    print(f"SMS sent: {message.sid}")

def send_email_alert():
    sender_email = 'janartanan47@gmail.com'
    receiver_email = 'vigneshkumaran.2317@gmail.com'
    password = 'kjvcdxjxjixxxxxx'
    
    msg = MIMEMultipart()
    msg['From'] = sender_email
    msg['To'] = receiver_email
    msg['Subject'] = "Alert: Object Moved Outside Boundary"
    
    body = "An object has moved out of the defined range!"
    msg.attach(MIMEText(body, 'plain'))
    
    try:
        server = smtplib.SMTP('smtp.gmail.com', 587)
        server.starttls()
        server.login(sender_email, password)
        server.sendmail(sender_email, receiver_email, msg.as_string())
        print("Email sent successfully.")
    except Exception as e:
        print(f"Error sending email: {e}")
    finally:
        server.quit()

# Initialize webcam
cap = cv2.VideoCapture(0)

# Initialize boundary coordinates
boundary_x1, boundary_y1, boundary_x2, boundary_y2 = 0, 0, 0, 0
drawing = False
start_point = None
inside_boundary_objects = set()

def draw_boundary(frame):
    if start_point:
        cv2.rectangle(frame, (boundary_x1, boundary_y1), (boundary_x2, boundary_y2), (0, 255, 0), 2)

def mouse_callback(event, x, y, flags, param):
    global boundary_x1, boundary_y1, boundary_x2, boundary_y2, drawing, start_point
    
    if event == cv2.EVENT_LBUTTONDOWN:
        drawing = True
        start_point = (x, y)
        boundary_x1, boundary_y1 = x, y
    elif event == cv2.EVENT_MOUSEMOVE:
        if drawing:
            boundary_x2, boundary_y2 = x, y
    elif event == cv2.EVENT_LBUTTONUP:
        drawing = False
        boundary_x2, boundary_y2 = x, y
        boundary_x1, boundary_x2 = min(start_point[0], boundary_x2), max(start_point[0], boundary_x2)
        boundary_y1, boundary_y2 = min(start_point[1], boundary_y2), max(start_point[1], boundary_y2)

# Set mouse callback
cv2.namedWindow('Object Detection')
cv2.setMouseCallback('Object Detection', mouse_callback)

alert_triggered = False

while True:
    ret, frame = cap.read()
    if not ret:
        print("Failed to capture frame. Exiting...")
        break

    # Run object detection
    results = model(frame)
    current_frame_objects = set()  # Track objects detected in the current frame within boundary

    # Process detected objects
    for result in results:
        boxes = result.boxes
        for box in boxes:
            x1, y1, x2, y2 = box.xyxy[0]
            conf = box.conf[0]
            cls = int(box.cls[0])
            
            # Check if the detected box is within the boundary
            if (boundary_x1 <= x1 <= boundary_x2 and boundary_y1 <= y1 <= boundary_y2 and
                boundary_x1 <= x2 <= boundary_x2 and boundary_y1 <= y2 <= boundary_y2):
                
                # Draw bounding box and label for detected objects within boundary
                label = f'{model.names[cls]} {conf:.2f}'
                cv2.rectangle(frame, (int(x1), int(y1)), (int(x2), int(y2)), (255, 0, 0), 2)
                cv2.putText(frame, label, (int(x1), int(y1) - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 2)

                # Add object ID to the current frame's set of boundary objects
                current_frame_objects.add(cls)

    # Check for objects that were inside but are now outside
    for obj_id in inside_boundary_objects:
        if obj_id not in current_frame_objects:
            if not alert_triggered:
                send_sms_alert()
                send_email_alert()
                alert_triggered = True  # Only alert once until object returns

    # Update the set of objects inside the boundary
    inside_boundary_objects = current_frame_objects

    # Draw the boundary rectangle
    draw_boundary(frame)

    # Show video feed
    cv2.imshow('Object Detection', frame)

    if cv2.waitKey(30) & 0xFF == ord('q'):
        break

# Release resources
cap.release()
cv2.destroyAllWindows()
```


