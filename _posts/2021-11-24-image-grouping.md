---
title: 'Image Grouping Using OpenCV and Scikit Learn'
date: 2021-11-24
permalink: /posts/2021/11/image-grouping/
tags:
  - k-means
  - unsupervised-learning
  - image-grouping
---

---

## INTRODUCTION

Definitely, the very first time you saw google photos segregate pictures into separate groups, you were just as completely freaked out and astounded at the same time as were we. How can the machine group your images into different sections like photos containing cars, mountains, cats and even recognize faces, right? So this became the inspiration of our project.

So, after some intense research, we and our superb mentors in SRA came up with 2 algorithms to be used, one for extracting features and other for clustering images. And so, we decide to get our feet wet with unsupervised clustering algorithms.

> So what do we aim to achieve through this project?

This project aims at creating an image grouping algorithm. The algorithm should be able to group similar images based on the extracted features. We have used ORB algorithm for extracting features and Scikit K-means clustering algorithm to clusterize images. So it reads images from a folder and applies ORB to all images to give its descriptors and finds optimum no. of groups(K) then applies K-means on descriptors and paste images to their respective cluster folder.

![aa5a9ffd5a354e3e99af6343568107db](https://user-images.githubusercontent.com/82367556/143062685-647a8bfe-6c2f-445a-b02c-2362fd379fa9.png)

## ALGORITHM USED

- ### ORB
    
    Oriented FAST and rotated BRIEF (ORB) is a fast robust local feature detector that can be used in computer vision tasks like object recognition or 3D reconstruction. It is based on the FAST keypoint detector and a modified version of the visual descriptor BRIEF (Binary Robust Independent Elementary Features).
    
- ### K-Means
    
    K-means is an unsupervised classification algorithm, also called clusterization, that groups objects into k groups based on their characteristics. The grouping is done minimizing the sum of the distances between each object and the group or cluster centroid. The distance usually used is the quadratic or euclidean distance.
    

## METHODS AND STAGES OF PROGRESS

### Step 1:  Preprocessing

To group images, it requires processing the images under test. Image processing is the operation of converting images into computer readable data. To perform the necessary operations we processed the images using the OpenCV python library which allows us to read images in matrix format.

**Code Implementation**

```Python
filenames = []
totalImages = 0
images = []

def load_images_from_folder(folder): #loading all images from a specified folder n storing it in an array of images
    global totalImages
    global filenames
    for filename in os.listdir(folder):
        filenames.append(filename)
        img = cv2.imread(os.path.join(folder,filename), flags = cv2.IMREAD_GRAYSCALE)
        if img is not None:
            images.append(img)
            totalImages +=1
    return images
main_path = input("Enter path to your image directory: ") #Specifing the path of images
allImages = load_images_from_folder(main_path)

kp =[] #Storing keypoints of all images
des = [] ##Storing descriptors of all images
c = 0
```

### Step 2: Feature extraction via Orb

Now we need to extract features from the image to understand the contents of the image. A feature / keypoint is a piece of information about the content of an image; typically about whether a certain region of the image has certain properties.These features are stored in the computer memory in the form of descriptors. The descriptor contains the visual description of the patch and is used to compare the similarity between image features. So, by applying openCV ORB to all images, we stored all keypoints and descriptors of images in the list.

ORB builds on the well-known FAST keypoint detector and the BRIEF descriptor. Both of these techniques are attractive because of their good performance and low cost.

***Fast(Features from Accelerated and Segments Test)***

Given a pixel p in an array fast compares the brightness of p to surrounding 16 pixels that are in a small circle around p. Pixels in the circle are then sorted into three classes (lighter than p, darker than p or similar to p). If more than 8 pixels are darker or brighter than p then it is selected as a keypoint. So keypoints found by fast gives us information of the location of determining edges in an image.

![ff54c61aa6a5406ca3d7e0eea9e36cc3](https://user-images.githubusercontent.com/82367556/142811202-d244c15c-09a2-4e97-b722-875e9cc0adb2.png)

However, FAST features do not have an orientation component and multiscale features. So the orb algorithm uses a multiscale image pyramid. An image pyramid is a multiscale representation of a single image, consisting of sequences of images all of which are versions of the image at different resolutions.

***Brief(Binary robust independent elementary feature)***

Brief takes all keypoints found by the fast algorithm and converts it into a binary feature vector so that together they can represent an object. Binary features vector also know as binary feature descriptor is a feature vector that only contains 1 and 0. In brief, each keypoint is described by a feature vector which is 128–512 bits string.

Brief start by smoothing the image using a Gaussian kernel in order to prevent the descriptor from being sensitive to high-frequency noise. Then brief select a random pair of pixels in a defined neighborhood around that keypoint. The defined neighborhood around a pixel is known as a patch, which is a square of some pixel width and height. The first pixel in the random pair is drawn from a Gaussian distribution centered around the keypoint with a stranded deviation or spread of sigma. The second pixel in the random pair is drawn from a Gaussian distribution centered around the first pixel with a standard deviation or spread of sigma by two. Now if the first pixel is brighter than the second, it assigns the value of 1 to corresponding bit else 0.

![48b538eae846477ebd20771ebec909e7](https://user-images.githubusercontent.com/82367556/142811773-1aa6a6e3-0151-4f4b-b1b2-f727e107fab0.png)

**Code Implelmentation**

```python
for i in range(totalImages):
  totalFeaturePoints = 800
  dimensions = (totalFeaturePoints*32) +1 #Descriptors return a 32 bit 
  orb = cv2.ORB_create(nfeatures= totalFeaturePoints, edgeThreshold=0,fastThreshold=0)
  temp_kp, temp_des = orb.detectAndCompute(allImages[i], None) #Gets keypoints and descriptors
  a, b= temp_des.shape
  if (a == totalFeaturePoints): 
    kp.append(temp_kp)
    des.append(temp_des)
    des[c] = des[c].reshape(a*b)
    des[c] = np.append(des[c], 0)
    c +=1
  else : #Kmeans needs uniform data, so we need to remove images if they give feature points
    filename = filenames[i]
    os.remove(os.path.join(main_path,filename))
    totalImages -= 1
    filenames.remove(filename)
```

### Step 3: K-Means clustering

So, after getting descriptors of all images, we need to cluster them by using K-Means clustering. First we need to find no. of clusters so we are doing that by applying the elbow method using distortions. K-means clustering is a method of vector quantization, originally from signal processing, that aims to partition n observations into k clusters in which each observation belongs to the cluster with the nearest mean.

The algorithm has three steps:

- **Initialization**: Once the number of groups, k has been chosen, k centroids are established in the data space, for instance, choosing them randomly.
- **Assignment of objects to the centroids**: Each object of the data is assigned to its nearest centroid.
- **Centroids update**: The position of the centroid of each group is updated taking as the new centroid the average position of the objects belonging to said group.

![image](https://user-images.githubusercontent.com/82367556/142813403-1db4ac6f-b9b0-4d3f-9113-c215bbc4e950.png)

**Elbow Method for optimal value of k in KMeans**

A fundamental step for any unsupervised algorithm is to determine the optimal number of clusters into which the data may be clustered. The Elbow Method is one of the most popular methods to determine this optimal value of k.

We now define the following:-

- Distortion: It is calculated as the average of the squared distances from the cluster centers of the respective clusters. Typically, the Euclidean distance metric is used.
- Inertia: It is the sum of squared distances of samples to their closest cluster center.

We iterate the values of k from 1 to 20 and calculate the values of distortions for each value of k and calculate the distortion and inertia for each value of k in the given range.

![cdd926642aca4b3cab41eec34d92864d](https://user-images.githubusercontent.com/82367556/142815048-f243a96d-03f1-45d9-8bb8-ce38c6e1a88d.png)

**Code Implementation**

```python
distortions = []
inertias = []
mapping1 = {}
mapping2 = {}
K = range(1, 20) #Kept a limit of 20 clusters, so it will predict no. cluster from 1 to 20

for k in K:
    # Building and fitting the model
    kmeanModel = KMeans(n_clusters=k, random_state=0, ).fit(X)
    # kmeans.labels_
    distortions.append(sum(np.min(cdist(X, kmeanModel.cluster_centers_,
                                        'euclidean'), axis=1)) / X.shape[0])
    inertias.append(kmeanModel.inertia_)
 
    mapping1[k] = sum(np.min(cdist(X, kmeanModel.cluster_centers_,
                                   'euclidean'), axis=1)) / X.shape[0]
    mapping2[k] = kmeanModel.inertia_
k = []

for key, val in mapping1.items():
    k.append(val) #stroing all values of distortions

x = range(1, len(k)+1)
kn = KneeLocator(x, k, curve='convex', direction='decreasing') #finding elbow point using Distortion
k = kn.knee
if k == None:
  k = int(input("You need to enter no. of cluster, our code couldnt predict :( ")) #It couldn't predict cluster when datasets contains similar images
print("No. of clusters: ", str(k))
kmeans = KMeans(n_clusters=k, random_state=0, ).fit(X) #SkLearn Kmeans
kmeans.labels_
```

### Step 4: Making directories for cluster and pasting image to its respective directory(cluster)

Now we have all images labeled(cluster number), we can group them by making separate directories using os library and copy paste images from the main folder to their respective directories by shutil.

**Code Implementation**

```Python
cluster = np.zeros(dimensions*k).reshape(k,dimensions)
for i in range(k):
  r= random.randint(0,(n-1))
  for j in range(dimensions):
    cluster[i][j]= p[r][j]
cluster_labels = kmeans.labels_ #Sroeing all labels in this

def make_all_cluster_folder():
  for i in range(k):
    clusterFolderName = "Cluster" + str(i)
    path= os.path.join(main_path, clusterFolderName)
    os.mkdir(path)

def get_target_folder(cluster_label_value):
  clusterFolderName = "Cluster" + str(cluster_label_value)
  target_folder = os.path.join(main_path,clusterFolderName)
  return target_folder

def paste_images_to_folder(original_folder):
  i = 0
  for filename in filenames:
    original_path = os.path.join(original_folder,filename)
    target_path = os.path.join(get_target_folder(cluster_labels[i]), filename)
    shutil.copyfile(original_path, target_path, follow_symlinks=True)
    i += 1

make_all_cluster_folder()
paste_images_to_folder(main_path)
```
## PROJECT SCHEMEA
![ImageGrouping](https://user-images.githubusercontent.com/82367556/143077903-1fe52db1-c5d3-497f-b69f-631c6483d04f.png)


## CONCLUSION

In this simple yet effective way, we were able to understand and implement our very own super lite version of image classifier using an unsupervised clustering algorithm, the one which we see and use every day in google photos.

## ACKNOWLEDGEMENTS AND RESOURCES

- [SRA VJTI](https://github.com/SRA-VJTI) Eklavya 2021
- Our mentors [Mann Doshi](https://github.com/MannDoshi) and [Prathamesh Tagore](https://github.com/meshtag) for their guidance throughout this project
- [K-means Research Paper](https://ieeexplore.ieee.org/document/5453745)
- [ORB Research Paper](https://ieeexplore.ieee.org/document/6126544?denied=)
- [Optimum K](https://www.geeksforgeeks.org/elbow-method-for-optimal-value-of-k-in-kmeans/)
- [Our github repo](https://github.com/shahpratham/Image_Grouping)
- For more resources refer References section in [report](https://docs.google.com/document/d/13rm8PvZJsEX0KtAoTCxCmSNBWjynkaadBWpaNet914Y/edit?usp=sharing "report/report.pdf")

## AUTHORS

- [Pratham Shah](https://github.com/shahpratham)