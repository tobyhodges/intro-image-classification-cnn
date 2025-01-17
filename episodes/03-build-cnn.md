---
title: "Build a Convolutional Neural Network"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- What is a (artificial) neural network (ANN)?
- How is a convolutional neural network (CNN) different from an ANN?
- What are the types of layers used to build a CNN?
- How do you monitor the training process?
- What is underfitting?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Understand how a convolutional neural network (CNN) differs from an artificial neural network (ANN)
- Explain the terms: kernel, filter
- Know the different layers: dense, convolutional, pooling, flatten
- Explain what underfitting is; how you detect underfitting; and ways to address underfitting

::::::::::::::::::::::::::::::::::::::::::::::::

## Neural Networks
A neural network is an artificial intelligence technique loosely based on the way neurons in the brain work. A neural network consists of connected computational units called neurons. Each neuron ...

- has one or more inputs, e.g. input data expressed as floating point numbers
- most of the time, each neuron conducts 3 main operations:
    - take the weighted sum of the inputs
    - add an extra constant weight (i.e. a bias term) to this weighted sum
    - apply a non-linear function to the output so far (using a predefined activation function)
- return one output value, again a floating point number

![](fig/03_neuron.png){alt=''}

Multiple neurons can be joined together by connecting the output of one to the input of another. These connections are associated with weights that determine the 'strength' of the connection, the weights are adjusted during training. In this way, the combination of neurons and connections describe a computational graph, an example can be seen in the image below. In most neural networks neurons are aggregated into layers. Signals travel from the input layer to the output layer, possibly through one or more intermediate layers called hidden layers. The image below shows an example of a neural network with three layers, each circle is a neuron, each line is an edge and the arrows indicate the direction data moves in.

![The image above is by Glosser.ca, [CC BY-SA 3.0], via Wikimedia Commons, [original source]](fig/03_neural_net.png){alt=''}

Neural networks aren't a new technique, they have been around since the late 1940s. But until around 2010 neural networks tended to be quite small, consisting of only 10s or perhaps 100s of neurons. This limited them to only solving quite basic problems. Around 2010 improvements in computing power and the algorithms for training the networks made much larger and more powerful networks practical. These are known as deep neural networks or Deep Learning

::::::::::::::::::::::::::::::::::::::::: callout
Concept: Why deep learning is possible and what infrastructure is best suited to deep learning
Systems with high quality GPUs and/or HPCs if available. [Comment: I feel this is important to note, in order to make it clear that anyone attempting to run neural networks on a standard laptop will quickly reach the upper limit of capacity. By setting this expectation clearly in the course, it could help prevent people from trying to do everything neural net related on their machines and becoming disenfranchise with ML as a result]
:::::::::::::::::::::::::::::::::::::::::::::::::

## Convolutional Neural Networks

A convolutional neural network (CNN) is a type of artificial neural network (ANN) that is most commonly applied to analyze visual imagery. They are designed to recognize the spatial structure of images when extracting features.

### Step 4. Build an architecture from scratch or choose a pretrained model

Now we will build a neural network from scratch, and although this sounds like a daunting task, with Keras it is actually surprisingly straightforward. With Keras you compose a neural network by creating layers and linking them together.

Let's look at our network from the introduction:

```python
# define the inputs, layers, and outputs of a convolutional neural network

#inputs_intro = keras.Input(shape=train_images.shape[1:])

#x_intro = keras.layers.Conv2D(50, (3, 3), activation='relu')(inputs_intro)
#x_intro = keras.layers.Conv2D(50, (3, 3), activation='relu')(x_intro)
#x_intro = keras.layers.Flatten()(x_intro)

#outputs_intro = keras.layers.Dense(10)(x_intro)
```

### Parts of a neural network

Here we can see there are three main components of a neural network:  

CNN Part 1. Input Layer
CNN Part 2. Hidden Layers
CNN Part 3. Output Layer

#### CNN Part 1. Input Layer

The Input in Keras gets special treatment when images are used. Keras automatically calculates the number of inputs and outputs a specific layer needs and therefore how many edges need to be created. This means we need to let Keras now how big our input is going to be. We do this by instantiating a not the.Input class and pass it a tuple that indicates the dimensionality of the input data.

In our case, the shape of an image is defined by its pixel dimensions and number of channels:

```python
# recall the shape of the images in our dataset
print(train_images.shape)
```
```output
(50000, 32, 32, 3) # number of images, image width in pixels, image height in pixels, number of channels (RGB)
```

```python
# Input layer of 32x32 images with three channels (RGB)
#inputs_intro = keras.Input(shape=train_images.shape[1:])
```

#### CNN Part 2. Hidden Layers

The next component consists of the so-called hidden layers of the network. The reason they are referred to as hidden is because the true values of their nodes are unknown - this is the black box. 

In a CNN, the hidden layers typically consist of dense, convolutional, reshaping (e.g., Flatten), and pooling layers. Check out the [Layers API] section of the Keras documentation.

##### **Dense layers**

A **dense** layer has a number of neurons, which is a parameter you can choose when you create the layer. When connecting the layer to its input and output layers every neuron in the dense layer gets an edge (i.e. connection) to **all** of the input neurons and **all** of the output neurons.

- **Dense**: Just your regular densely-connected NN layer 
- defined by the keras.layers.Dense class

![](fig/03-neural_network_sketch_dense.png){alt=''}

This layer is called fully connected, because all input neurons are taken into account by each output neuron. The number of parameters that need to be learned by the network is thus in the order of magnitude of the number of input neurons times the number of hidden neurons.

##### **Convolutional Layers**

A **convolutional** layer transforms the input image in order to extract features from it. 

- **Conv2D**: 2D convolution layer (e.g. spatial convolution over images) 
- defined by the keras.layers.Conv2D class

With image classification, note that our input dimension is now quite high (even with small pictures of 32x32 pixels), we have:

```python
dim = train_images.shape[1] * train_images.shape[2] * train_images.shape[3]
print(dim)
```
```output
3072
```

::::::::::::::::::::::::::::::::::::: challenge

## Number of parameters
Suppose we create a single Dense (fully connected) layer with 100 hidden units that connect to the input pixels, how many parameters does this layer have?

:::::::::::::::::::::::: solution

Each entry of the input dimensions, i.e. the shape of one single data point, is connected with 100 neurons of our hidden layer, and each of these neurons has a bias term associated to it. So we have 307300 parameters to learn.
```python
width, height = (32, 32)
n_hidden_neurons = 100
n_bias = 100
n_input_items = width * height * 3
n_parameters = (n_input_items * n_hidden_neurons) + n_bias
print(n_parameters)
```
```output
307300
```
We can also check this by building the layer in Keras:
```python
inputs_ex = keras.Input(shape=dim)
outputs_ex = keras.layers.Dense(100)(inputs_ex)
model_ex = keras.models.Model(inputs=inputs_ex, outputs=outputs_ex)
model_ex.summary()
```
```output
Model: "model"
_________________________________________________________________
Layer (type)                 Output Shape              Param #
=================================================================
input_1 (InputLayer)         [(None, 3072)]            0
_________________________________________________________________
dense (Dense)                (None, 100)               307300
=================================================================
Total params: 307,300
Trainable params: 307,300
Non-trainable params: 0
```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

We can decrease the number of units in our hidden layer, but this also decreases the number of patterns our network can remember. Moreover, if we increase the image size, the number of weights will ‘explode’, even though the task of recognizing large images is not necessarily more difficult than the task of recognizing small images.

The solution is that we make the network learn in a ‘smart’ way. The features that we learn should be similar both for small and large images, and similar features (e.g. edges, corners) can appear anywhere in the image (in mathematical terms: translation invariant). We do this by making use of a concepts from image processing that precede Deep Learning.

A **convolution matrix**, or **kernel**, is a matrix transformation that we 'slide' over the image to calculate features at each position of the image. For each pixel, we calculate the matrix product between the kernel and the pixel with its surroundings. A kernel is typically small, between 3x3 and 7x7 pixels. We can for example think of the 3x3 kernel:

```
[[-1, -1, -1],
 [0, 0, 0]
 [1, 1, 1]]
```
This kernel will give a high value to a pixel if it is on a horizontal border between dark and light areas. Note that for RGB images, the kernel should also have a depth of 3.

In the following image, we see the effect of such a kernel on the values of a single-channel image. The red cell in the output matrix is the result of multiplying and summing the values of the red square in the input, and the kernel. Applying this kernel to a real image shows that it indeed detects horizontal edges.

![](fig/03_conv_matrix.png){alt=''}

![](fig/03_conv_image.png){alt=''}

In our convolutional layer our hidden units are a number of convolutional matrices (or kernels), where the values of the matrices are the weights that we learn in the training process. The output of a convolutional layer is an 'image' for each of the kernels, that gives the output of the kernel applied to each pixel.

:::::::::::::::::::::::::::::::::::::: callout

## Playing with convolutions

Convolutions applied to images can be hard to grasp at first. Fortunately, there are resources out there that enable users to interactively play around with images and convolutions:

[Image kernels explained] shows how different convolutions can achieve certain effects on an image, like sharpening and blurring.
The [convolutional neural network cheat sheet] shows animated examples of the different components of convolutional neural nets.
:::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Border pixels
What, do you think, happens to the border pixels when applying a convolution?

:::::::::::::::::::::::: solution

There are different ways of dealing with border pixels. You can ignore them, which means that your output image is slightly smaller then your input. It is also possible to 'pad' the borders, e.g. with the same value or with zeros, so that the convolution can also be applied to the border pixels. In that case, the output image will have the same size as the input image.
:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Number of model parameters
Suppose we apply a convolutional layer with 100 kernels of size 3 * 3 * 3 (the last dimension applies to the rgb channels) to our images of 32 * 32 * 3 pixels. How many parameters do we have? Assume, for simplicity, that the kernels do not use bias terms. Compare this to the answer of the previous exercise.

:::::::::::::::::::::::: solution

We have 100 matrices with 3 * 3 * 3 = 27 values each so that gives 27 * 100 = 2700 weights. This is a magnitude of 100 less than the fully connected layer with 100 units! Nevertheless, as we will see, convolutional networks work very well for image data. This illustrates the expressiveness of convolutional layers.
:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

##### **Shaping Layers: Flatten**

The third type of hidden layer used in our introductory model is a **Flatten** layer. Let's hold off on discussion this for just a moment.


#### CNN Part 3. Output Layer

Recall for the outputs we will need to look at what we want to identify from the data. If we are performing a classification problem then typically we will have one output for each potential class. We need to finish with a Dense layer to connect the output cells of the convolutional layer to the outputs for our 10 classes.

```python
# Output layer with 10 units (one for each class)
#outputs = keras.layers.Dense(10)(x)
```

## Putting it all together

So let us look again at the small network used in our introduction:

```python
# Input layer of 32x32 images with three channels (RGB)
inputs_intro = keras.Input(shape=train_images.shape[1:])

# Convolutional layer with 50 filters, 3x3 kernel size, and ReLU activation
x_intro = keras.layers.Conv2D(50, (3, 3), activation='relu')(inputs_intro)
# Second Convolutional layer
x_intro = keras.layers.Conv2D(50, (3, 3), activation='relu')(x_intro)
# Flatten layer to convert 2D feature maps into a 1D vector
x_intro = keras.layers.Flatten()(x_intro)

# Output layer with 10 units (one for each class)
outputs_intro = keras.layers.Dense(10)(x_intro)

# create the model
model_intro = keras.Model(inputs=inputs_intro, outputs=outputs_intro, name="cifar_model_intro")
```

We first store a reference to the input class in a variable 'inputs_intro' so we can pass it to the creation of our first hidden layer. Creating the convolutional layers can then be done as follows:

```python
#x_intro = keras.layers.Conv2D(50, (3, 3), activation='relu')(inputs_intro)
```

The instantiation here has 3 parameters and a seemingly strange combination of parentheses, so let us take a closer look.

- The first parameter 50 is the number of neurons we want in this layer, this is one of the hyperparameters of our system and needs to be chosen carefully. We will get back to this in the section on hyperparameter tuning.

- The second parameter is the kernel size.

- The third parameter is the activation function to use, here we choose **relu** which is 0 for inputs that are 0 and below and the identity function (returning the same value) for inputs above 0. This is a commonly used activation function in deep neural networks that is proven to work well. We will discuss activation functions in a subsequent episode.

- Next we see an extra set of parenthenses with inputs in them, this means that after creating an instance of the Conv2D layer we call it as if it was a function. This tells the Conv2D layer to connect the layer passed as a parameter, in this case the inputs.

- Finally we store a reference so we can pass it to the next layer.

Adding a second Conv2D layer we use the same arguments but change the input to be the output of the first Conv2D layer.

```python
#x_intro = keras.layers.Conv2D(50, (3, 3), activation='relu')(x_intro)
```

Now let's take a closer look at that **Flatten** layer: 

::::::::::::::::::::::::::::::::::::: challenge

## Convolutional Neural Network
Inspect the network above:

- What do you think is the function of the Flatten layer?
- Which layer has the most parameters? Do you find this intuitive?


:::::::::::::::::::::::: solution
```python
model_intro.summary()
```
```output
Model: "cifar_model_intro"
_________________________________________________________________
 Layer (type)                Output Shape              Param #   
=================================================================
 input_2 (InputLayer)        [(None, 32, 32, 3)]       0         
                                                                 
 conv2d (Conv2D)             (None, 30, 30, 50)        1400      
                                                                 
 conv2d_1 (Conv2D)           (None, 28, 28, 50)        22550     
                                                                 
 flatten (Flatten)           (None, 39200)             0         
                                                                 
 dense_1 (Dense)             (None, 10)                392010    
                                                                 
=================================================================
Total params: 415,960
Trainable params: 415,960
Non-trainable params: 0
_________________________________________________________________
```

- The Flatten layer converts the 28x28x50 output of the convolutional layer into a single one-dimensional vector, that can be used as input for a dense layer.
- The last dense layer has the most parameters. This layer connects every single output 'pixel' from the convolutional layer to the 10 output classes. That results in a large number of connections, so a large number of parameters. This undermines a bit the expressiveness of the convolutional layers, that have much fewer parameters.

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

- **Flatten**: Flattens the input. Does not affect the batch size
- defined by the keras.layers.Flatten class

## We have a model now what?

This minimal CNN should be able to run with the CIFAR-10 dataset and provide reasonable results for basic classification tasks. However, do keep in mind that this model is relatively simple, and its performance may not be as high as more complex architectures. The reason it's called deep learning is because in most cases, the more layers we have, ie, the deeper and more sophisticated CNN architecture we use, the better the performance.

How can we tell? We can look at a couple metrics during the training process to detect whether our model is underfitting or overfitting. To do that, we first need to continue with the next steps in our Deep Learning workflow, **Step 5. Choose a loss function and optimizer** and **Step 6. Train model**. We will go into more details of these steps in the next lesson, but for now we just need to run this code to access the training history:

```python
model_intro.compile(optimizer = 'adam', loss = keras.losses.SparseCategoricalCrossentropy(from_logits=True),
     metrics = ['accuracy'])
history_intro = model_intro.fit(train_images, train_labels, epochs=10, 
     validation_data=(val_images, val_labels))
```

#### Monitor Training Progress (aka Model Evaluation during Training)

It can be very insightful to plot the training loss to see how the training progresses. 

Using seaborn we can plot the training process using the history:

```python
import seaborn as sns
import pandas as pd

# convert the history to a dataframe for plotting 
history_intro_df = pd.DataFrame.from_dict(history_intro.history)

# plot the loss and accuracy from the training process
fig, axes = plt.subplots(1, 2)
fig.suptitle('cifar_model_intro')
sns.lineplot(ax=axes[0], data=history_intro_df[['loss', 'val_loss']])
sns.lineplot(ax=axes[1], data=history_intro_df[['accuracy', 'val_accuracy']])
```

![](fig/03_model_intro_accuracy_loss.png){alt=''}

This plot can be used to identify whether the training is well configured or whether there are problems that need to be addressed. When your validation loss is decreasing, the model is underfit. Underfitting occurs when the model is too simple or lacks the capacity to capture the underlying patterns and relationships present in the data. As a result, the model's predictions are not accurate, and it fails to generalize well to unseen data.

Key characteristics of an underfit model include:

- Low Validation Accuracy: This indicates that the model is not learning from the data effectively.

- Large Training Loss: The training loss (error) is high, indicating that the model's predictions are far from the true labels in the training set.

How to Address Underfitting:

- Increase the model's complexity by adding more layers or units to the existing layers.
- Train the model for more epochs to give it more time to learn from the data.
- Perform data augmentation or feature engineering to provide the model with more informative input features.

Given we intentionally started with a shallow model, let's try adding more layers! Now is a good time to discuss the pooling layer.


#### **Pooling Layers**

Often in convolutional neural networks, the convolutional layers are intertwined with **Pooling** layers. As opposed to the convolutional layer, the pooling layer actually alters the dimensions of the image and reduces it by a scaling factor. It is basically decreasing the resolution of your picture. The rationale behind this is that higher layers of the network should focus on higher-level features of the image. By introducing a pooling layer, the subsequent convolutional layer has a broader 'view' on the original image.

- **MaxPooling2D**: Max pooling operation for 2D spatial data 
- defined by the keras.layers.MaxPooling2D class

Let us create a new model that includes a pooling layer after each Conv2D layer:

```python
# pooling layer
#x_pool = keras.layers.MaxPooling2D((2, 2))(x_pool)
```

The instantiation here has a single parameter, pool_size.

- The function downsamples the input along its spatial dimensions (height and width) by taking the maximum value over an input window (of size defined by pool_size) for each channel of the input.

- The resulting output, when using the default "valid" padding option, has a spatial shape (number of rows or columns) of:
![](fig/03_shape_equation.png){alt=''}


- And again we store a reference so we can pass it to the next layer.

We will also add a second set of convolutional and pooling layers before flattening the result and passing it to an additional dense layer. 

```python
# dense layer
#x_pool = keras.layers.Dense(50, activation='relu')(x_pool)
```

The instantiation of this Dense layer has 2 parameters, the number of neurons and the activation function.

We then add our final output layer and reassemble, compile, and train the deeper model with pooling.

Putting it all together:

```python
# define the inputs, layers, and outputs of a cnn model with pooling
inputs_pool = keras.Input(shape=train_images.shape[1:])
x_pool = keras.layers.Conv2D(50, (3, 3), activation='relu')(inputs_pool)
x_pool = keras.layers.MaxPooling2D((2, 2))(x_pool)
x_pool = keras.layers.Conv2D(50, (3, 3), activation='relu')(x_pool)
x_pool = keras.layers.MaxPooling2D((2, 2))(x_pool)
x_pool = keras.layers.Flatten()(x_pool)
x_pool = keras.layers.Dense(50, activation='relu')(x_pool)
outputs_pool = keras.layers.Dense(10)(x_pool)

# create the pooling model
model_pool = keras.Model(inputs=inputs_pool, outputs=outputs_pool, name="cifar_model_pool")

model_pool.compile(optimizer = 'adam', loss = keras.losses.SparseCategoricalCrossentropy(from_logits=True), 
    metrics = ['accuracy'])

history_pool = model_pool.fit(train_images, train_labels, epochs=10, validation_data=(val_images, val_labels))

model_pool.summary()

# save pool model
model_pool.save('fit_outputs/model_pool.h5')

```
```output
Model: "cifar_model_pool"
_________________________________________________________________
 Layer (type)                Output Shape              Param #   
=================================================================
 input_1 (InputLayer)        [(None, 32, 32, 3)]       0         
                                                                 
 conv2d (Conv2D)             (None, 30, 30, 50)        1400      
                                                                 
 max_pooling2d (MaxPooling2D  (None, 15, 15, 50)       0         
 )                                                               
                                                                 
 conv2d_1 (Conv2D)           (None, 13, 13, 50)        22550     
                                                                 
 max_pooling2d_1 (MaxPooling  (None, 6, 6, 50)         0         
 2D)                                                             
                                                                 
 flatten (Flatten)           (None, 1800)              0         
                                                                 
 dense (Dense)               (None, 50)                90050     
                                                                 
 dense_1 (Dense)             (None, 10)                510       
                                                                 
=================================================================
Total params: 114,510
Trainable params: 114,510
Non-trainable params: 0
_________________________________________________________________
```

:::::::::::::::::::::::::::::::::::::: callout
## How to choose an architecture?
Even for this neural network, we had to make a choice on the number of hidden neurons. Other choices to be made are the number of layers and type of layers (as we will see later). You might wonder how you should make these architectural choices. Unfortunately, there are no clear rules to follow here, and it often boils down to a lot of trial and error. However, it is recommended to look what others have done with similar datasets and problems. Another best practice is to start with a relatively simple architecture. Once running start to add layers and tweak the network to see if performance increases. 
::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge 

## Challenge Network depth

What, do you think, will be the effect of adding a convolutional layer to your model? Will this model have more or fewer parameters? Try it out. Create a model that has an additional Conv2d layer with 50 filters after the last MaxPooling2D layer. Train it for 20 epochs and plot the results.

**HINT**: The model definition that we used previously needs to be adjusted as follows:

```python
inputs_cnd = keras.Input(shape=train_images.shape[1:])
x_cnd = keras.layers.Conv2D(50, (3, 3), activation='relu')(inputs_cnd)
x_cnd = keras.layers.MaxPooling2D((2, 2))(x_cnd)
x_cnd = keras.layers.Conv2D(50, (3, 3), activation='relu')(x_cnd)
x_cnd = keras.layers.MaxPooling2D((2, 2))(x_cnd)
# Add your extra layer here
x_cnd = keras.layers.Flatten()(x_cnd)
x_cnd = keras.layers.Dense(50, activation='relu')(x_cnd)
outputs_cnd = keras.layers.Dense(10)(x_cnd)
```

:::::::::::::::::::::::: solution 

## Output
 
```output
inputs_cnd = keras.Input(shape=train_images.shape[1:])
x_cnd = keras.layers.Conv2D(50, (3, 3), activation='relu')(inputs_cnd)
x_cnd = keras.layers.MaxPooling2D((2, 2))(x_cnd)
x_cnd = keras.layers.Conv2D(50, (3, 3), activation='relu')(x_cnd)
x_cnd = keras.layers.MaxPooling2D((2, 2))(x_cnd)
x_cnd = keras.layers.Conv2D(50, (3, 3), activation='relu')(x_cnd)
x_cnd = keras.layers.Flatten()(x_cnd)
x_cnd = keras.layers.Dense(50, activation='relu')(x_cnd)
outputs_cnd = keras.layers.Dense(10)(x_cnd)

model_cnd = keras.Model(inputs=inputs_cnd, outputs=outputs_cnd, name="cifar_model_Challenge_network_depth")
```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

With the model defined above, we can inspect the number of parameters:

```python
model.summary()
```

```output
Model: "cifar_model_Challenge_network_depth"
_________________________________________________________________
 Layer (type)                Output Shape              Param #
=================================================================
 input_7 (InputLayer)        [(None, 32, 32, 3)]       0

 conv2d_16 (Conv2D)          (None, 30, 30, 50)        1400

 max_pooling2d_10 (MaxPoolin  (None, 15, 15, 50)       0
 g2D)

 conv2d_17 (Conv2D)          (None, 13, 13, 50)        22550

 max_pooling2d_11 (MaxPoolin  (None, 6, 6, 50)         0
 g2D)

 conv2d_18 (Conv2D)          (None, 4, 4, 50)          22550

 flatten_6 (Flatten)         (None, 800)               0

 dense_11 (Dense)            (None, 50)                40050

 dense_12 (Dense)            (None, 10)                510

=================================================================
Total params: 87,060
Trainable params: 87,060
Non-trainable params: 0
_________________________________________________________________
```

The number of parameters has decreased by adding this layer. We can see that the conv layer decreases the resolution from 6x6 to 4x4, as a result, the input of the Dense layer is smaller than in the previous network.

:::::::::::::::::::::::::::::::::::::: callout
## Other types of data

Convolutional and Pooling layers are also applicable to different types of data than image data. Whenever the data is ordered in a (spatial) dimension, and translation invariant features are expected to be useful, convolutions can be used. Think for example of time series data from an accelerometer, audio data for speech recognition, or 3d structures of chemical compounds.
::::::::::::::::::::::::::::::::::::::::::::::

Adding more layers to our model should increase the accuracy of its predictions. But before we look at the training metrics for our pooling model, let's take a step back and discuss in more detail **Step 5. Choose a loss function and optimizer** and **Step 6. Train model**.

::::::::::::::::::::::::::::::::::::: keypoints 

- Artificial neural networks (ANN) are a machine learning technique based on a model inspired by groups of neurons in the brain.
- Convolution neural networks (CNN) are a type of ANN designed for image classification and object detection
- The filter size determines the size of the receptive field where information is extracted and the kernel size changes the mathematical structure
- A CNN can consist of many types of layers including dense (fully connected), convolutional, pooling, and flatten layers
- Convolutional layers make efficient reuse of model parameters.
- Pooling layers decrease the resolution of your input

::::::::::::::::::::::::::::::::::::::::::::::::

<!-- Collect your link references at the bottom of your document -->
[CC BY-SA 3.0]: https://creativecommons.org/licenses/by-sa/3.0
[original source]: https://commons.wikimedia.org/wiki/File:Colored_neural_network.svg
[Image kernels explained]: https://setosa.io/ev/image-kernels/
[convolutional neural network cheat sheet]: https://stanford.edu/~shervine/teaching/cs-230/cheatsheet-convolutional-neural-networks
[Layers API]: https://keras.io/api/layers/
