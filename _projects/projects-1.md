---
title: "Multi-Cam-ReID"
excerpt: "Developed a robust multi-object tracking system with custom tracking algorithms, including advanced velocity modeling for enhanced performance in occlusion scenarios. <br/><img src='/images/p1-multicam.gif'>"
collection: projects
---

## About
Developed a robust multi-object tracking system with custom tracking algorithms, including advanced velocity modeling for enhanced performance in occlusion scenarios

## Methodolgy

### IOU Tracker
*Step 1: Calculating IOU Values*

First we will calculate IOU values by passing bbox of detections and tracks to `get_iou function`, which returns a matrix in which each cell represents IOU value for corresponding detection and track.

*Step 2: Finding the matching detections and tracks*

After getting IOU matrix we will use hungarian algorithm for finding the matches between corresponding tracks and detections.

Hungarian algorithm is a graph algorithm which solves the assignment problem and returns the list of index of matching tracks and detections.

After getting list of matching tracks and detections we will make umatched tracks and unmatched detections list and add tracks and detections which does not match. We will also add tracks and detections who have IOU value less than a particular threshold in unmatched tracks and detections.

*Step 3: Update bbox and returning matching tracks*

Now, we have list of matched detections and tracks, unmatched tracks and detections. We will update bbox of existing tracks with new matching detections. 

Unmatched detections will be the list in which object will be appearing first time. So we will add a new track for all unmatched detections. and, for Unmatched tracks the list will be of object which did not appeared in current frame and will increment the `miss` counter and call `delete_track` to check if `miss` passed `max_age` parameter.

Finally we will return the tracks.

### IOU-Predict Tracker

So here the approach is exactly the same as the IoU predict, the only difference is in bbox of trackers, so instead of using the tracker's bbox for calculating the IoU metric with detections, we will be using our predicted bbox which will be an addition of the average sum of their offset of the previous bbox's centroid and also considering the change in bbox area should change proportionately.

We have declared `hiscount` as the number of the previous bbox to be stored in `history`. We are firstly converting bbox format from [x1, y1, x2, y2] to [x, y, w, h] where x and y are bbox centroid co-ordinate and w and h are width and height. So, we are calculating the offset of the centroid from the previous bbox centroid with the current bbox and adding that offset to our predicted bbox. And the same thing with the area, so we are calculating the area ratio of the previous bbox with the current bbox and multiplying with the same ratio in our predicted bbox. And to make it more robust, instead of just storing the previous bbox, we can store a list of the previous bbox, which is stored in `history`(the default size chosen is 5).

## Github Link
<a href="https://github.com/amanchhaparia/Multi-Cam-ReID" target="_blank">https://github.com/amanchhaparia/Multi-Cam-ReID</a>