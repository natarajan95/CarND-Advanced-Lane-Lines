## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

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

[out_pipeline1]: ./test5.jpg "Test Frame"
[video1]: ./output_rev2.mp4 "Video Output"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

#### Camera Calibration
 camera_calibration() helps us to calibration camera using chessboards (of 9x6 inner edges) as reference. We created "img_pt" list to obtain inner edge points from the chessboard images as (x, y) and "obj_pt" list to store translated coordinates (in 3D space) of type (x, y, z) with z being 0 for all points as the board is assumed to be on the sam efixed plane.

 We run through the all calibration images, convert them to gray scale and end up finding inner corners using OpenCV function: _findChessboardCorners_ and obtaining obj_pt and img_pt in the process. We can visualize them by drawing these idenitifed corners using _drawChessboardCorners_ function. Drawn calibrated images are stored onto _./camera_cal_res/_ directory 

####  Correct for Distortion
Now we use OpenCV's _calibrateCamera_ and _undistort_ function with the collected "obj_pt" and "img_pt" lists to undistort sample image frames. (Output frames at _./out_dist_correction/_ directory)

#### Perspective Transform
We define a region of ineterst, the ROI (Area that well focuses the lane lines on lower part of road) to perspective transform the undistorted frames so as to get a Bird's eye view of the ROI. We do this through OpenCV functions:
_getPerspectiveTransform_ creates a perspective transform matrix and _warpPerspective_ creates the desired undistorted bird's eye view(warped frame). (Source and target points are eye-balled from visually from the frame) (Output frames at _./out_persp_trans/_ directory)

#### Binarize Frame
_binary_threshold_ binarizes this warped image. This frame is now binarized by cnverting the frame onto hls color space and thresholding the _s-channel_. (Output frames at _./out_bin_img_ directory)

#### Identify Lane Lines
_hist_ function takes this binarized frame and retutns a histogram, which is essentially the sum of all points vertically across the width of the frame. Histogram peaks will tell us locations where we might have potential lane lines. So, peak points to the left and right of image centre as used as starting points to detect lane lines.

_fit_polynomial_ function generates two sets of 9 windows vertically, one set for left and the other for right lane line identification.
The bottom most window is centred at the histogram peak and all the other windows are vertically stacked over this. We iterate through these windows from bottom to top by virtue of the mean lane line, to be greate to the _minpix_ value. Then we fit a second order polynomial to these boxes to generate lane line.

#### Radius of Curvature
_radius_curvature_ function calculated lane line curvature and vehcile position wrt to centre. The lane fitted polynomials (both left and right polynomials) are now used to compute left and right lane curvatures using the formula from _https://www.intmath.com/applications-differentiation/8-radius-curvature.php_. Pixel values are converted to be represented in metres to represent real world.

Vehicle position is calculated as a mean value of detected lane lines and image's centre is half of undistorted image's. The difference between these two values gives the vehicle's location. Output test frames are stored onto _./out_test_images/_ directory.

#### Sample Output

![Sample Output][video1]

---

### Scope for Improvement

Few more color spaces/gradient detection are to be explored to make sure the lane lines are captured quite well on shaddy road and roads with walls.
