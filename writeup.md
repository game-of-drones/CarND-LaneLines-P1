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

I tested my pipeline on all the test images (in a separate cell). And I save the resuting images in the same `test_img` folder with `result_` prefix for file name.

#### How to draw a single line instead of a set of line segments

In order to draw a single line on the left and right lanes, I followed the hints the `draw_lines()` function. I created a new function `draw_lines_v2()`, and call it in `hough_lines()`. Here is the procedure of `draw_lines_v2()`.




###2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


###3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
