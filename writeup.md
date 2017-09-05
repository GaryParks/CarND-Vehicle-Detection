##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/data.png
[image2]: ./output_images/hog.png
[image3]: ./output_images/windows.png
[image4]: ./output_images/heat.png
[image5]: ./output_images/thresh.png
[image6]: ./output_images/label.png
[image7]: ./output_images/out.png
[image8]: ./output_images/out_all.png
[image9]: ./output_images/all.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the second through fourth cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and ended up choosing the current set due to the SVM accuracy and speed.  I treied a larger number of orientations and pixeles per cell, but the computatition time was quite long and the accuracy actually decreased by a few percent.

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

Cell 2 has the function extract_features. This function, given an array of images will get spatial, histogram and HOG features and return them as a list of feature vectors.

In cell 5th vehicle and non-vehicle images were read and then used extract_features, followed by preprocessing using StandardScaler. Then the data was split.  80% for training and 20% for testing.  Then a LinearSVC was trained.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The sliding windows functions were defined in code block 6.

I decided to linit the search area from y=350 to y=650 and and use a variety of scales (1.0, 1.5, 2.0, 2.5, 3.0).

Here is an example of the 1.5 scale of the first test image.

![alt text][image3]

Then a heatmap function was used.

![alt text][image4]

This was followed by a threshold of 2 to remove flase positives.

![alt text][image5]

Then `scipy.ndimage.measurements.label()` was used.

![alt text][image6]

This resulted in the following bounding boxes.

![alt text][image7]

I then used 
####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I defined a find_cars_pipeline function in code block 13 to analyze each image.

Ultimately I searched on 5 scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  This was followed by a heat map and a labeling function to give me one box each detected car.  Here are some example images:

![alt text][image8]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_out.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

### Here is a frame's heatmaps across all scales and the output of `scipy.ndimage.measurements.label()`, followed by the resulting bounding boxes:

![alt text][image9]


---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I had a few issues with trying to set the parameters.  Even when the accuracy of the model was high, I still ended up with very unstable bounding boxes.  The green road sign was even included in the  bounding box for a few moments.

I would really like to further restrict my search areas in the future relative to the scale I am using.  The current function still takes too long to analyze each image.  I would also want to improve the bounding box stability as well. 

