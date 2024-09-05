# Person-Detection-and-Tracking-using-YOLOv8-and-DeepSORT
This project implements a person detection and tracking system using YOLOv8 (You Only Look Once version 8) and DeepSORT (Deep Simple Online and Realtime Tracking). The code is designed to analyze a video, detect persons within each frame, and track them over time. It assigns labels (such as "Child" and "Therapist") based on certain heuristics to identify roles in specific contexts, like therapy sessions.

**Table of Contents**
Overview
Logic Behind Analyzing Model Predictions
1. YOLOv8 Model for Person Detection
2. DeepSORT Tracker for Tracking Persons
3. Label Assignment
Dependencies
Running the Code
**Overview**
The system uses the YOLOv8 object detection model to detect persons in each video frame and DeepSORT to track those detected persons across frames. The program labels each tracked person as either a "Child" or a "Therapist" based on a heuristic (using the track ID).

**Logic Behind Analyzing Model Predictions**
**1. YOLOv8 Model for Person Detection**
The YOLOv8 model is used as the primary tool for detecting persons in each video frame. YOLO is an efficient, real-time object detection model known for its speed and accuracy. The specific variant used (yolov8n.pt) is chosen for its balance between speed and performance, suitable for processing videos in real time.

Prediction Step:
The model.predict() function is called with the frame as input. It returns predictions including bounding boxes, confidence scores, and class IDs for the detected objects. The confidence threshold is set to 0.4, ensuring that only reliable detections are considered.

Bounding Boxes:
Bounding boxes represent the location of detected persons in the frame. These coordinates are used to draw rectangles around detected persons and provide the data for further processing by the tracker.

Filtering Detections:
The detections are filtered by class to ensure that only persons (Class ID 0) are processed. Each detection is stored as a tuple containing the bounding box, confidence score, and class ID.
**
**2. DeepSORT Tracker for Tracking Persons****
DeepSORT is used to maintain consistent tracking of detected persons across multiple frames. DeepSORT is a popular tracking algorithm that combines appearance features and motion information to assign unique track IDs to objects.

Detection Format:
Each detection from YOLOv8 is passed to DeepSORT in the format [bbox, confidence, class_id]. These detections are updated frame-by-frame, providing a continuous tracking output.

Track Updates:
The tracker uses Kalman filters and a deep appearance descriptor to maintain track continuity, even in the case of brief occlusions or when the objects overlap. Each track is assigned a unique ID, which remains consistent as long as the person is continuously tracked.

Handling Track IDs:
The assigned track IDs are utilized to differentiate between multiple persons detected in the same frame, allowing individual tracking and labeling.

**3. Label Assignment**
The tracked individuals are labeled either as "Child" or "Therapist" based on a simple heuristic using the track ID:

Heuristic:
The heuristic used here is label = "Child" if int(track_id) % 2 == 0 else "Therapist". This means that persons with even-numbered track IDs are labeled as "Child," while those with odd-numbered track IDs are labeled as "Therapist."

Purpose of Heuristic:
This simple approach helps differentiate between roles in a predefined scenario, such as an ABA therapy session where the goal might be to separately track the actions of the child and the therapist. 

 OR

** 3. Manual Assignment of Roles**
The assignment of roles ("Child" and "Therapist") is done manually based on the order of appearance:

Initial Assignment:
The first detected individual is assumed to be the "Child," and subsequent individuals are labeled as "Therapist." This is stored in the track_labels dictionary, which maps each track_id to its assigned role.
Logic Implementation:
**Assign roles based on the order of appearance**
if track_id not in track_labels:
    if len(track_labels) == 0:
        # Assume the first detected track ID is the Child
        track_labels[track_id] = "Child"
    else:
        # Assign remaining track IDs as Therapist
        track_labels[track_id] = "Therapist"
return track_labels[track_id]

Persistent Labeling:
This logic ensures that once a role is assigned to a track ID, it remains consistent throughout the video, avoiding mislabeling caused by ID changes or switching.
Use Case:
This manual approach is particularly useful in settings where the participants have clearly defined roles and can be initially identified based on context or observation.

**Dependencies**
The project requires the following dependencies:

opencv-python==4.8.0.74
torch==2.0.1
numpy==1.24.4
ultralytics==8.0.107
deep-sort-realtime==1.3.1
These can be installed using the provided requirements.txt file:
pip install -r requirements.txt

**Running the Code**
Ensure the necessary dependencies are installed.
Update the input_video_path and output_video_path with the correct paths to your input and output videos.
Run the script using your preferred Python environment:
process_video(input_video_path, output_video_path)
The output will be a video with tracked persons, each labeled appropriately as "Child" or "Therapist," saved to the specified pat
