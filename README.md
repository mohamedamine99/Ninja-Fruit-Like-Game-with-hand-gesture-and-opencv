# Ninja-Fruit-Like-Game-with-hand-gesture-and-opencv

<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about-the-project">About The Project</a></li>  
    <li><a href="#software-requirements">Software Requirements</a></li>
      <ul>
        <li><a href="#python-environment">Python environment</a></li>
        <li><a href="#packages">Packages</a></li>
      </ul>
    </li>
    <li><a href="#software-implementation">Software implementation</a></li>
    <li><a href="#executing-the-program">Executing the program</a></li>
    <li><a href="#making-an-upgrade">Making an upgrade</a></li>
    <li><a href="#conclusion-and-perspective">Conclusion and perspective</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgements">Acknowledgements</a></li>
       
  </ol>
</details>

## About the project

The use of a physical device for human-computer interaction, such as a mouse or keyboard, hinders natural interface since it creates a significant barrier between the user and the machine.  
However, new sorts of HCI solutions have been developed as a result of the rapid growth of technology and software.  
In this project , I have made use of a robust hand and finger tracking system ,which can efficiently track both hand and hand landmarks features , in order to make a fun Ninja fruit-like game.

## Software Requirements:

### Python environment:

* Python 3.9 
* A python IDE , in my case I used [PyCharm](https://www.jetbrains.com/fr-fr/pycharm/).

### Packages:
* [OpenCV](https://opencv.org/course-opencv-for-beginners/#home) : OpenCV is the world's largest and most popular computer vision library . The library is cross-platform and free for use.
* [MediaPipe](https://google.github.io/mediapipe/) : MediaPipe offers cross-platform, customizable ML solutions for live and streaming media. it will help us detect and track hands and handlandmarks features.
* [Numpy](https://numpy.org/) : introducing support for large, multi-dimensional arrays and matrices, as well as a vast set of high-level mathematical functions to manipulate them.

**NB**: All these packages need to be installed properly.

## Software implementation:

Now let's get to our code:  
Let's begin with importing the required packages

 ```py
import cv2 
import time               # useful for calculating the FPS rate
import random             # for spawning "fruits" at random positions and random colours
import mediapipe as mp    # for hand detection and tracking
import math               # for various mathematical calculations
import numpy as np
 ``` 
Now lets get our objects :
 ```py
mp_drawing = mp.solutions.drawing_utils
mp_drawing_styles = mp.solutions.drawing_styles
mp_hands = mp.solutions.hands

hands = mp_hands.Hands(False,1,0.7,0.5)
 ``` 
The  `hands = mp_hands.Hands(False,1,0.7,0.5)` line is used to initialize a MediaPipe Hand object.  
Its arguments are as follows:
* **static_image_mode:** Whether to treat the input images as a batch of staticand possibly unrelated images, or a video stream. 
* **max_num_hands:** Maximum number of hands to detect. 
* **min_detection_confidence:** Minimum confidence value ([0.0, 1.0]) for hand detection to be considered successful. 
* **min_tracking_confidence:** Minimum confidence value ([0.0, 1.0]) for the hand landmarks to be considered tracked successfully. 
  
now , the below variables will be needed to calcumate the FPS rate.

 ```py
curr_Frame = 0
prev_Frame = 0
delta_time = 0
 ``` 
Let's create and assign our gameplay variables:
 ```py
next_Time_to_Spawn = 0   # variable to compute the time to spawn a "fruit".
Speed = [0,5]            # Speed vector along the x , y axis
Fruit_Size = 30          # radius of the circle representing the fruit
Spawn_Rate = 1           # Spawning rate of "fruits" (Per second) initially at 1 fruit /s
Score = 0                # Score initially at 0
Lives = 15               # number of Lives initially at 15
Difficulty_level= 1      # Difficulty level which will increase according to Score, initially at 1
game_Over=False          # Whether the game is lost , initially false ofc.
 ``` 
 
  ```py
 slash = np.array([[]],np.int32)   # a numpy array of arrays in order to keep track of the index finger positions in order to draw a curve representing the slash
slash_Color=(255,255,255)         # initial slash color : white
slash_length= 19                  # number of points to keep track of

w=h=0       # to store width and height of the frame
Fruits=[]   # the list to keep track of the "fruits" on screen
 ``` 
 Now lets create our functions :
 lets begin with fruit spawning function:
 
   ```py
 def Spawn_Fruits():
    fruit = {}
    random_x = random.randint(15,600)                                                   # x position of the fruit randomly generated
    random_color = (random.randint(0,255),random.randint(0,255),random.randint(0,255))  # Colour of the fruit randomly generated
    cv2.circle(img,(random_x,440),Fruit_Size,random_color,-1)                           # spawning the fruite as a circle on random x position and on a 440 y position
    fruit["Color"] = random_color                                                       
    fruit["Curr_position"]=[random_x,440]
    fruit["Next_position"] = [0,0]
    Fruits.append(fruit)
 ``` 
Each fruit data is represented with a dictionary with the following keys : `"Color"` , `"Curr_position"` ,`"Next_position"` .
so we can keep track of each fruit after its creation it must be appended to the Fruit list 
Each fruit is generated at position of a 440 value on the y axis and a random position betwwen 15 and 600 on the x axis
Each fruit is generated with a random colour of value between 0 and 255 on each of the rgb channels.

