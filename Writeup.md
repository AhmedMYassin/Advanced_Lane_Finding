# Advanced Lane Finding Project

[//]: # (Image References)

[image1]: ./output_images/Distorted_Undistorted.jpg "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./output_images/Perspective_Transform.jpg "Warp Example"
[image5]: ./output_images/lane_lines.jpg "Fit Visual"
[image6]: ./output_images/Road_Curvature.jpg "Output"
[video1]: ./project_video.mp4 "Video"

### Camera Calibration

First I find the "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

### 2. Perspective transform

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:
                  
```python
src = np.float32([[0.156*img_shape[1], img_shape[0]],
                  [0.458*img_shape[1], 0.632*img_shape[0]],
                  [0.548*img_shape[1], 0.632*img_shape[0]],
                  [0.882*img_shape[1], img_shape[0]]])
                  
dst = np.float32([[0.25*img_shape[1], src[0][1]], 
                  [0.25*img_shape[1], 0], 
                  [0.765*img_shape[1], 0], 
                  [0.765*img_shape[1], src[3][1]]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 200, 720      | 320, 0        | 
| 586, 455      | 320, 720      |
| 701, 455      | 980, 720      |
| 1129, 720     | 980, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Video Processing

[Here](./project_video_output.mp4) is a link to the project video after processing. In video processing, I used batches of 10 frames to calculate the average radius of curvature and the lane lines equation to keep them smooth as mush as possible.

I also processed another 2 videos (challenge and hard challenge) but the results wasn't good enough but you can check them from [here](./challenge_video.mp4) and [here](./harder_challenge_video.mp4) 


### Discussion

The most difficult part of this project is to define the right threshold to be able to detect the lane lines. However, the processed project video was good. The challenge video wasn't. I tried to change the threshold to get better output but these values wasn't good for the project video.
Maybe there is another way to detect lane lines instead of tuning threshold values .. maybe I would need to manipulate the image lighting and darkness based on its histogram.
