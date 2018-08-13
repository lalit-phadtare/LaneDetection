# **Finding Lane Lines on the Road** 

[//]: # (Image References)

[image1]: ./test_output_images/input.jpg "Sample Input Image"
[image2]: ./test_output_images/output.jpg "Sample Output Image"
[image3]: ./test_output_images/grayscale.jpg "Output of grayscale conversion"
[image4]: test_output_images/blur.jpg "Output of Gaussian blur"
[image5]: ./test_output_images/edge.jpg "Output of Canny Edge Detection"
[image6]: ./test_output_images/ROI.jpg "Output of ROI"
[image7]: ./test_output_images/lines.jpg "Output of Hough Line Detection"






### Description

The goal of this project is to use input images of a lane marked road to detect the lanes and highlight the detected lanes in the output image. We first develop a process pipeline to run on single images. We further develop to deploy the same pipeline on video input.
![alt text][image1]
![alt text][image2]


The project is implemented as a part of [Udacity Self Driving Car Nanodegree coursework](https://www.udacity.com/course/self-driving-car-engineer-nanodegree--nd013)



### Process pipeline

Here we describe the processing pipeline on the single image:
1. We first convert the input image which is RGB i.e. color to grayscale as that is a more apporpriate colorspace for further processing.
![alt text][image3]



2. Second step is to use Gaussain blur on the image to smooth the image so that only prominent edges are left in the image. This is useful preprocesing for edge detection.
![alt text][image4]

3. Third, we find the edge in the grayscale image using the Canny operation. We use the OpenCV Canny operation. With certain experimemntation we found 50 and 150 to be suitable lower and upper threshold values for the dataset.
![alt text][image5]

4. Fourth, we generate a region of interest(ROI) mask for the image. This is the area where we expect the lanes that we are intersted to be in. The values of the polygon ROI are again found by experimentation. For the input image size of 540x980 the vertices are [(0, 540),(440, 320), (520, 320), (900, 540)] where (0,0) is the top left of the image.
![alt text][image6]

5. Finally, we detect the lines in the ROI using Hough transform. We again use the OpenCV implementation and the parameters for Hough transform are found using experimentation. We use value 1 for rho, theta and threshold. Min. line length to be found is 5 pixels and max. line gap is 5.

 Within this step we work on smoothing the various line segments returned by Hough transform to show single conjoined line as left and right lane. To do this we look at every line segment returned by Hough transform and decide if it is a part of left lane or right based on its slope value i.e. > or < 0. The we calculate the average slope and average x-y coordinates for each lane. While doing this we restrict only the line segments where the slope was between certain values. This helped filter out some outliers. One we have the average slope and coordinates we calculate the intersection with the y-coordinates of the ROI at the top and bottom of the image for both the lanes. We use this cooridnates to draw the lines.

![alt text][image7]

The final output is shown by highligthing the detected lanes on the original image.
![alt text][image2]




### Shortcomings:
Following shortconings can be noticed with this approach:
1. The values for parameters at various stages, i.e. Gaussian blur, edge detection, line detection, slope determination for identifying left and right lane lines are hardcoded to this dataset and will not work for different images based upon lighting, curvature, resolution, camera position etc.

2. The point of view of the images is front view which makes the lanes vanish at the horizon and the lanes are not always parallel.

3. Some frames while running on video did not detect either left or right lane. This issue might be worse in some cases. 

### Possible improvements:
1.  Slopes within successive frames do not change value drasctically. The calculated slope values can be made smooth by using the knowledge of previously calculated slopes.

2. We can use better learning methods for parameters. One of them could be to use input data sets from different sources.