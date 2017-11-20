The student clearly explains each layer of the network architecture and the role that it plays in the overall network. The student can demonstrate the benefits and/or drawbacks different network architectures pertaining to this project and can justify the current network with factual data. Any choice of configurable parameters should also be explained in the network architecture.

The student shall also provide a graph, table, diagram, illustration or figure for the overall network to serve as a reference for the reviewer.


After some experimentation, i decided to go with a 5 layer network.

 I first started with 2 layers of convolutions on the input data. Then, I applied the encoder block 5 times. Inside each encoder block is the same structure: downsampling layer, followed by 2 convolutions. In order to downsample, I use a convolution of size 2 with same padding in order to decrease input size by 2 with each encoder block. The main purpose of this is to extract features at different scales of the image. So every time I downsampled the image with an encoder block, I also increase the depth of the filter.

The final output of the encoder layer is 5x5x500. It then feeds into a 1x1 convolution layer where the stride and kernel size is 1. This step helps in combining and reducing the depth of the filter.

Finally, it runs through 5 decoding layers to get the final output of the system. Each decoding layer consists of a bilinear upsampling layer which increases the height and width by 2 followed by 2 convolution layers. Each decoding layer is also concatenating input from a previous encoding layer to get more information about the precious inputs. This last step helps scale the height and width back up to get spatial information about the features. 



--------------------------------------------------------------------------


Epoch: I wanted to find a good balance of getting the most out of the training set, not overfitting, and training time. I played around with this parameter, increasing it slowly each time. I found that for my network structure, the validation error would cap out around 10-15 epochs so I set it to 20 to give it some buffer.

Learning rate: Once again I had to make a tradeoff between training time and accuracy. Althought because O was strugging to hit 40%, I opted for  a lower learning rate. I ended up choosing .005 which is a bit on the slow side without being too slow to train.

Batch Size: Since my network depth was pretty deep, I chose a smaller batch size in order to prevent overfitting and increase training times. After some experimentation, I found that a batch size of 50 was a good balance.

--------------------------------------------------------------------------

1 by 1 Convolutions: A 1 by 1 convolution is just a convolution with a kernel size of 1x1 and a stride of 1. This retains the height and width of the layer and applies a spatially contained convolution of the gathred features. The main usage of 1 by 1 convolutions is to change the depth of the filter. In the case of my network, I apply it after the encoding stage, right after gathering all the features at each scale. This is when the filter depth is the greatest so it is most effective to reduce it here to reduce the load on the decoding stage.

Fully connected layer: This is a layer in which every input is connected to every output. This layer is generally used for classification as it doesn't retain spatial information. It is also expensive as it doesn't take advantage of weight sharing. For these reasons, fully connected layers are usually applied at the and of a classification network to analyze the collected high level features collected from convolutional layers.

--------------------------------------------------------------------------

