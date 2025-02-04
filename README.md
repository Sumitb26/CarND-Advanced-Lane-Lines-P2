**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/undistorted.jpg "Undistorted"
[image2]: ./output_images/sChannel.jpg "S Channel"
[image3]: ./output_images/sobel.jpg "Sobel"
[image4]: ./output_images/combined.jpg "Combined"
[image5]: ./output_images/warped.jpg "Warped"
[image6]: ./output_images/laneLines.jpg "Lane Lines with sliding window"
[image7]: ./output_images/drawLanes.jpg "Detected lane on input image"
[image8]: ./test_images/test3.jpg "Input Image"
[video1]: ./video_output/project_video.mp4 "Video Output"

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./P2.ipynb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Distortion-correction is implemented in `undistort()` in cell 6 of P2.ipynb. cv2.undistort was used with mtx and dst returned by `cv2.calibrateCamera()` function.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. I combined output from `sChannel()` and `Sobel()` function in cell 7 & 8 respectively. For color thresholding first I converted RGB image to HLS color space and used s channel of the output which detected yellow lines efficiently. For gradient threshold I used sobel operator to detect lane lines. Below you can find images of sobel output, sChannel output and combined image.

![alt text][image2]
![alt text][image3]
![alt text][image4]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `PerspectiveTransform()`, which appears in 10th code cell of the P2.ipynb.  The `PerspectiveTransform()` function takes as inputs an image (`image`). I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[  100.,   719.],
                      [  542.,   470.],
                      [  738.,   470.],
                      [ 1180.,   719.]])
dst = np.float32([[ 200.,  720.],
                      [ 200.,    0.],
                      [ 1080.,    0.],
                      [ 1080.,  720.]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

This is implemented in 11th cell of `P2.ipynb` in `findLanes()` function. First I used histogram peaks on bottom part of the image to determine position of lane lines. Then I used sliding windows moving up the detected lane lines to determine where it's going and then I fit a polynomial on detected lane lines.

![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

this is implemented in 12th cell of P2.ipynb. I used radius of curvature formula for polynomials. I determined the position difference of the vehicle by substracting the vehicle center from lane center. 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in 13th cell of P2.ipynb lines the function `drawLane()`.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result][video1]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline fails when input video has a lot of turns one after the other  
