---
title: 'Introduction to Image Data'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- Where can I find image data to train my model?
- How much data do you need for Deep Learning?
- How do I plot image data in python?
- How do I prepare image data for use in a CNN?
- What is one hot encoding?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Identify sources of image data
- Write code to plot image data
- Understand the properties of image data
- Prepare an image data set to train a CNN
- Know how to perform one-hot encoding

::::::::::::::::::::::::::::::::::::::::::::::::

## Where can I find image data?

Deep Learning requires extensive training using example data which shows the network what output it should produce for a given input. One common application of Deep Learning is classifying images. In this workshop our network will be trained by being “shown” a series of images and told what they contain. Once the network is trained it should be able to take another image and correctly classify its contents.

In some cases you will be able to download an image dataset that is already labelled and can be used to classify a number of different object like we saw with the CIFAR dataset. Other examples include:

- MNIST database - 60,000 training images of handwritten digits (0-9)
- ImageNet - 14 million hand-annotated images indicating objects from more than 20,000 categories. ImageNet sponsors an annual software contest where programs compete to avieve the highest accuracy
- MS COCO - >200,000 labelled images used for object detection, instance segmentation, keypoint analysis, and captioning

In other cases, you will need to create your own set oflabelled images. For image classification the label applies to the entire image; object detection requires bounding boxes, and instance or semantic segmentation require each pixel to be labelled.

There are a number of different software that can be used to label your dataset, including:
- VGG Image Annotator (VIA)

::: How much data do you need for Deep Learning?
The rise of Deep Learning is partially due to the increased availability of very large datasets. But how much data do you actually need to train a Deep Learning model? Unfortunately, this question is not easy to answer. It depends, among other things, on the complexity of the task (which you often do not know beforehand), the quality of the available dataset and the complexity of the network. For complex tasks with large neural networks, we often see that adding more data continues to improve performance. However, this is also not a generic truth: if the data you add is too similar to the data you already have, it will not give much new information to the neural network.

In case you have too little data available to train a complex network from scratch, it is sometimes possible to use a pretrained network that was trained on a similar problem. Another trick is data augmentation, where you expand the dataset with artificial data points that could be real. An example of this is mirroring images when trying to classify cats and dogs. An horizontally mirrored animal retains the label, but exposes a different view.
:::::::::::::::::::::::::::::::::::::::::::::::

## Plotting image data in python

## Image Dimensions

## RGB vs Greyscale

## Split data into training and test set
Finally, we will split the dataset into a training set and a test set. As the names imply we will use the training set to train the neural network, while the test set is kept separate. We will use the test set to assess the performance of the trained neural network on unseen samples. In many cases a validation set is also kept separate from the training and test sets (i.e. the dataset is split into 3 parts). This validation set is then used to select the values of the parameters of the neural network and the training methods. For this episode we will keep it at just a training and test set however.

To split the cleaned dataset into a training and test set we will use a very convenient function from sklearn called train_test_split. This function takes a number of parameters:

- The first two are the dataset and the corresponding targets.
- Next is the named parameter test_size this is the fraction of the dataset that is used for testing, in this case 0.2 means 20% of the data will be used for testing.
- random_state controls the shuffling of the dataset, setting this value will reproduce the same results (assuming you give the same integer) every time it is called.
- shuffle which can be either True or False, it controls whether the order of the rows of the dataset is shuffled before splitting. It defaults to True.
- stratify is a more advanced parameter that controls how the split is done. By setting it to target the train and test sets the function will return will have roughly the same proportions (with regards to the number of penguins of a certain species) as the dataset.


## One-hot encoding
A neural network can only take numerical inputs and outputs, and learns by calculating how “far away” the species predicted by the neural network is from the true species. When the target is a string category column as we have here it is very difficult to determine this “distance” or error. Therefore we will transform this column into a more suitable format. Again there are many ways to do this, however we will be using the one-hot encoding. This encoding creates multiple columns, as many as there are unique values, and puts a 1 in the column with the corresponding correct class, and 0’s in the other columns.

TBC

## Image augmentation

TBC

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Inline instructor notes can help inform instructors of timing challenges
associated with the lessons. They appear in the "Instructor View"

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge 

## Challenge 1: Can you do it?

What is the output of this command?

```r
paste("This", "new", "lesson", "looks", "good")
```

:::::::::::::::::::::::: solution 

## Output
 
```output
[1] "This new lesson looks good"
```

:::::::::::::::::::::::::::::::::


## Challenge 2: how do you nest solutions within challenge blocks?

:::::::::::::::::::::::: solution 

You can add a line with at least three colons and a `solution` tag.

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::
