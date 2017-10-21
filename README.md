

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[car]: ./images/car.png
[non-car]: ./images/non-car.png
[car_hog]: ./images/car_hog.png
[hog]: ./images/hog.png
[cars_boxes]: ./images/cars_boxes.png
[cars_boxes1]: ./images/cars_boxes1.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.    

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the code cell number 6 of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][car]![alt text][non-car]

I then explored different color spaces and different `get_hog_features()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `get_hog_features()` output looks like.

Here is an example from cars group using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][car_hog]![alt text][hog]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and I came back to 9 orientations, 8 pixels per cell and 2 cells per block for my final implementation. These numbers seemed more intutive from the class exercise.

#### 3. Describe how (and identify where in your code) you trained classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using all the three feature sets of each image, i.e. Hog features, color histogram features, and spacial binning features. I chose the YCrCb color space because RGB gave me more false positives during my trials with the sample images.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The function find_cars in code cell number 16 includes the sliding window search algorithm. This code was taken from the class assignment. This code steps through the image in both horizontal and vertical directions. The image scale selected (1.5) is sufficient for image recognition. 


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][cars_boxes]![alt text][cars_boxes1]

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my video result](./project_video_out.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual local peaks in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

The choice of threshold for the heatmap decided the false positives. I chose a threshold of 2 for the heatmaps for reasonable accuracy of detection. But this also means that in some of the frames the car does not get detected, simply because it was not crossing the threshold detection from multiple scales of the image detection algorithm.

However, the choice of parameters resulted in a reasonably working pipeline.


---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

This project had taken the most time for me, simply because I was trying to run the models with lowest possible data, i.e. with only one channel or hog features, and notting else. My experiments with every possible color space had some errors on the final video output. I was trying to run the pipeline from my PC. 

I then moved the pipeline execution to AWS instance and increased the feature list, 3 channel hog features, and color histogram, spacial bins. It was not difficult to get it working at this point of time. I had to tune some hyperparameters to get it up and working. In most cases, I used the code from the class exercises, and also the parameters from the class exercises. This is mainly because I find that the values are working. Although I experiemnted to use different set of values, I finally came back to the working set of values. 

Overall, this project has been an exhausting exercise, more intensive than the behavioural cloning project, and taught me a valuable lesson that more data results in better model. But at the same time, this pipeline runs very slowly. This is not really optimized for real-time runs.
