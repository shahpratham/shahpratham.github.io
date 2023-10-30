---
title: "Image Grouping"
excerpt: "Developed an image grouping algorithm that effectively segregated similar images based on extracted features through the implementation of clustering techniques.<br/><img src='/images/p4-ig.png'>"
collection: projects
---

## About
Developed an image grouping algorithm that effectively segregated similar images based on extracted features through the implementation of clustering techniques.

## Methodolgy
elect assorted images of single label test subjects like for example cats & cars. Apply the clustering algorithm to find images of cats in one folder & cars in a seperate folder. It reads images from a folder and applies ORB to all images to give its descriptors and find optimum value of k and applies K-means on descriptors and paste images to their respective cluster folder.

- **Preprocessing**

To group images, it requires processing the images under test. Image processing is the operation of converting images into computer readable data. To perform the necessary operations we processed the images using the OpenCV python library which allows us to read images in matrix format.

- **Feature extraction via Orb**

Now we need to extract features from the image to understand the contents of the image. A feature / keypoint is a piece of information about the content of an image; typically about whether a certain region of the image has certain properties.These features are stored in the computer memory in the form of descriptors. The descriptor contains the visual description of the patch and is used to compare the similarity between image features. So, by applying openCV ORB to all images, we stored all keypoints and descriptors of images in the list.

- **K-Means clustering**

So, after getting descriptors of all images, we need to cluster them by using K-Means clustering. First we need to find no. of clusters so we are doing that by applying the elbow method using distortions. K-means clustering is a method of vector quantization, originally from signal processing, that aims to partition n observations into k clusters in which each observation belongs to the cluster with the nearest mean.

- **Making directories for cluster and pasting image to its respective directory(cluster)**

Now we have all images labeled(cluster number), we can group them by making separate directories using os library and copy paste images from the main folder to their respective directories by shutil.


## Github Link

<a href="https://github.com/shahpratham/Image_Grouping" target="_blank">https://github.com/shahpratham/Image_Grouping</a>