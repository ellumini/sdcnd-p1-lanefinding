
# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./test_images_output/outputsolidWhiteCurve.jpg
---

### Reflection

### 1. Pipeline description / the modification of the draw_lines() function.

My pipeline consisted of following steps:

1. convert image to greyscale 
2. run edge detection with previous gauss blur 
3. limit region of interest with 4sided polygone
4. run line detection based on hough line detection
5. draw lines with draw_line()
5. superimpose drawn line image with initial image
6. save image in file directory  

#### function modifications
##### hough_lines()

Instead of only returning the image array with lines drawn on the hough_lines() returns now a tuple of:

- image with lines 
- np.array and the corresponding houghlines (x1,y1,x2,y2)


##### draw_lines()
- calculates m (cartesian slope) of each line
- divides lines array into left lane lines and right lane lines based on:
	- on the slope of the line (between 0.3 m 0.9) 
	- and wether its left or right from the center axis (x_center = imshape[1]/2) 
- point pairs that respect the conditions for the slope and the position to the center and divides them into four lists:
	- x values for left lane
	- y values for left lane
	- x values for right lane
	- y values for right lane
- uses these lists with the help of a one-dimensional regression (np.polyfit) to derive a line equation for left and for right lane
- takes y_far(given as argument in function) and y_near(bottom of image via image shape) coordinates to calculate the respective x cordinates 
- with the resulting 4 points the lane marking is be drawn

### input image
![before][image1]
### output
![after][image2]

### 2. Shortcomings with current pipeline

the basic issue is that every line within the polygon-region is taken into account for np.polyfit meaning the following scenarios:
- it is fixed to one camera setting
- lane switching of the car itself
- objects directly in front of the car 
- abrupt changes in road slope ahead
- ...

another more programming oriented shortcomming is that the np.polyfit approach aborts when there is no points detected as relevant points for the lane based on the previous steps. 

### 3. Possible improvements to your pipeline

- dynamic limiting of region of interest only to lane marking (also based on other sensor data)
- hsv filter => consider white and yellow lines
- in a video setting it could be very benificial to take into consideration a maximum change rate between the last images the current image(it is not realistic that the lane marking changes abruptly)
- higher dimensional polygones for np.fit
- dynamic slope conditions for the detected lines (near to the car lane slopes do not vary as much as distant slopes of hough detected lines)
- ...