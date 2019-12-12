# Liver-and-Lesion_Segmentation_UNet

An automatic algorithm to segment liver and liver lesions in abdominal CT scans using deep learning, using Keras package.

The project consists of the following tasks:
1. Liver segmentation
2. Lesions segmentation

The training data set consists of 11 CT scans:

• ‘Data’ directory contains CT images converted to ‘png’ format

• ‘Segmentation’ directory contains segmentation masks:

– Pixels with value 127 indicate liver

– Pixels with value 255 indicate liver lesion

Download the data from the following link: https://drive.google.com/file/d/1lhYdOFymZSC5Gz76Zt4GzcDYc8nWaWJv/view


## Models Architecture
All the models that were implemented are based on the U-net [1] architecture and were trained from scratch, without any pre-training procedure.

1. End-to-end model based on a single slice
- In order to segment both the liver and the lesions two channels were used in the output, which represent a one-hot-encoding of the two classes. This way, one channel contains liver segments with lesion “holes” and one channel contains lesions segments only.
- The training strategy relies on the use of data augmentation to use the available annotated samples more efficiently, as the amount of available data was small.
- The augmentation parameters were chosen to create reasonable augmented images which do not exceed the slice borders:
  Rotation range: 4
  Width shift range: 0.08
  Height shift range: 0.08
  Zoom range: 0.05

2. Two-steps model based on a single slice
- A test to split the model into two separate U-net networks was made. In this case, each network performs one step of the task.

- The first network was trained to segment the full liver out of a CT image, and the second network was trained to segment a lesion from an image which contain
only the liver itself.

3. Three-Slices Model: End-to-end model based on three slices
- Inspired by [2], the input was modified to have three CT slices, the relevant slice and two adjacent slices (above and below), in order to use additional
information from z-axis.
- The new samples, containing three slices, were treated as a three channels input, in a similar way to RGB images.
- The same U-net architecture was used, so that the first layer performs a 3D.

convolution over the three slices, and the next layer behave similarly to the single-slice network.

## Post processing
- In order to refine and improve the network output CRF was used.
- CRF is a conditional random field is a discriminative statistical modelling method that is used when the class labels for different inputs are not independent. For image
segmentation, the class label for each pixel depends on the label of its neighboring pixels.
A CRF provides a class prediction for a pixel considering the pixel label as well as the labels of all pixels which are defined as neighbors of the pixel.
- Using CRF, a smoothed version of the input was recieved. That means that certain spikes or very small contours of a certain label are vanished and yield a cleaner result. That fits medical tasks such as organ segmentation, which usually does not include sharp spikes or very small elements that are only a few pixels wide.



[1] O. Ronneberger, P. Fischer, T. Brox, "U-net: Convolutional networks for biomedical image segmentation", Proc. Int. Conf. Medical Image Comput. Comput.-Assisted Intervention, pp. 234-
241, 2015.

[2] Ben-Cohen A, Diamant I, Klang E, Amitai M, Greenspan H (2016) Fully convolutional network for liver segmentation and lesions detection. In: Proceedings of the international workshop on large-scale annotation of biomedical data and expert label synthesis. Springer, pp 77–85.
