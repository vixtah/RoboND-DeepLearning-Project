
# Model Architecture

[image1]: ./model.JPG
![alt text][image1]

The network architecture used here is the encoder/decoder architecture. This architecture is the best fit for our goal of tracking because it's able to extract features at different scales and use weight sharing to take advantage of positional invariance.

I first started with 2 layers of convolutions on the input data. Then, I applied the encoder block 5 times. Inside each encoder block is the same structure: downsampling layer, followed by 2 convolutions. In order to downsample, I use a convolution of size 2 with same padding in order to decrease input size by 2 with each encoder block. The main purpose of this is to extract features at different scales of the image. So every time I downsampled the image with an encoder block, I also increase the depth of the filter.

The final output of the encoder layer is 5x5x512. It then feeds into a 1x1 convolution layer where the stride and kernel size is 1. This step helps in combining and reducing the depth of the filter.

Finally, it runs through 5 decoding layers to get the final output of the system. Each decoding layer consists of a bilinear upsampling layer which increases the height and width by 2 followed by 2 convolution layers. Each decoding layer is also concatenating input from a previous encoding layer to get more information about the precious inputs. This last step helps scale the height and width back up to get spatial information about the features.


--------------------------------------------------------------------------

# Hyperparameters

Epoch: I wanted to find a good balance of getting the most out of the training set, not overfitting, and training time. I played around with this parameter, increasing it slowly each time. I found that for my network structure, the validation error would cap out around 10-15 epochs so I set it to 20 to give it some buffer.

Learning rate: Once again I had to make a tradeoff between training time and accuracy. Althought because O was strugging to hit 40%, I opted for  a lower learning rate. I ended up choosing .005 which is a bit on the slow side without being too slow to train.

Batch Size: Since my network depth was pretty deep, I chose a smaller batch size in order to prevent overfitting and increase training times. After some experimentation, I found that a batch size of 50 was a good balance.

--------------------------------------------------------------------------

# Layer types

1 by 1 Convolutions: A 1 by 1 convolution is just a convolution with a kernel size of 1x1 and a stride of 1. This retains the height and width of the layer and applies a spatially contained convolution of the gathred features. The main usage of 1 by 1 convolutions is to change the depth of the filter. In the case of my network, I apply it after the encoding stage, right after gathering all the features at each scale. This is when the filter depth is the greatest so it is most effective to reduce it here to reduce the load on the decoding stage.

Fully connected layer: This is a layer in which every input is connected to every output. This layer is generally used for classification as it doesn't retain spatial information. It is also expensive as it doesn't take advantage of weight sharing. For these reasons, fully connected layers are usually applied at the and of a classification network to analyze the collected high level features collected from convolutional layers.

--------------------------------------------------------------------------

# Encoding/Decoding

Encoding images is useful for extracting features at different scales as well as preserving spatial information about the features. It should be used to take when the subject can be of variable size and position within an image because the encoder takes advantage of weight sharing to establish locational invariance.

Decoding images is useful for upsampling images to a desired size and we want to extract positional information in our output.

--------------------------------------------------------------------------

# Limitations

This model and data wouldn't work well for other objects because it was trained only with images of people. The model has implicitly learned to analyze and extract human features which wouldn't be useful for classifying and tracking a car.  We could however extend the model to fit more objects if we add more data to the training set which includes other objects like dogs or cars and extend the filter depth at each layer.

