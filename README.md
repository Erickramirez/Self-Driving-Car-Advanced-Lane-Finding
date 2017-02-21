# SDCND-P4-Advanced-Lane-Finding
###In the context of traffic control, a lane is part of a roadway that is designated for use by a single line of vehicles, to control and guide drivers and reduce traffic conflicts [wikipedia](https://en.wikipedia.org/wiki/Lane). Lane finding is important for perception in order to know about the environment and the road. This project is applying computer vision techniques to detect the lane where the car is located for perception. 
---

**Advanced Lane Finding Project**

The steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Use color mask and sobel filters to create a thresholded binary image.
* Detect lane pixels and fit to find the lane boundary. There are two algoritms to perform this task:
i.	fit_polinomial_sliding_windows and ii.	fit_polinomial_based_on_lines 
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./images/corners.JPG "Corners"
[image2]: ./images/Undistorted_Image.JPG "Undistorted Image"
[image3]: ./images/Road_Undistorted_Image.jpg "Road Undistorted Image"
[image4]: ./images/birds_eye_view.JPG "birds eye view"
[image5]: ./images/color_mask_and_Sobel_Filters.JPG "color mask and Sobel Filters"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Camera Calibration

####1. Camera calibration consist to find the quantities internal to the camera that affect the imaging process. The first step is case extracting object points and image points for camera calibration.

The code for this step is contained in the second code cell of the IPython notebook located in "./camera_calibration.ipynb" 

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (9, 6) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

![alt text][image1]
I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  The code for this step is contained in the last code cell of the IPython notebook located in "./camera_calibration.ipynb" 

I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 
![alt text][image2]


###Read and undistort image

####1. read the image and apply the distortion coefficients found in the camera calibration, this help to reconstruct a world model with more accuracy.
The code for this step is contained in the function undistort_image() of the IPython notebook located in "./P4-CarND-Advanced-Lane-Lines-master.ipynb" 
![alt text][image3]

####2. perspective transform

The code for my perspective transform includes a function called `getPerspectiveTransform_values()`,  in the IPython notebook located in "./P4-CarND-Advanced-Lane-Lines-master.ipynb".  The `getPerspectiveTransform_values()` function takes as inputs an image (`image size`), as well as source (`src`) and offset .  After dat It will apply the `warp_image()` function with the return of the perspective transform and  the image. 
![alt text][image4]

####3.Use color mask and sobel filters to create a thresholded binary image: 
#####i.	Select yellow color: transform the RGB image to HSV image and use the following range: [22, 35, 80] to [100, 255, 255]
#####ii.	Select white color: transform the RGB image to HLS image and use the following range: [0, 180, 160] to [150, 255, 255]
#####iii.	Filter for saturation:  transform the RGB image to HLS image and use the following range: [20, 0, 100] to [100, 255, 255]
#####iv.	Apply color mask using Or operator: mask[(hsv_yellow ==255)| (hls == 255) | (hls_white == 255)] = 1
The code for this step is contained in the second code cell of the IPython notebook located in "./P4-CarND-Advanced-Lane-Lines-master.ipynb" 
Here's an example of my output for this step.  (note: For this image I applied bitwise_and using as a mask the restult of this step)

![alt text][image5]



```
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])

```
This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  


