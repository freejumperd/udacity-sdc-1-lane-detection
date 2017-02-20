#**Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

[1_raw]: ./writeup/1_raw.png "Raw"
[2_regionmask]: ./writeup/2_regionmask.png "Region Mask"
[3_colormask]: ./writeup/3_colormask.png "Color Mask"
[4_edges]: ./writeup/4_edges.png "Edges"
[5_lines_hough]: ./writeup/5_lines_hough.png "Lines Hough"
[5_lines_extrapolated]: ./writeup/5_lines_extrapolated.png "Lines Extrapolated"
---

### Reflection


###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 4 steps: applying a region mask, applying a color mask, detecting edges, and then detecting lines.

![alt text][1_raw]

1) A region mask insures that we do not include erroneous measurements that bias our line estimates. By doing this step early, we can potentially avoid spending compute power on regions that will not be considered. Keep in mind that we are assuming that all essential road markings can be found within a triangular polygon:

![alt text][2_regionmask]

2) A color mask assumes that road lines are either white or yellow. Of course, thresholds were chosen from examples with fairly consistent and bright lighting conditions.

![alt text][3_colormask]

3) Edge detection assumes that road marking have high constrast with their surroundings. This allows us to use the Canny edge detector (which uses gradients along both x and y directions). Notice that the detector identifies PIXELS with high contrast (as opposed to line segments, which is what we actually want).

![alt text][4_edges]

4) Line detection looks for groups of pixels that agree on the line. Of course, an inherent assumption is that road markings can be modeled as a line.

The result of the hough transform groups neighborhoods of previously identified edges. Unfortunately, these do not fully capture the boundaries in which the car should remain.

![alt text][5_lines_hough]

To identify the lane boundaries, we extrapolate the hough lines by separating the identifies line segments into two sets: left and right lane. These are identified by grouping lines with similar slopes (within some threshold -- to eliminate erroneous cars that also happen to be white). For each group of lines, we fit points of the line segments to a line along the boundaries of the image (from bottom to middle of the image).

![alt text][5_lines_extrapolated]



###2. Identify potential shortcomings with your current pipeline

There are a lot of assumptions mentioned above

1) Reliance on road markings within a particular region

2) Assuming certain types of lighting conditions due to fixed RGB thresholds for color masking

3) Lanes must have high contrast with the road so dirt roads would be a problem

4) Lanes are lines without curves


###3. Suggest possible improvements to your pipeline

1) Adaptive region boundaries

2) Adaptive lighting thresholds

3) Utilize scene understanding

4) Fit lanes to a polynomial instead of a linear function

