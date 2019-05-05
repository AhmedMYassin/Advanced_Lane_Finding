# Advanced Lane Finding Project

[//]: # (Image References)

[image1]: ./output_images/Distorted_Undistorted.jpg "Undistorted"
[image2]: ./output_images/Undistorted_image.jpg "Road Transformed"
[image3]: ./output_images/Pipeline_result.jpg "Binary Example"
[image4]: ./output_images/Perspective_Transform.jpg "Warp Example"
[image5]: ./output_images/lane_lines.jpg "Fit Visual"
[image6]: ./output_images/Road_Curvature.jpg "Output"
[video1]: ./project_video.mp4 "Video"
[image7]: ./output_images/Color_Space.jpg "Color Space"
[image8]: ./output_images/Grad.jpg "Gradient"

### Camera Calibration

First I find the "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline

#### 1. Undistortion

Here I used the calibration parameters to undistort one of the test images:

![alt text][image2]

#### 2. Color Space and Gradient

I strated with visualizing the effect of different color spaces to decide which one to select or use combination. R in RGB and S & L in HSL were the best so I decided to use combination of them.

![alt text][image7]

Then I checked the gradient effect to decide which one gradient direction should I select. The gradient in x-direction was the best but it wasn't as good as any of the results of color space tests. 

![alt text][image8]

Finally, I decided to use combination of R, S, L to create a thresholded binary image in `Detect_lane_lines()`. You can here check the original image and the resulted binary image

![alt text][image3]

### 3. Perspective transform

Here I needed to have a top view of the road to calculate the radius of curvature. I defined first source and destination points int the following manner:
                  
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

#### 4. Lane lines as polynomial function

As mentioned in the project tutorial, I used image histogram to difine the start position of the left and right lane lines. After that, I used windows starting from the start points to detect lane line pixels from bottom to top. Each window is a reference for the next one to define the search area. After detecting all pixels from bottom to top, I used them as input to `polyfit()` to define the polynomial function for each lane line. Here you can check the result:

![alt text][image5]

#### 5. Radius of Curvature

To calculate the curvature radius for each lane line function, I had to convert the pixels dimension to the real world dimension. Each pixel represents `30/720` m in the y direction and `3.7/700` m in the x direction. After this conversion, I have the real world x,y points so I can calculate the radius of cruvature for each lane line.

#### 6. Plot lane area

After defining the lane lines and the `x,y` coordinates in real world, I fill the area between those 2 lines with green color to visualize the final results.

![alt text][image6]

---

### Video Processing

[Here](./project_video_output.mp4) is a link to the project video after processing. In video processing, I used batches of 10 frames to calculate the average radius of curvature and the lane lines equation to keep them smooth as mush as possible.

I also processed another 2 videos (challenge and hard challenge) but the results wasn't good enough but you can check them from [here](./challenge_video_output.mp4) and [here](./harder_challenge_video_output.mp4) 


### Discussion

The most difficult part of this project is to define the right threshold to be able to detect the lane lines. However, the processed project video was good. The challenge video wasn't. I tried to change the threshold to get better output but these values wasn't good for the project video.
Maybe there is another way to detect lane lines instead of tuning threshold values .. maybe I would need to manipulate the image lighting and darkness based on its histogram.
