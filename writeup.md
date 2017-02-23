#**Finding Lane Lines on the Road** 

##Project Writeup

###You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 6 steps. 

1. Convert the image to grayscale: `gray = grayscale(img)`.

2. Run Gaussian blue to remove noise: `blurred_img = gaussian_blur(gray, 3)`. Here I chose kernel size to be 3.

3. Detect edges using Canny edge detector: `edge_img = canny(blurred_img, 50, 150)`

4. Mask the region that contains the lanes. Here I chose 4 vertices from basically observing all the test images and videos:
    ```
    vertices = np.array([[(x_size*0.5, y_size-1),
                           (x_size*0.95, y_size-1),
                           (x_size*0.51, y_size*0.58),
                           (x_size*0.49, y_size*0.58)]], dtype=np.int32)
    masked_edges = region_of_interest(edge_img, vertices)
    ```
    where `x_size=img.shape[1]`, `y_size=img.shape[0]`.

5. Run Hough transformation `hough_output = hough_lines(masked_edges, rho, theta, threshold, min_line_length, max_line_gap)`. I chose `rho=1 px`, `theta=np.pi/180.00` (1 degree), `threshold = 25`, `min_line_length=5`, `max_line_gap=1`.

6. Overlay the `hough_output` with the original image, to draw red line segments on the original image.

I tested my pipeline on all the test images (in a separate cell). And I save the resulting images in another folder `test_img_results` folder with `result_` prefix to file name.

#### How to draw a single line instead of a set of line segments

In order to draw a single line on the left and right lanes, I followed the hints the `draw_lines()` function. I created a new function `draw_lines_v2()`, and call it in `hough_lines()`. 

The basic procedure is like this

1. Group the line segments given by Hough transform in to 2 groups, left lane and right lane. The criterior for the segmentation are slope and position, i.e., the left lane has negative slope (since the origin is at top left) between -0.5 and -1.5, and is located on the left side of image; similar for the right lane.
    
2. Find the average slope of each group (left lane and right lane). I used weighted average of the slopes with the weight being the length of each line segments in the group.

3. Find one end point, i.e., the intersection with the bottom of the image of each group. What I did was to draw a line with the slope calcuated above crossing each end point of each line segment, and check it's `x` coordinate intersection with the bottom of the image. Then I remove the outliers and calcuate the average.

###2. Identify potential shortcomings with your current pipeline

There are some underlying assumptions with this code. 

1. The car is always driving in the center of the lane (without lane crossing). So I can categorize my left and right lanes using the slope and position assumption (step 1 above).

2. The camera is not wide angle, so the current vertices for the region of interest works. Otherwise, my code will detect multiple lanes and the average will mess up.



###3. Suggest possible improvements to your pipeline

To draw a single line, I feel that my method (following the hints in draw_lines) of averaging slopes and positions can be improved. I think we can use line fitting (least square) to do a better job.

Also, I notice that the final straight line jumps a little from frame to frame. In order to make it smoother, I think I can do some moving average from previous frames.

Also, if I know the lane line is either white or yellow, I should be able to do some color threshold to remove edges from things like curbs.
