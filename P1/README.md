# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />


## Project: **Finding Lane Lines on the Road** 

The process pipe line is as follows:


1. Filter the colors of interest (white and yellow for lane markings)
2. Convert the image to gray scale
3. Blur the image and extract edges using Canny edge detector
4. Use Hough transform to extract lines
5. Average the extracted lines and overlay it with the original image


### Color Selection
The lane markings are strictly in White or Yellow and hence we need to filter out only the pixels which are white or yellow. I originally began with RGB space and after further exploration realized HLS color space works better.

### RGB Image
For white I chose the upper and lower limits as **[195, 195, 195]** and **[255, 255, 255]** respectively. Similarly, for yellow, **[200, 200, 0]** and **[255, 255, 200]**

![RGB Space](./examples/rgb.png?raw=true )

### HLS Space 
In HLS space, white and yellow pixels are recognized better. All further processing will be performed in HLS.


![HLS Space](./examples/hls.png?raw=true )


To detect edges, we only need the change in intensity of the pixel. So we can safely convert the image to grayscale to make the algorithm faster.

![Grayscale image](./examples/gray.png?raw=true )


To avoid noises in the edge detection, we need to smoothen out the edges. Gaussian filtering with a kernel size of 9 is applied for this task.

![Blurred image](./examples/blur.png?raw=true )

### Edge Detection

Canny edge detector was employed to extract the edges from the image using the intensity gradient. The Canny Edge detector was developed by John F. Canny in 1986. Canny algorithm satisfies three main criteria:
* Low error rate: Meaning a good detection of only existent edges.
* Good localization: The distance between edge pixels detected and real edge pixels have to be minimized.
* Minimal response: Only one detector response per edge.

![Detected edges](./examples/edge.png?raw=true )

### Region of Interest

Since the lane markings are usually within the center-lowerhalf of the image (atleast for the given situation), I defined a polygon which encompasses the region that interests us. This ensures unwanted edges in the image like sign boards are neglected. 


![Polygonal ROI](./examples/roi.png?raw=true )

### Line Extraction

I then applied Hough transform to extract lines from the edge images. 


### Draw Lines 
Now that we have the line coordinates in (x,y), we need to look into the slopes of these lines to classify them as left and right lane markers.

![Hough lines](./examples/lines.png?raw=true )

### Slopes

Since the (0,0) is top left, a positive slope implies that the line belongs to right lane. Once the slopes have been computed, the lines are averaged. Weights are assigned to each line based on its length. This makes sure that longer lines (possible lane markings) are preferred shorter ones (errors and other possible edge lines).

Some lane lines are only partially recognized and some are dashed white lane markings. So, once the lane lines are averaged, they are extrapolated to highlight the entire lane line from the bottom of the image to the centre.


![Detected lanes](./examples/lanes.png?raw=true )


## Reflections

The project makes a lot of assumptions on the input camera feed. Currently, all lane lines are assumed to be straight lines, which is most likely to be the case unless the car is traversing on steep curves. 

Also, there is no contingency in effect if there is a loss of camera feed for few frames. One possible solution could be storing past slopes and averaging them to fill in when there is frame loss.