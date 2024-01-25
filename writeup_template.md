# **Finding Lane Lines on the Road** 

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:


[//]: # (Image References)
[image_1]: ./writeup_1.jpg
[image_2]: ./writeup_2.jpg
[image_3]: ./writeup_3.jpg
[image_4]: ./writeup_4.jpg
[image_5]: ./writeup_5.jpg
[image_6]: ./writeup_6.jpg

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps. 

1. I thresholdded "white" and "yellow" after converting the original image to HSV with the threshold values
   for "white" and "yellow". Then I apply the "masks" to the origianl image (overlap them together)
   This is the picture.
   ![image_1]

2. I used grayscale and added gaussian smoothing to the grayscaled picture. I chose the kernal size with
   the processed image here and after canny edge detector. I finally chose 5 because it makes the most effective gussian filter for the following steps. Using guassian filter mainly help to do lane edge 
   detection and help reduce some noice especially around the driving lane markings.
   ![image_2]

3. I applied canny edge detector to the image after guassian smoothing. The picture I got are those with
   with edges which are not filtered out by color threholding.
   ![image_3]

4. Then I added region of interest. My ROI is trapezoid made of four points in the graph, the points are 
   [100, 540], [900, 540], [500, 320], [400, 320] and they are the area close to the "Driver's vision".
   ![image_4]

5. I applied hough_lines here in which there are draw lines. The given draw_lines only draws the semgments
   thresholdded and within the region of interest. The improvement idea is mainly to do regression on points on all these lines. In draw_lines, I first classified the driving lane into left side and right side according to their slope. I also added a threshold after testing my initial idea because there's a picture in which the right lane drawn is a almost horizontal line. After adding a threshold to the "valid" driving lane detected by opencv, I got good lines classified. Then I did a linear regression on
   both lines and get best fit line. I applied the line with limit to y (I used 320 and 540 because they are
   the borders of my ROI), then I drew the line with the calculated (x1, y1), (x2, y2)
   ![image_5]

6. I applied addWeighted to the initial image and the image processed by the pipeline(which is simply two 
   lines drew on a black board).
   ![image_6]




### 2. Identify potential shortcomings with your current pipeline

1. During thresholding, the HSV thresholds for both white and yellow is not accurate enough, especially for 
   yellow, some of the surroundings are still not thresholdded out, such as yellow grass and road shone by
   shiny lights.

2. Color thresholding may introduce limits as well. We need region of interest to eliminate the limits. For 
   example, for some pictures, to make the yellow lanes be detected better, my threshold needs to be wider which leads noise into the picture. Though region of interest helps eliminate the limits, but we also could't have the information outside the ROI.

3. Using linear regression for draw lines cannot make curved lines. This leads to inaccuracy of lines when
   turning. We can only make straight lines everywhere.

4. Region of interest is static, this introduces limits to some of the situations. If the line is pretty wide
   or the car is switching the lane, the lane detection won't work under that case.




### 3. Suggest possible improvements to your pipeline

1. We should try to train a regression model that can be implemented on staright lines, on dotted lines, on
   curved lines, etc.

2. We can add a ML algorithm that dynamically detects the lanes in front of the car, so the lane detection
   can fit all cases, including U-turning, switching lanes, etc.

3. We can try to adopt edge detection using image gradient to see where are the "sharp" changes. Then 
   try to classfy edges into lanes and not lanes.
