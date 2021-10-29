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

[image1]: ./output_images/distortion_correction_chess/calibration1.jpg "Undistorted"
[image2]: ./output_images/distortion_correction/test3.jpg "Road Transformed"
[image3]: ./output_images/binary/test3.jpg "Binary Example"
[image4]: ./output_images/birds_eye/test3.jpg "Warp Example"
[image5]: ./output_images/fit_polynomial/test3.jpg "Fit Visual"
[image6]: ./output_images/search_fit/test3.jpg "Output"
[image7]: ./output_images/pipeline/test3.jpg "Output"
[video1]: ./output_videos/project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the second code cell of the IPython notebook located in "./Advanced Lane Finding.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

I applied `calibration()` function on chess board images to find mtx, dist, and then applied `distortion_correction()` function.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image in the 5th code cell.  Here's an example of my output for this step.

![alt text][image3]

I filtered red and green channel in RGB model, and l and s channel in HLS model along with gradient thresholds. I also applied a mask to eliminate unwanted features.

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `birds_eye()`, which appears in the 7th code cell of the IPython notebook. The `birds_eye()` function takes as inputs an image (`image`).  I chose to hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(x / 2) - 63, y / 2 + 100],
    [((x / 6) - 20), y],
    [(x * 5 / 6) + 60, y],
    [(x / 2 + 65), y / 2 + 100]])
dst = np.float32(
    [[(x / 4), 0],
    [(x / 4), y],
    [(x * 3 / 4), y],
    [(x * 3 / 4), 0]])
```

Where x = image.shape[1], and y = image.shape[0].

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

First I used the `find_lane_pixels()` function to find lane line pixels. Then I fit an polynomial to those pixels by using `fit_polynomial()` function (the 9th code cell).

![alt text][image5]

Then I used `search_around_poly()` and `line_average()` to search more efficiently (the 11th code cell).

![alt text][image6]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in the 13th code cell. I used two functions for this purpose. `measure_curvature()`, and `car_position()`.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the 15th code cell in the function `draw_on_image()`.  Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

#### 1. Provide a link to your final video output. Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's the link to project video: [project_video_output.mp4](./output_videos/project_video_output.mp4).

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I think the most challenging part of this project is the lane boundary section. The right threshold boundaries for binary image section, and correct source and destination points for birds-eye view section would be found by trial and error.

The implemented pipeline would fail in dark or fuggy situations where lane lines are not so clearly appearing in the captured video. In addition, series of sharps turn in the road would lead to the failure of the pipeline.

Sanity check of the detected lane lines is one way to address these issues.
