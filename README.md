# **Finding Lane Lines on the Road** 


---

### Reflection

### 1. Pipeline description


The image processing pipeline involves several main steps.
1. Image is converted to grayscale
* Gaussian blur is applied
* Edges are detected with Canny edge detection
* Image is masked to isolate the region of interest
* Hough transform is applied to detect lane segments
* Segment lines are averaged and extrapolated to cover the whole lane

Converting segments to a single line involved modifying the draw_lines() function to apply linear regression to each segment. The gradient and intercept were averaged over the left and right side of the image seperatly to specify two individual lines. A buffer was added around a gradient of zero to remove segment gradients that fell between -0.5 and 0.5. This was done to stop artifacts on the side of the road causing jitter and major deviations from the lane markings. Finally the specified lines were extrapolated to the bottom of the image and to 40pixels in the x-axis prior to where the left and right lines would intersect. The vertical cap was set to make the image more aesthetically pleasing. 


### 2. Potential shortcomings with the current pipeline


The primary issue with the pipleine is it can't handle curved road segments. This is caused by two underlying issues. The masked region of interest is static and only detects in the area imediatly infront of the car. That is it wouldn't adjust left or right during a curve. Additionally the draw_lanes() function applies a linear model to the segments which guarentees that even if a curve is found it won't be displayed.

Another issue is shadowed areas make it difficult to detect lane markings. This is most promenent when a shadow blocks the entirety of either the left or right side of the interest area. This results in absolutle loss of one or both of the lane lines. In the current implementation this also causes numeric error.


### 3. Suggested improvements to the pipeline

A possible solution to finding curves would be to forego linear regression in favour of a higher order polynomial or curved spline. Using the raw points from the hough transform it is not difficult to fit a series of cubic splines. 

To fix the issue with detecting lanes under shadow information from a pervious frame could be used to assist prediction in later frames. For example if there is a frame where shadow causes errors in detecting a lane we should be able to go back to the prior frame knowing that all things considered the gradient and intercept of the extrapolated line shouldn't change significantly. Playing around with this could lead to a solution which handles single frame defects.