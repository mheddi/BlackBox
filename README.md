# Interpretation of BlackBox classifiers #

# Introduction
Machine learning studies the algorithms that can learn and make predictions. These algorithms can be either interpretable or not. Interpretable classifiers are really shallow: a decision tree for instance, but once we try to go deeper in the feature generation process (to increase the performance) it becomes more difficult to understand the classifier’s behaviour. One of the most famous black box approaches is ”Deep Learning” which is a branch of machine learning that uses deep neural networks to perform feature representations. Deep learning is used in a wide range of applications: computer vision, speech recognition, medicine, finance and others. 
While dealing with a classifier, we need to understand its behaviour by studying and inferring an introspection explanation and a justification explanation. The introspection explanation is a technical explanation aiming to explain the computational side. On the other hand, the justification explanation provides a qualitative explanation that links the input to the output. 
This project is focused on the different techniques that we can use to understand the behaviour of these ”black box” algorithms for a none machine learning expert (a doctor for instance), the study will concern only the justification explanation.
# Understanding the black box
In this section, we will present a pipeline that we believe would help understanding the errors generated by a black box classifier. The goal here is to provide an answer to the question: why is my classifier making this error? 
## Data set:
Before training any classifier, we have to make sure that the training data set is perfectly suitable for a classification task (or clustering). Data shift and data leakage are two popular problems that corrupt our training process. Data shift is the case when the joint distribution of the input and output changes between the training set and the test set. 
P(x,y) = P(x|y)P(y) = P (y|x)P (x) 
Where x is the input and y is the output, if any value from the above probabilities changes then it will change the joint probability. Sample selection bias and imbalanced data sets are two popular types of data shifting. When deploying a classifier in the real world applications, it can perform badly compared to the results of the test set. This can be due to the problem of data leakage. The latter is a really popular phenomenon due to the creation of additional unexpected information in our data set: including test data into the training data, adding the ground truth into the test set... 

## Metrics:
Metrics selection is really important in understanding the behaviour of a classifier. The most used one is accuracy. However, the latter is not a good metric in a wide range of tasks. In the medical field for instance (disease detection), due to the problem of imbalanced data sets the classifier will perform a high accuracy by only classifying all the inputs as a non disease which will generate a lot of false negative detection. In this task, it is more relevant to measure the classifier performance by looking at the sensitivity, specificity, recall, precision, f1-score, AUC... 

## Neural networks: Explaining the output
As mentioned in the introduction, this project is restricted in providing a ”justification explanation” to a non machine learning expert. Thus we can only use the inputs and the outputs to understand the classification process. By generating several crops from the input, we can feed them into the neural network and evaluate the output of each crop compared to the one of the original image. In computer vision for instance, if the crops labels are: door, window and wall then it makes a lot of sense that the image label would be a house rather than a horse. 

## Neural networks: Understanding errors
In this section we will give a research track aiming to correct misclassified inputs. Generating crops from the input and feeding them into the neural network, can help us to understand the misclassified inputs. In the following we will deal with images (computer vision) but the proposed ideas should work for any other type of data. 
Let’s consider the case where the classifier predicts ”grass” for a ”dog” as the one in figure 3. After generating the crops from the original image, we can calculate the distance between the output of each crop from a specific layer (the last layer for instance, before the softmax function) and the output of the original image from the same layer . Now let’s imagine that the lowest distance is the one between the original image and its crop that represents the grass. One hypothesis can be that the neural network focused its activations on the grass part leading to a misclassification. 
One possible suggestion to correct this problem would be to generate the target crop (the crop that represents most the picture. In the case of figure 3 the dog face for example) for a set of the training data and finetune the network by adding an additional term in the loss function. This term would represent the distance between the crop output from a specific layer and the original image output from the same layer (we can use the last layer before softmax, or find the optimal one using a cross validation). By adding the distance term in the loss function we will be minimizing it during the training process and then encouraging the neural network to learn sophisticated patterns from the crop. 

# Application
Here we use AlexNet for a simple implementation (after downloading a trained model from the internet: [a link](http://www.cs.toronto.edu/%7Eguerzhoy/tf alexnet/bvlc alexnet.npy)). 
The output given by AlexNet for the poodle illustrated in figure 4 is ”miniature poodle”. The figure 4 illustrates the picture of the poodle and 9 crops taken for its original image. By looking at the crops, it makes a lot of sense to think that the crop containing the poodle’s face is the one carrying the most important information. Table 1 illustrates the distances between the crops in figure 4 and the original image. the face’s dog (Crop Id 7) is the one with the lowest distance as expected. We can see than the dog’s back and the dog’s tail comes afterwards, which makes a lot of sense. 
Crop Id 
distance to original image 
|1|100| 
|2|112.6|
|3|111.5|
|4|97.4|
|5|118.6|
|6|114.2|
|7|82.6|
|8|113.6|
|9|111.7|

# Conclusion
This repo presented some ideas aiming to explain the outputs of complex machine learning classifiers. Most of the techniques use crops generated form original images to infer an explanation for the prediction. A better option might be to process an image segmentation, then use the different segments instead of the crops. 



 
