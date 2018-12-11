## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)
## Advance Lane Lines Finder - Writeup

### Goals

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

[image1]: ./camera_cal/undistort_output.png "Undistorted"
[image2]: ./test_images/undistorted_straight_lines1.jpg "Road Transformed"
[image3]: ./test_images/combineGradient_color_straight_lines1.jpg "Binary Example"
[image4]: ./test_images/region_straight_lines1.jpg "Warp Example"
[image4_1]: ./test_images/perspective_tran_straight_lines1.jpg "Warp Example"
[image5]: ./test_images/poly_perspective_tran_straight_lines1.jpg "Fit Visual"
[image6]: ./test_images/warpback_poly_perspective_tran_straight_lines1.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points



### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./camera_calibration.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients (mtx,dist) using the `cv2.calibrateCamera()` function and saved to './camera_cal/wide_dist_pickle.p'
I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

The code used to implement these functionality could be found at "./CarND-Advanced-Lane-Lines-P2.ipynb"

#### 1. Provide an example of a distortion-corrected image.

The distortion coefficients were loaded from './camera_cal/wide_dist_pickle.p' and I unditorted the image like this. At the 

bottom of image, The car hood was flatened

![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines In[3] through In[8] in `./CarND-Advanced-Lane-Lines-P2.ipynb`).  Here's an example of my output for this step.  

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform , which appears in In[9] through In[10] in the file `./CarND-Advanced-Lane-Lines-P2.ipynb`.  The code takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  


This resulted in the following source and destination points:

|Source|Destination|
|-----:|----------:|
|(585, 455)|(200,0)|
|(705, 455)|(maxX - 200, 0)|
|(1130, 720)|(maxX - 200, maxY)|
|(190, 720)|(200, maxY)|

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]
![alt text][image4_1]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The algorithm calculates the histogram on the X axis. Finds the picks on the right and left side of the image, and collect the non-zero points contained on those windows. When all the points are collected, a polynomial fit is used (using `np.polyfit`) to find the line model. The following picture shows the points found on each window, the windows and the polynomials:
,which appears in In[12] in the file `./CarND-Advanced-Lane-Lines-P2.ipynb`.

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Using polynomial coefficients from In[12] I calculated R-curve on the meters space,which appears in In[13] in the file `./CarND-Advanced-Lane-Lines-P2.ipynb`.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.


To display the lane lines on the image, the polynomials where evaluated on a lineal space of the Y coordinates. The generated points where mapped back to the image space using the inverse transformation matrix generated by the perspective transformation. The code used for this operation could be found on `In [14]`in the file `./CarND-Advanced-Lane-Lines-P2.ipynb`., and the following images are examples of this mapping

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

After some refactoring of the code found at `./CarND-Advanced-Lane-Lines-P2-movie.ipynb`, the project video was processed and the results at [video_output](./result_project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

- I hard corded the percpective area, But this was assumed the lane line is alligned center of camera, If the lane were out of center then we could not find exact lane, So adaptive skill will impove this

- I didn't use Intra-frame information for polynomial coefficient. The inter-frame information will improve calcuration speed.
