# 2. Function Instruction

## 2.1 Color Recognition

### 2.1.1 Introduction to Color Recognition function

This feature can recognize objects of various colors within the image and highlight them with bounding boxes on the display. Through the I²C interface, users can obtain data such as the position and size of different color blocks in the image, enabling color recognition and tracking in different scenarios and supporting a wide range of extended applications.

### 2.1.2 Instructions

Please read and follow the instructions below carefully, as failure to do so may affect the performance of this feature!

1.  Use bright and high saturated color. Example red, green or blue. Targeted object color and its background must be distinctive. Example if target object is Yellow, avoid yellow background.

2.  Do not learn White color or Composite color. Ensure environment is bright enough or use Fill Light on WonderCam when required.

3.  Adjusting light condition and the focus angle can help to stabilize and improve the color recognition process in the white frame within the display panel.

### 2.1.3 Color Recognition Operations

* **Enter Function**

The default start-up function on WonderCam is Color Recognition. If not, push Navigation button on WonderCam to left or right to select to Color Recognition menu.

* **Learning new color and recognizing new color**

To use Color Recognition function, program WonderCam to learn about the color first.

Steps are as follows

1)  When in the Color Recognition Menu, push Function button to the left once to enter Learning mode.

<img class="common_img" src="../_static/media/section_4_2.1.1 Color Recognition\media\image2.jpeg" style="width:500px"  />

2)  In Learning mode, a red + sign will be shown in center of display with menu showing “**Color ID:1**”, “**Color ID:2**”.

3)  Focus red + on to the targeted colored object. When target is in focus, a white frame on screen will encapsulate the object.

4)  Push Function button to the left for WonderCam to Learn the color. Wait for Learning successful message display. Push Function button down to OK to complete. If Learning fail, try adjusting lighting condition or change to more distinctive color. Ensure the White frame is stabilized when in focus and push top right button to the left to Learn.

<img class="common_img" src="../_static/media/section_4_2.1.1 Color Recognition\media\image3.jpeg" style="width:500px"  />

5. When learning is successful and WonderCam detected corresponding color to the ID number it had learned previously, the ID number of the object will be shown on display.

   <img class="common_img" src="../_static/media/section_4_2.1.1 Color Recognition\media\image4.jpeg" style="width:500px"  />

* **Delete Color**

To delete an ID color, enter Color Recognition mode and move to the selected ID by using Navigation button. Select Color ID to delete and push Function button to the right towards Bin icon. Push Function button down to select OK to delete.

### 2.1.4 Color Recognition Parameter Settings

In the Color Recognition main menu, press Function button down to enter Settings interface.

<img class="common_img" src="../_static/media/section_4_2.1.1 Color Recognition\media\image5.jpeg" style="width:500px"  />

The settings include **Color Range** and **Reset Parameters**.

**1. Color Range** 
Controls the detection range during color learning. Setting an appropriate value ensures accurate identification of the target color and avoids selecting unrelated areas.

- Recommended value: **20** — generally provides optimal recognition results.

- If the value is too high: the white bounding box may cover the entire screen during learning.

- If the value is too low: only part of the target color may be enclosed, reducing recognition accuracy.

*Note:* The color range for a learned color ID is stored with that ID and is not affected by subsequent changes to the “**Color Range**” setting. To adjust the range for an existing color ID, relearning is required.

**2. Reset Parameters** 
Restores all color recognition parameters to factory settings. 
*Important:* After resetting, save the changes before exiting the settings interface; otherwise, the reset will not be applied.

## 2.2 Facial Recognition

### 2.2.1 Introduction to Facial Recognition Function

In facial recognition mode, the WonderCam Vision Module can detect faces in the image and determine whether they match a specific face. Through the I²C interface, external control devices can obtain additional data such as the position and size of the face in the image, enabling applications such as face detection, face recognition, and face tracking.

### 2.2.2 Instructions

Please read and follow the instructions below carefully, as failure to do so may affect the performance of this feature!

1.  To Learn Facial, point + to center of face for higher accuracy in learning.

2.  Use proper front facing Facial photograph for Face Template.

3.  Ensure environment is bright enough or use Fill Light on WonderCam when required.

### 2.2.3 Facial Recognition Operations

* **Enter Function**

Push Navigation button to Facial Recognition menu.

* **Face Detection**

Once WonderCam is in Facial Recognition mode, face detection will automatically be activated. When face is detected, a white frame will appear on screen to enclose the detected face.

* **Learning and Recognizing Face**

To recognize specific face, WonderCam need to learn to recognize the face.

**1. Learn Single Face**

1)  Push Function button to left to enter Learn mode.

When enter to Learn model, a “**+**”will appear in the middle of screen, menu will show “**Face ID:1**”, “**Face ID:2**“ etc. The ID is to tag and save different Faces.

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image3.jpeg" style="width:500px"  />

2)  Point on screen “**+**” to center of Face. Take note of the pointers given in Instructions.

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image4.jpeg" style="width:500px"  />

3)  When face is centered, push Function button to the left camera icon to learn the face. When learning is successful, push Function button down once to OK and complete the learning process. If the learning is successful, when camera detected the face, WonderCam will display the corresponding ID on screen.

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image5.jpeg" style="width:500px"  />

**2. Learn Multiple Faces**

To learn more faces, when in Learning mode, push Navigation button to the right to to other ID number and repeat earlier process.

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image6.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image7.jpeg" style="width:500px"  />

**3. Recognizing Faces**

When learning multiple faces is completed, Wondercam is able to display multiple

faces detected in same display frame with their corresponding ID.

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image8.jpeg" style="width:500px"  />

**4. Delete Face**

To delete Face ID that is no longer required. In Face Recognition Learning mode, push Navigation button to the Face ID number targeted to delete. Push Function button to the right toward Bin icon, push Function button down to OK to delete.

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image9.jpeg" style="width:500px"  />

### 2.2.4 Facial Recognition Parameter Settings

In Facial Recognition main menu, press Function button down toward Gear icon to enter Settings interface.

<img class="common_img" src="../_static/media/section_5_2.2 Facial Recognition\media\image10.jpeg" style="width:500px"  />

Available for configurations are: Detecting Threshold, NMS Threshold, Recognition Threshold and Reset.

1)  **Detecting Threshold:** To set threshold is recognizing face. Higher value will have higher accuracy rate. However, higher value will also resulted in lower face detection rate.

2)  **NMS Threshold:** Non- Maximum Suppression is a computer vision method. Leave it at default value.

3)  **Recognition Threshold:** Settings for specific Face ID. Higher value will have higher accuracy rate. However, higher value may result in unable to recognize or only recognize at specific part or angle of the face.

4)  **Reset:** This will erase all previous recordings and reset to factory default value. Do note to Save settings to effective the reset settings

## 2.3 Tag Recognition

### 2.3.1 Introduction to April Tag Recognition Function

Tag recognition function: Able to detect and recognize AprilTags (support only AprilTag family type TAG36H11) and framed the detected tag with ID on WonderCam display. Through IIC port, the tag position data and 3D dynamic data can be captured to position space orientation.

AprilTag is a visual fiducial system, while similar to the concept of QR Code or barcode, it can be more easily detected, more robustly identified, and less difficult to detect at longer ranges.

It is useful for a wide variety of tasks including augmented reality, robotics, and camera calibration. Tags can be printed from an ordinary printer, and the AprilTag detection software computes the precise 3D position, orientation, and identity of the tags relative to the camera. 

<img class="common_img" src="../_static/media/section_6_2.3 Tag Recognition\media\image2.png" style="width:500px" />

### 2.3.2 Instructions

1.  AprilTags are provided in folder name “**April Tags Collection**”, within are 200 different AprilTags for use.

2.  Please maintain suitable distance between Tag and WonderCam. Too far or too near distance to the tag can affect the recognition process.

3.  Ideal background surrounding the Tag is all white for higher accuracy in detection.

### 2.3.3 Tag Recognition Operation

* **Enter Function**

Push Navigation button to “**Tag Recognition**” menu.

* **Using AprilTag Recognition**

In Tag Recognition menu, when WonderCam camera focus on AprilTag TAG36H11 and appear on display, a + sign will appear on screen on the tag. WonderCam is able to detect multiple tags with same or different ID.

<img class="common_img" src="../_static/media/section_6_2.3 Tag Recognition\media\image3.png" style="width:500px" />

Please maintain suitable distance between Tag and WonderCam. Too far or too near distance to the tag can affect the recognition process.

## 2.4 Visual Line Following

### 2.4.1 Introduction to Visual Line Following function

Visual Line Following: Able to recognize and identifies multiple color lines and show each line’s direction. It provides data such as line position, deviation, direction angle etc to perform smooth visual line following program executions.

### 2.4.2 Instructions

1)  Use bright and high saturated color for Line. Background of Line color must be distinctive from the Line color. Example if Line color is black, do not use it on dark background color like gray or black.

2)  Do not Learn White color or Composite color. Ensure lighting condition is bright enough or use Fill Light on WonderCam if required.

3)  Depending on actual height of WonderCam to ground, Line width should be suitable in size. Ensure the white arrow on screen is stable.

### 2.4.3 Visual Line Following Operations

* **Enter Function**

Push Navigation button on WonderCam to the right to select to Visual Line Following menu.

* **Learning new color and recognizing new color**

To use Visual Line Following function, program WonderCam to learn about the color first. The method is similar as Color Recognition.

Steps are as follows:

Step 1: When in the Visual Line Following Menu, push Function button to the left once to enter Learning mode. In Learning mode, a red + sign will be shown in center of display with menu showing “**Line ID:1**”, “**Line ID:2**” etc. This to tag and save various line color.

<img class="common_img" src="../_static/media/section_7_2.4 Visual Line Following\media\image2.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_7_2.4 Visual Line Following\media\image3.jpeg" style="width:500px"  />

Step 2: Focus red + on to the targeted line. Ensure target Line to be as straight as possible either in vertical or horizontal direction.

Note: Use bright and high saturated color for Line and background is a clear contrast to the Line color. Ensure lighting condition is bright enough or use Fill Light on WonderCam if required.

Step 3: Adjust the lighting and focus angle to stabilize the arrow line. Push Function button left toward the Camera icon to learn and wait for success message. If fail, turn on the WonderCam light or change the target line color to more contrasting color and do the Learning process again.

<img class="common_img" src="../_static/media/section_7_2.4 Visual Line Following\media\image4.jpeg" style="width:500px"  />

Once learning is successful, WonderCam will identifies the color line it had learn and presented as an arrow line on screen.

<img class="common_img" src="../_static/media/section_7_2.4 Visual Line Following\media\image5.jpeg" style="width:500px" />

* **Delete Line**

To delete any Line ID, navigate to Visual Line Following menu and push Function button to the left to enter Learn function mode. When in Learn mode, use Navigation button to push to the ID number to delete and push down to Bin icon to delete.

### 2.4.4 Visual Line Following Settings

In Visual Line Following menu, push Function button down towards Gear icon to access Settings.

<img class="common_img" src="../_static/media/section_7_2.4 Visual Line Following\media\image6.jpeg" style="width:500px"  />

Available for configurations are Color Range and Reset function.

**Color Range:** Used to control the recognition range during color learning. Proper settings help accurately identify the target color and avoid selecting unrelated areas.

**Reset:** This will erase all previous recordings and reset to factory default value. Do note to Save settings to effective the reset settings.

## 2.5 Number Recognition

### 2.5.1 Introduction to Number Recognition Function

Using the preprogrammed number recognition models to recognize the picture and present

the corresponding number on display.

> [!NOTE]
>
> **Note:** Use the number template provided.

### 2.5.2 Instructions

Distance from Card to WonderCam camera should be between 15cm to 20cm. Ensure

sufficient lighting in the surrounding and use white background for better recognition results.

### 2.5.3 Number Recognition Operations

* **Enter Function**

Push Navigation button to Number Recognition menu.

* **Recognizing Number**

When in Number Recognition mode and using the number templates provided, WonderCam

is able to recognized the number and present the corresponding time on its display. When in operation, a value will also be shown to signify the trust value. The highest value is 1.0 being closest matched.

> [!NOTE]
>
> **Note:** Distance from Card to WonderCam camera should be between 15cm to 20cm. Ensure sufficient lighting in the surrounding and use white background for better recognition results.

<img class="common_img" src="../_static/media/section_8_2.5 Number Recognition/media/image2.png" style="width:500px" />

<img class="common_img" src="../_static/media/section_8_2.5 Number Recognition/media/image3.png" style="width:500px" />

|                             Card                             | number |                             Card                             | number |
| :----------------------------------------------------------: | :----: | :----------------------------------------------------------: | :----: |
| <img class="common_img" src="../_static/media/section_8_2.5 Number Recognition/media/image4.jpeg" style="width:200px" /> |   1    | <img class="common_img" src="../_static/media/section_8_2.5 Number Recognition/media/image5.jpeg" style="width:200px" /> |   2    |
| <img class="common_img" src="../_static/media/section_8_2.5 Number Recognition/media/image6.jpeg" style="width:200px" /> |   3    | <img class="common_img" src="../_static/media/section_8_2.5 Number Recognition/media/image7.jpeg" style="width:200px" /> |   4    |
| <img class="common_img" src="../_static/media/section_8_2.5 Number Recognition/media/image8.jpeg" style="width:200px" /> |   5    |                                                              |        |

## 2.6 Landmark Recognition

### 2.6.1 Introduction to Number Recognition Function

Using the programmed Landmark recognition model to recognize the picture

and present the corresponding ID number on display.

### 2.6.2 Instructions

Distance from Card to WonderCam camera should be between 15cm to 20cm.

Ensure sufficient lighting in the surrounding and use white background for

better recognition results.

> [!NOTE]
>
> **Note: Use the number template provided.**

### 2.6.3 Number Recognition Operations

* **Enter Function**

Push Navigation button to Landmark Recognition menu.

* **Recognizing Landmark**

When in Landmark Recognition mode and using the templates provided,

WonderCam is able to recognized the mark and present the corresponding ID

number on its display. When in operation, a value will also be shown to signify

the trust value. The highest value is 1.0 being closest matched.

<img class="common_img" src="../_static/media/section_9_2.6 Landmark Recognition\media\image2.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_9_2.6 Landmark Recognition\media\image3.jpeg" style="width:500px"  />

Distance from Card to WonderCam camera should be between 15cm to 20cm. Ensure sufficient lighting in the surrounding and use white background for better recognition results.

|                             Card                             | number |                             Card                             | number |
| :----------------------------------------------------------: | :----: | :----------------------------------------------------------: | :----: |
| <img class="common_img" src="../_static/media/section_9_2.6 Landmark Recognition\media\image4.jpeg" style="width:200px" /> |   1    | <img class="common_img" src="../_static/media/section_9_2.6 Landmark Recognition\media\image5.jpeg" style="width:200px" /> |   2    |
| <img class="common_img" src="../_static/media/section_9_2.6 Landmark Recognition\media\image6.jpeg" style="width:200px" /> |   3    | <img class="common_img" src="../_static/media/section_9_2.6 Landmark Recognition\media\image7.jpeg" style="width:200px" /> |   4    |
| <img class="common_img" src="../_static/media/section_9_2.6 Landmark Recognition\media\image8.jpeg" style="width:200px" /> |   5    |                                                              |        |

## 2.7 Image Classification

### 2.7.1 Introduction to Image Classification Function

Using the programmed Image Classification model to recognize and classify pictures. This function can be used to classify garbage and etc.

> [!NOTE]
>
> Note: WonderCam module has a built-in 12 card sorting model at factory default setting, which can be used with the waste template provided.

### 2.7.2 Instructions

Distance from Card to WonderCam camera should be between 15cm to 20cm. Ensure sufficient lighting in the surrounding and use white background for better recognition results.

### 2.7.3 Number Recognition Operations

* **Enter Function**

Push Navigation button to Image Classification menu.

* **Recognizing Landmark**

When in Image Classification mode and using the templates provided, WonderCam is able to recognized the mark and present the corresponding ID number on its display. When in operation, a value will also be shown to signify the trust value. The highest value is 1.0 being closest matched.

<img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image2.jpeg" style="width:500px"  />

<img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image3.jpeg" style="width:500px"  />

Distance from Card to WonderCam camera should be between 15cm to 20cm. Ensure sufficient lighting in the surrounding and use white background for better recognition results.

|                           **Card**                           | **ID** |                           **Card**                           | **ID** |
| :----------------------------------------------------------: | :----: | :----------------------------------------------------------: | :----: |
|                       White background                       |   1    | <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image4.png" style="width:200px" /> |   2    |
| <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image5.png" style="width:200px" /> |   3    | <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image6.png" style="width:200px" /> |   4    |
| <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image7.png" style="width:200px" /> |   5    | <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image8.png" style="width:200px" /> |   6    |
| <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image9.png" style="width:200px" /> |   7    | <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image10.png" style="width:200px" /> |   8    |
| <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image11.png" style="width:200px" /> |   9    | <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image12.png" style="width:200px" /> |   10   |
| <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image13.png" style="width:200px" /> |   11   | <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image14.png" style="width:200px" /> |   12   |
| <img class="common_img" src="../_static/media/section_11_2.7 Image Classification/media/image15.png" style="width:200px" /> |   13   |                                                              |        |

## 2.8 Feature Learning

### 2.8.1 Introduction to Feature Learning

WonderCam can learn and record the features of an item and do comparison between real time image and the learned images. This function can be used to judge whether the two images are the same.

### 2.8.2 Number Recognition Operations

* **Enter Function**

Push Navigation button to Image Classification menu.

* **Recognizing Landmark**

In this function, WonderCam need to learn the image first. If the image detected had not been learned before, it will display value of “**ID:0 P:0.00**”.

**Step 1:** Push Function Button to the Left to enter the function. The ID number on the bottom are used to mark the learn features. A “+” sign will appear in middle of screen with value of “**ID:0 P:0.00**” because there is no ID learn yet.

<img class="common_img" src="../_static/media/section_12_2.8 Feature Learning/media/image2.png" style="width:500px" />

**Step 2:** Move the module to adjust the image and them press Function Button to start the learning process.

<img class="common_img" src="../_static/media/section_12_2.8 Feature Learning/media/image3.png" style="width:500px"  />

**Step 3:** ID and P have their values after learning. P is the value to signify the trust value. The highest value is 1.0 being closest matched.

<img class="common_img" src="../_static/media/section_12_2.8 Feature Learning/media/image4.png" style="width:500px"  />

<img class="common_img" src="../_static/media/section_12_2.8 Feature Learning/media/image5.png" style="width:500px"  />

> [!NOTE]
>
> **Note: To Learn multiple objects, ensure there are distinctive differences in the features with other ID. When multiple ID are learned, the result will show the highest matched ID and matching value.**

* **Delete Feature ID**

To delete a Feature ID, in Feature Learning menu push Function Button to the left to enter Learn mode.

Navigate to the Feature ID to be deleted and push Function button to the right towards Bin icon to delete.

Push Function button down to select OK to exit.