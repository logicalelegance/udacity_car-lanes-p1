# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./pipeline.png "Pipeline"
[image2]: ./singlelanes.png "lanes"
---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The basic pipeline consists of the following six stages:

- Convert the image to grayscale. Removeing unncessary color information enhances the contrast and makes edge detection and line detection more effective. However, in the challenge, it was clear that this simple color transformation may not be sufficient for all lighting/surface conditions.
- Blur the image. Using Gaussian blur to remove some of the high frequency spatial noise in the image reduced the number of false edges detected and improved proper lane finding.
- Determine edges using the canny edge detection algorithm. This produces an image with the outlines of areas with high gradient value subject to the canny thresholds which can be used as input to the Hough transform.
- Screen the portion of the image we aren't interested in with a region of interest (ROI)
- Apply the Hough transform to find the prominent lane lines.
- Finally merge the Hough transform image lines with the original image.

Below is an image by image walkthrough of the above steps, in the same order.

![Pipeline illustration][image1]

Optionally, we further consolidate the line outputs from the Hough transform by:

- Collecting line segments into left/right lanes by slope (positive slopes belong to the left lane, negative to the right)
- Average all the segments' slopes and y-intercepts to produce a combined equation of a single line they describe. In this step, slopes that are obviously not part of a lane are rejected (like those of Bott's dots, lane cracks). With this equation, we compute a line that starts at the bottom of the image and ends at the top of the ROI.

The output of that looks like this:

![Single lane line illustration][image2]

### 2. Identify potential shortcomings with your current pipeline

I've tried to apply some error handling where lines would result in divide by zeros and other such problems. However, if *no* lines are found by the Hough transform, then my algorithm does break down.

Additonally, real visible lines that are not lane markers but are within the region of interest will factor into the lane finding algorithm and will produce error in the lane finding, causing jitter.

As seen in the challenge portion, this algorithm does not handle changing lighting/pavement surface well as it's tuned for the test images and conditions.

A problem with this entire frame-by-frame method is that if lane lines are not found, nothing is drawn on the image.

### 3. Suggest possible improvements to your pipeline

To reduce jitter, some history could be kept and averaged so that lane lines have a smoother transition as they change. This would also handle loss of lane lines or transient poor image quality by maintaining lane location based on prior data.

The algorithm probably should use more sophisticated color treatment than simply grayscale. Moving to HSV or LAB colorspace might allow more clear separation of lanes from pavement and handle more conditions.

Fitting something higher order than a line to the segments would give a more accurate representation of the lane marker on curves.
