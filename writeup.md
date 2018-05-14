# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/1_solidYellowLeft.jpg "Masking"
[image2]: ./test_images_output/2_solidYellowLeft.jpg "Color Selection"
[image3]: ./test_images_output/3_solidYellowLeft.jpg "Grayscale"
[image4]: ./test_images_output/4_solidYellowLeft.jpg "Blur"
[image5]: ./test_images_output/5_solidYellowLeft.jpg "Canny"
[image6]: ./test_images_output/6_solidYellowLeft.jpg "Hough"
[image7]: ./test_images_output/7_solidYellowLeft.jpg "Weighted"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

I had 7 steps in my pipeline, described as follows:

#### 1. Region Masking
I masked using vertices = np.array(\[\[(60,imshape\[0]),(440, 330), (520, 330), (imshape\[1]-20,imshape\[0])]], dtype=np.int32), which did a good job of removing everything outside the lane ahead of the car
![alt text][image1]

#### 2. Color Selection
Using a color threshold of \[200,200,0], I was able to isolate bright yellows and whites
![alt text][image2]

#### 3. Grayscale
I converted to grayscale to allow me to use Canny edge detection in a later stage
![alt text][image3]

#### 4. Blurring
I blurred with a kernel size of 11 to soften edges/remove small artifacts ahead of Canny edge detection
![alt text][image4]

#### 5. Canny Edge Detection
Canny edge detection with low and high thresholds of 40 and 200
![alt text][image5]

#### 6. Hough Transform
Hough transform with rho = 1, theta = np.pi/180, threshold = 20, min_line_len = 12, max_line_gap = 1. The image_hough function calls draw_lines2(), a modified version of draw_lines().  

In draw_lines2(), lines are seperated into left/right lane markings by slope. Negative slopes indicate left lane markings, and positive slopes indicate right lane markings. Lines with small values for slope are ignored as they indicate horizontal lines that are likely not lane markings. To extrapolate, lines are fitted through the points from the left and right line points to get the parameters for the left and right lane lines.  Using fixed values for y corresponding to the bottom and top of the lane, the x values are calcuated and the lane lines are drawn on the image.
![alt text][image6]

#### 7. Weighted images
The output from the hough transform is combined with the original image to provide a semitransperant effect.
![alt text][image7]

### 2. Identify potential shortcomings with your current pipeline

There are a number shortcomings in my current pipeline:

Currently, the lines are a little "shakey", which is another area that the pipeline could be improved. Some shakeyness could be removed by tuning the parameters, and further improvements could be made by detecting smaller line segments corresponding to lane markings further away from the car.  However, trying to detect smaller line segments could also lead to false lane markings being detected.

Furthermore the challenge video, the current pipeline has trouble with the bright/changing lighting conditions.

Bright objects on the road or road markings that are not lane markings could also cause my current pipeline to have some problems, along with worn/faded lane lines.


### 3. Suggest possible improvements to your pipeline

Beyond the improvements suggested above for shakeyness, it may be possible to do a better job of color selection in another colorspace - perhaps HSV or HSL.  Normalizing image brightness might also help with colorselection and canny edge detection.

Another improvement is making the pipeline more robust to changing image shape.  Currently, most of the vertices used in the region masking stage are fixed points that do not account for the shape of the image.  This could be improved by making more of the points relative, or even further improved by detecting the horizon and sides of the road, and incorporating that information in the masking region.

