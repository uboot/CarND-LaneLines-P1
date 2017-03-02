**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

My pipeline consists of the following 5 steps:

#### Color selection

In the color selection step only pixels with with high red and green values are selected. This leads to the selection of white and yellow pixels.

#### Conversion the to grayscale

Next, the image is converted to grayscale.

#### Edge detection

For the edge detection the image is first blurred with a Gaussian kernel and then the Canny edge detector is applied.

#### Masking

Next, all pixels outside a given polygon are masked out, i.e. they are set to black. The polygon consists of four corners and is designed to resemble the area in the image where the current lane is visible. The coordinates of the polygon are computed relative to the image size which means different image resolutions should be automatically supported in this step.

#### Line detection

Then the lane lines are detected in the masked edge image. First, the coordinates (i.e. the two endpoints) of all line segments in the image are computed. Then, the set of line segments is split into line segments with positive and negative slope. In general, this corresponds to the right and left lane lines. Next, the line segments for each side are "merged" into one line. This is done by computing the affine functions `x = k*y + d` which coincide with the each line segment. Horizonal lines (`k` would be infinity) are discarded. Then the weighted average over the `k`'s and `d`'s of all line segments of a class (positive and negative) is computed. The weights are given by the length of the original line segment, i.e. long line segments are more significant than short ones. Finally, there are two averaged functions of the type `x = kmean*y + dmean`. These functions are used to compute the endpoints of the two "merged" lane lines by evaluating the functions for suitable values of `y`. These values are the bottom of the image and the top of the polygon used for masking. In the last step the lane lines are drawn into the line image.

### Potential shortcomings

The color selection in the first step is not robust with respect to exposure changes. If the image is overally darker the lane lines would still be visible but not selected. This is effect is severed by the relatively large threshold of 200.

The masking operation assumes a fixed position of the horizont. This can change if the vehicle moves on a curved (in the sense of going up or down) road.

For computing the average of the right and left lane segments a relativly simple statistic is used. In particular it is not robust against outliers, i.e. wrongly detected line segments which are far away from the lane lines can significantly influence the result.

### Possible improvements

To overcome the dependence on the overall brightness of the image one could try to normalize the image contrast (e.g. `cv2.equalizeHist()`).

One should use more robust statistics for the computation of the averaged line segments, e.g. the trimmed mean. Moreover, `k` is not the best representation of the line slop as it tends to infinity for horizontal lines. Representing the slope by its angle should lead to more natural mean slope values.

Finally, for the detection in frame sequences it would be benificial to the consider the position of the lines in the previous frames to improve the robustness of the lane detection. One could consider the line segments of previous frames in the computation of the mean line segments (multiplying them with weights which increase with the age of the frame).
