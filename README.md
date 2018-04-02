

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Implement a sliding-window technique and use the trained classifier to search for vehicles in the images.
* Run the pipeline on a video stream (test on test_video.mp4 and later implement on full video project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[car]: ./images/car.png
[non-car]: ./images/non-car.png
[car_hog]: ./images/car_hog.png
[hog]: ./images/hog.png
[cars_boxes]: ./images/cars_boxes.png
[cars_boxes1]: ./images/cars_boxes1.png
[video1]: ./project_video.mp4


---
### Writeup / README


### Histogram of Oriented Gradients (HOG)

The code for this step is contained in the code cell number 6 of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][car]![alt text][non-car]

I then explored different color spaces and different `get_hog_features()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `get_hog_features()` output looks like.

Here is an example from cars group using the `RGB` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][car_hog]![alt text][hog]

#### Choice of HOG parameters.

I tried various combinations of parameters and I came back to 9 orientations, 8 pixels per cell and 2 cells per block for my final implementation.

#### Training the classifier using the selected HOG features 

I trained a linear SVM using all the three feature sets of each image, i.e. Hog features, color histogram features, and spacial binning features. I chose the YCrCb color space because RGB gave me more false positives during my trials with the sample images.

### Sliding Window Search

The function find_cars in code cell number 16 includes the sliding window search algorithm. This code steps through the image in both horizontal and vertical directions. The image scale selected (1.5) is sufficient for image recognition. 


#### Working of the pipeline

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][cars_boxes]![alt text][cars_boxes1]

---

### Video Implementation


Here's a [link to my video result](./project_video_out.mp4)


#### Implementation

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual local peaks in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

The choice of threshold for the heatmap decided the false positives. I chose a threshold of 2 for the heatmaps for reasonable accuracy of detection. But this also means that in some of the frames the car does not get detected, simply because it was not crossing the threshold detection from multiple scales of the image detection algorithm.

However, the choice of parameters resulted in a reasonably working pipeline.


---

### Discussion

This project had taken the most time for me, simply because I was trying to run the models with lowest possible data, i.e. with only one channel or hog features, and nothing else. My experiments with every possible color space had some errors on the final video output. I was trying to run the pipeline from my PC. 

I then moved the pipeline execution to AWS instance and increased the feature list, 3 channel hog features, and color histogram, spacial bins. It was not difficult to get it working at this point of time. I had to tune some hyperparameters to get it up and working.

Overall, this project has been an exhausting exercise, more intensive than the behavioural cloning project, and taught me a valuable lesson that more training data results in better model. But at the same time, this pipeline runs very slowly. This is not really optimized for real-time runs.
