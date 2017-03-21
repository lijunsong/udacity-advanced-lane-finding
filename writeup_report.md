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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is in Jupyter notebook section "Camera Calibration." The general steps are preparing arguments to pass into `cv2.calibrateCamera`

 1. `objpoints` is (x, y, z) positions in 3D world. In our case, z is 0, and `x` and `y` can be simply passed in `(0,0)`, `(1, 0)`, ..., `(5, 8)`
 2. for all chessboards, use openCV `findChessboardCorners` to get `corners`, i.e. points on 2D plane.
 3. passing `objpoints` and all corresponding `corners` to `calibrateCamera` to get camera matrix and distortion coefficients.

Here is an example of restoring distorted image (it is also in Jupyter Book under section "Correct Image Distortion.")

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.
Here is an example of correction of distorted image

!["Correct distortion"](./output_images/correct-distortion.png)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
there are various functions for thresholding colors and gradients in the Jupyter book, under title "Create Thresholded Binary Image."

I used a combination of color and gradient thresholds to generate a binary image.

I transform the image from RGB to HLS color space (function `todo`) and take its `S` channel as one of many sources of the final binary image.

I also threshold images by its gradient magnitude, TODO.

Here's an example of threshed images.

TODO.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Perspective transformation is done using function `TODO`. The code is under Jupyter notebook section "Perspective Transform."

I selected `(. TODO ..)` as source points. It is hard coded, because the camera position is known. I selected `(...TODO...)` as destination points.

Finally I verified that the source and destination worked as expected by eyeballing a perspective transformation done on an image containing a straight lane; the warped image appeared to have two parallel lines. I visualized the image below:

IMAGE TODO.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

To identify lane-line pixels, I used the sliding window techniques given in the lecture: split the perspective-transformed lane line vertically into 9 windows, and find lane line pixels in each of these windows. After getting pixels, I used `polyfit` function to fit these pixels with a polynomial.

The code is in section "TODO". Here is a visualization of the fitting process.

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

After getting parameters of two polynomials (left line and right line) `f(y) = A*y^2 + B*y + C`, I calculate `f1(y)` and `f2(y)` given `y` is 720, the height of the image. The deviation of vehicle is the deviation of the middle of the image and middle of `(f1(720)+f2(720))/2`

Calculate the radius of curvature: TODO.

Finally, these calculated numbers are in pixels. It's then turned into estimated meters.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented `AnnotateLane` function in section `TODO`. Here is an example of annotated images:

IMG TODO.

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's the [link to the anotated value](./project_video.mp4)

---

###Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I didn't optimize the sliding window algorithm. Since it's computation intensive, it's not fast enough for real time lane detection. I can also see the following problems.

Perspective transformation currently is tightly coupled with camera position, and the region of interest is unfortunately hard coded. I can imagine this algorithm would have difficulty to recognize sharp turns and lanes on a big slope.

Color transformation could have problems because I only used the S channel. S channel is actually bad at highlighting lanes in the shadow. Take this example:

IMAGE TODO

Introducing another channel (maybe the L channel) may partially solve this problem. Again I think using one channel is not enough to highlight lines in different conditions.

Another problem is that I manually tuned all the parameters. The process is slow, inefficient, and not be able to produce optimal parameters. I would think of some automated process to do parameter tuning.
