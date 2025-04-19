# MAPL VRTIGO README

**[Overview Video and Quick Tutorial](https://youtu.be/als9t3aj5yU)**


## What the Project Does

Sudden-onset dizziness or vertigo is one of the most challenging stroke symptoms to diagnose, as only a small fraction of the nearly 4% of Emergency Department visits for new-onset vertigo are stroke-related. Current diagnostic approaches depend on clinical expertise and costly imaging, leading to frequent misdiagnoses, particularly in settings lacking neurological specialists. To address this, the MAPL lab developed a VR-based assessment system (VRITGO) that uses immersive technology with eye and head tracking to objectively distinguish between central (stroke-related) and peripheral causes of vertigo. This scalable, low-cost tool aims to improve stroke diagnosis in emergency settings, even when neurologists are unavailable. In addition to building out 5 core diagnostic tests, VRTIGO has a control panel for the physcian for live monitoring. All data is written and saved as text files for further analysis.

## What You Need
- Unity Game Engine and core files - where VRTIGO is stored and running from
- Meta Quest Pro (MQP) - critical to run the program and record data
- Laptop or PC - should be powerful enough/ have a good graphics card to run the Unity programs and connect to Meta Link. Helpful to be connected to power.
- Development Cord - to connect the MQP to the Laptop

## Core Diagnostic Tasks
1. **Bucket Test**
This virtual replication of the traditional "bucket test" challenges participants to rotate a line until it appears perfectly vertical, without any contextual visual cues. The test provides insight into the participant's subjective visual vertical (SVV), often disrupted in cases of vestibular dysfunction or brainstem stroke.

2. **Nystagmus Detection**
A moving visual target guides participants' gaze while the system uses built-in eye tracking to detect signs of nystagmus—rapid, involuntary eye movements that can indicate vestibular pathology or central neurological issues.

3. **Dysdiadochokinesia (Finger Tapping Test)**
In this motor coordination task, participants rapidly pinch their thumb and index finger together in quick succession. The VR system captures the timing and rhythm of these movements, which can highlight cerebellar dysfunction when irregular or slowed.

4. **Test of Skew**
Modeled after the clinical cover-uncover test, this task alternately occludes each eye to detect vertical misalignment (skew deviation). It helps reveal disconjugate gaze patterns that may point to central nervous system involvement.

5. **Head Stability Test**
Participants fixate on a stationary virtual point in space while the VR headset captures movements in head and trunk position. Subtle sway patterns are analyzed to assess postural stability and balance control—key indicators of vestibular function.

## Setting Up the Scene/Project
1. Open Meta Quest Link and connect your MQP to your PC. Ensure that it says that your MQP is connected; this is the only way in which you can stream the VRTIGO Unity file in real time.
2. In your headset, make sure you click on the notification called "USB Debugging" and then enter Quest Link via the settings. You should be in a white room after you click this.
3. In Meta Quest Link, go to settings. In the "General" Tab, ensure that the "OpenXR Runtime" is set so the meta quest link as act. Then in the "Beta" Tab, enable "Developer Runtime Features" and all the options below this. If you don't see this, then you might need to log in with a different account - it doesn't appear when I used my University of Rochester account.
4. Open Unity and the corresponding VRTIGO Files. Please use the same version of Unity as used in the original files - it should be version 2022.3.45f1
5. In your Unity project, open the StartMenu Scene in the project explorer. You will need to set up your screen where you can see 2 "Game" Displays. If you right click on the "Scene" or "Game" tab on the top of your screen, you can press "Add Tab" and then "Game".
6. You can drag your game tabs to be side by side. In your first game tab, there will be an option to change the "Display" (right under the tab). Ensure that one game tab says "Display 1" and the other says "Display 2". Display 1 is what the participant will see and this will adjust based on how they move their head with the headset. Display 2 is the Experimenter Control - you can control what tests to use and how data is collected. See **Controller Menu** for more info on this.
7. You should be able to click play right after this and run the system. If there are any issues with the VR system, try going to Edit > Project settings > Meta XR and check the Checklist for any issues (dont need to worry about the recommeded items). Please consult with the video at the start of this document for any questions.
8. Place the controllers and headset on the participant. On the the experiment side, enter the participant's name and click "Start/Next Step" to begin.
9. For Specific outlines of each tests, please see **Test Code**

---------------------------------------------------- **CODE/Technical Stuff** -----------------------------------------------------------

Just a headsup, I apologize for not commenting each code (was too focused on making it work).

## VR Systems

VRTIGO uses a combinations of plugins. Core plug-ins for VR set up: Meta XR All-in-One SDK and Occulus XR Plugin. If there are issues with VR setup, it may be worth looking into whether these are functional via Windows > Package Manager. You may need to update these packages, but keep an eye out if any methods are changed. In General, I reccomend not changing any code directly with the VR Rigs. Try to solve your issue using the inspector before touching the code, or at least save a backup.


All Scenes and tests have the "[BuildingBlock] Camera Rig" gameobject. This gameobject is different for every scene, based on that test's needs. For example, Finger Tapping will have hand tracking enabled, whereas Bucket Test will focus solely on controller user. You can edit the properties of this using the inspector tab. 


**Notable Exceptions**

- The Test of Skew uses a regular "XRRig". It doesn't necessarily integrate with Meta's OVR SDK, but we use a combination of Occulus's Rig with OVR's Eye gaze tracking. This allows us to cover each eye indepenently (something you can't do with just the OVR SDK by itself), but still record eye movement. We may need to look into this further to ensure that eye tracking is accurate using this method.

## Controller Menu

The controller menu was implemented so the experimenter can control the program without needing to take the headset off after every test. It also allows for specific and easy to use data collection and correction when needed. 
- Reload Scene: If you ever need to rerun a test or something breaks in the scene
- Disable/Show Tutorials: Some tests have a tutorial like the bucket test and test of nystagmus. You can turn off these tutorials after the patient has read through them.
- Recenter Player: Sometimes the screen is not right in front of the user. In this case, this button should help recenter the screen. If this doesn't work, you can also use the Meta button on your right hand controller to recenter while the headset is on the participant.
- Skip Test: Moves on to the next test, following the same order as outlined in **Core Diagnostic Tasks**
- Textbox/Participant Name: Enter the patient ID or participant's name. This is how the data will be stored on the PC after each test.
- Start/Next Step: Starts the test and progresses through the system.
- Running Indicator: Shows when the test is working and in progress
- Recording Indicator: Shows when the test is also recording data. Some tests have trial periods where data is not recorded, this helps distinguish these trials.

## Controller Menu Code and Core Game Loop Code

In your Heirarchy, you can find the Controller Menu gameobject as "ControllerScreen. Each of the buttons inside this game object has its own code associated with its function. The core code that controls the progrssion of the test is in the "Start" button and called "StartSystem.cs". 

**StartSystem.cs**

In the method "Start()" we record the player name as written in and saves it so that it is consistent while switiching scenes for each tests


In the Method "StartProcedure()" you can see the general game loop which progresses from startmenu > buckettest > TestofNystagmus > FingerTapping > TestofSkew > HeadStability. In each of these If statements, it follows a "phase" value of Tutorial, Round 1 (up to Round 3), and then Final. Please see **Test Code** for specific outlines on each tests. In each phase, we find a gameobject in the scene called "SceneCode". This is the life for each test, and is different for each scene. It basically works by Disabling and Enabling the gameobject every phase so that the code runs multiple times.


In the method "DelaySceneCodeChange()" we change the phase value from tutorial to the different round values.

## Test Code

### BucketTest
The bucket test involves placing a bucket with a straight line inside over a person's head and asking them to align the line with what they perceive as vertical. This test helps assess spatial perception and can reveal signs of vertigo or vestibular dysfunction by detecting a person's perceived vertical tilt. In stroke analysis, abnormal results may indicate damage to brain regions responsible for balance and spatial orientation, helping differentiate between peripheral and central causes of dizziness.


Sequence of Events (as written in StartSystem.cs): Test Phase (Data is not recorded), Phase 1, 2, and 3 (data is recorded). 


Circular UI Rotation
The circularUI GameObject represents a rotatable visual reference (the line in the middle of the bucket). The user can adjust its Z-axis rotation using the right thumbstick X-axis:

float joystickInput = OVRInput.Get(OVRInput.Axis2D.PrimaryThumbstick, OVRInput.Controller.RTouch).x;
circularUI.transform.Rotate(0f, 0f, -joystickInput * rotationSpeed * Time.deltaTime);


Adjust the speed of this rotation using the rotationSpeed variable, e.g.:
public float rotationSpeed = 50f; // Speed of rotation adjustment


Random Start Orientation: When enabled, the circularUI begins with a random Z rotation between 0° and 360°, simulating perceptual disorientation:
float randomZRotation = Random.Range(0f, 360f);
circularUI.transform.rotation = Quaternion.Euler(0f, 0f, randomZRotation);


When startMenu.recording is true, the script logs data to a session-specific folder:
path = Path.Combine(Application.persistentDataPath, "BucketTest", StartSystem.playerName, timestamp);
Files:
HeadPosition.txt – Logs X, Y, Z position per frame
HeadRotation.txt – Logs Euler angles of head rotation
BucketAngle.txt – Logs the circular UI’s normalized Z rotation (angle difference)

File logging is handled in Update():
File.AppendAllText(headposfile, headsetPosition.x + ", " + headsetPosition.y + ", " + headsetPosition.z + "\n");


Angle Normalization: To simulate subjective alignment with vertical, the script normalizes the Z-rotation of circularUI into a 0–90° range:
Angle.text = "Angle Difference: " + normalizedAngle.ToString("F2") + "°";
This helps maintain consistent evaluation of perceptual accuracy across all trials.





## Maintainers and Contributors

This project is co-created by **Joshua Jones '25** and the MAPL Labs team at [University of Rochester](https://www.rochester.edu/). All content belongs to [MAPL labs](https://www.urmc.rochester.edu/labs/movement-and-plasticity-lab).

---

