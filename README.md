##  Sign Language Lecture Evaluation System From (bad-excellent) 



[project:] 
This project leverages computer vision to analyze sign language gestures in real-time, providing feedback on the quality of a lecture presentation. It uses MediaPipe for hand tracking and recognition, allowing it to identify specific signs that correspond to different levels of lecture quality. 


###  Libraries

* **[mediapipe]:** 
* **[os]:** 
* **[open cv]:** 
    


##  Example

[Provide a clear explanation of how the system works. You could use a bullet point list for the sign language gestures and their corresponding feedback levels.]

**Sign Language:**

* **[Sign 1]:** [Description of the sign, including hand configuration and movement.] - **Feedback:** [Level of feedback]
* **[Sign 2]:** [Description of the sign, including hand configuration and movement.] - **Feedback:** [Level of feedback]
* **[Sign 3]:** [Description of the sign, including hand configuration and movement.] - **Feedback:** [Level of feedback]
* **[Sign 4]:** [Description of the sign, including hand configuration and movement.] - **Feedback:** [Level of feedback]


##  Code Structure

img = cv2.flip(img, 1)  # Flip horizontally for mirrored view

imgRGB = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
results = hands.process(imgRGB)

lmList = []
if results.multi_hand_landmarks:
    for handLms in results.multi_hand_landmarks:
        for id, lm in enumerate(handLms.landmark):
            h, w, c = img.shape
            cx, cy = int(lm.x * w), int(lm.y * h)
            lmList.append([id, cx, cy])
        mpDraw.draw_landmarks(img, handLms, mpHands.HAND_CONNECTIONS)

        if len(lmList) == 21:
            fingers = []

            # Thumb logic (different from other fingers)
            if lmList[tipIds[0]][1] > lmList[tipIds[0] - 1][1]:  # Thumb extended
                fingers.append(1)
            else:
                fingers.append(0)

            # Check for extended fingers (index to pinky)
            for tip in range(1, 5):
                if lmList[tipIds[tip]][2] < lmList[tipIds[tip] - 2][2]:
                    fingers.append(1)
                else:
                    fingers.append(0)

            totalFingers = fingers.count(1)
            print(f"Fingers: {totalFingers}")

            # Define feedback based on finger count
            feedback = ""
            if totalFingers == 1:
                feedback = "Bad"
            elif totalFingers == 2:
                feedback = "Not bad"
            elif totalFingers == 3:
                feedback = "Okay"
            elif totalFingers == 4:
                feedback = "Good"
            elif totalFingers == 5:
                feedback = "Perfect"

            # Display the feedback
            cv2.putText(img, feedback, (40, 80), cv2.FONT_HERSHEY_SIMPLEX,
                        1.5, (0, 0, 255), 3)

# Save screenshots every 10 frames
if frame_count % 10 == 0:
    screenshot_filename = f"{screenshots_folder}/screenshot_{frame_count}.jpg" 
    cv2.imwrite(screenshot_filename, img)
frame_count += 1

cv2.imshow('Hand Tracker', img)
if cv2.waitKey(5) & 0xFF == 27:
    break
    
  
###  Example

* **[Bad: Thumb extended, other fingers closed.]:** 
* **[Okay: Index and middle fingers extended, other fingers closed.]:** 
* **[Good: All fingers extended except thumb]:**
* **[Excellent: All fingers extended]:**


### Output:
The system will display the corresponding feedback level (e.g., "Bad," "Good") based on the recognized sign gesture.
