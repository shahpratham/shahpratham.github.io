---
title: "Vision-Beyond-Limits"
excerpt: "Extracted and classified buildings based on damage levels from the post-earthquake imagery training set, assigning color codes to each category. Implemented multi-label classification model for accurate classification in our network training process. <br/><img src='/images/p3-vbl.png'>"
collection: projects
---

## About
Extracted and classified buildings based on damage levels from the post-earthquake imagery training set, assigning color codes to each category. Implemented multi-label classification model for accurate classification in our network training process

## Methodolgy
We tired solving this problems in 5 steps:

- **Get mask**

First we need to get mask ready. To do that, we have used skimage and matplotlib libraires on our JSON data to from multi-label masking which will used for traing the model for damage detection.

- **Define model**

We defined Unet model which takes `n_classes`, `IMG_HEIGHT`, `IMG_WIDTH`, `IMG_CHANNELS` as argument. We have used `softmax` as activation function as in our case its multi-class classification.

- **Encode data**

Now we will encode our labels using `LabelEncoder` from sklearn. Encoder add values from 0-5 (as we have 6 classes) to our label. Further split our data set into train image and test image in 9:1 ratio.

- **Train model**

Next, we complied the model using `Adam` , optimizer, `focal-loss`  as loss function and `accuracy` metric and used sample weights as data was imbalanced. We tried different things while training the model like changing the number of epochs , changing the weights that we have defined , also we have changed the image size to see if there is any change in the accuracy or not.

- **Test model**

After training the model we saved the model to use it while testing. Finally we are ready to test our model, plot accuracy and loss graph and get our values of `IoU`, `Precison` and `Recall`. You can check the results that we got in [Results](#results) section.


## Github Link

<a href="https://github.com/Neel-Shah-29/Vision-Beyond-Limits" target="_blank">https://github.com/Neel-Shah-29/Vision-Beyond-Limits</a>