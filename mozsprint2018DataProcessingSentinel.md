# Suggested workflow for using satellite data to classify objects for mapping

## Mozsprint 2018 

### Hosted in University of Manchester, Allan Turning building by Rachael Ainsworth

*Contrubutors*

* Sophie Ashcroft - ms.sophie.ashcroft@gmail.com
* Rohini Joshi - rohini.joshi@manchester.ac.uk
* Elliott Polzin - elliott.polzin3@gmail.com
* Robert Lyon - robert.lyon@manchester.ac.uk
* Therese Cantwell - therese.cantwell@manchester.ac.uk
* Joe Fennell - joseph.fennell@manchester.ac.uk

*Document target user*

This is written for individuals confindent and compitent in Machine Learning technologies and systems.

### Document Goal

After reading into the context and scope of the Crowd2Map project we found that with the time and human constraints we are under, the best way for us to contribute would be to create a set of recommendations for how in the future contributors can automate mapping by using a combination of satallite and data processing technologies.

Below is a proposed workflow of what data should be collected, from where, how to train a K-means algorithm on it and what this proposed solution will deliver. Finally there is further information on how this process has worked in practise on a map of Peurto Rico.

It is our recommendation that this good first step step in starting to automate the mapping process, we also are consious to note that we believe that the human and community interaction provided by the on-the-ground mappers is invaluable - how this will change in the future due to automation we don't know, that's up to you!

It is also our opinion that the human-validated data is not the optimal source of data to use to draw your map from, given the varied and abundant data taken from satallites, used with both supervised and unsupervised ML algorithms will produce a more reliable map.

## Workflow option 1

*Goal*

Ideally you'd start with this data as the objects are already labled, this data can be used to train the k-means algoithm which would then be applied to classify the new (uncliassified) images taken from Sentinal.

Please beer in mind that this may not be complete

*Workflow*

1. Download Sentinel or Crowd2Map data
  * http://www.esa.int/Our_Activities/Observing_the_Earth/Copernicus/Sentinel-2 - provides information on the type of data Sentinel collects.
  *  https://github.com/Fernerkundung/awesome-sentinel - provides information on how best to work with Sentinel. A curated list of awesome tools, tutorials and APIs related to data from the Copernicus Sentinel Satellites.
  * It is unclear on how to download validated Crowd2Map data.
  * What Sentinel data is nedded - for this you need a description from an astronomer/domain expert, explaining it to whomever is doing the ML work (e.g. what is polarisation, why is it important, what is the difference between optical/infrared data). This will save a lot of time. Will also allows an expert to choose appropriate methods.
  * *Python script for reading in Sentinel files* - [Classes for reading in sentinel files from the .SAFE format and outputting as
np arrays.](https://bitbucket.org/joe-fennell/sentinelpy_v1/src/master/) by Joe Fennell

2. Pre-process the data
  * Convert to usable file format - a format compatible with python.
  * Dimensionality reduction if necessary.
  * Segment images (group pixels) to reduce the amount of inputs (faster classification, and less noise in feature - [This is probably also required to build a reasonably effective classifier.](http://scikit-image.org/docs/dev/api/skimage.segmentation.html)

3. Using validated data we can decide which features (e.g. relative brightness at different wavelengths) are best to determine what the objects in the images are.
  * Obtain some ground truth (labelled data). Verify the labels, how they were obtained, their correctness (i.e. mis-labeling possible?), consider whether or not the labels are sufficient (perhaps more/fewer needed). 
Consider the transfer of information from other datasets. It may not be possible to do this without breaking the i.i.d. Assumption, but it’s worth pursuing
Google API’s should probably be explored as a potential source of relevant data which can be used to train our algorithms..
  * An example of this is in the Puerto Rico case (below) and why these features worked to classify water / ground from Sentinel images.

4. K-means cluster the (pre-processed) data.
  * https://www.coursera.org/learn/machine-learning/lecture/93VPG/k-means-algorithm
  * http://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html 
  * Use the validated data to train the K-means clustering
  * Apply resulting model to other image areas that require classification - This should identify Regions Of Interest (ROI) that can be used for easier / quicker manual classification


### Machine learning challenge

*Core machine learning challenge*

The automatic segmentation of image data (radar or otherwise) and the application of classification labels to those segments. We can think of this as two distinct problems. The segmentation problem can be tricky, but there are lots of solutions out there to learn from.
Robert suggests looking at a good survey paper which summarises possible approaches to unsupervised image segmentation [“Image segmentation evaluation: A survey of unsupervised methods”, Hui Zhang et. al., 2008, ](https://ac.els-cdn.com/S1077314207001294/1-s2.0-S1077314207001294-main.pdf?_tid=55d77098-545b-438a-80f3-151c740e7272&acdnat=1526036269_9eb5f42fe279e75b3a6d20c69b8ade73 ).

Perhaps the biggest challenge with respect to the segmentation problem, is quantifying what makes a good segmentation - especially if we have no ground truth (is there a ground truth?). For instance what makes a good segmentation for this problem? Some criteria to consider taken from the paper reference above, (i) Regions should be uniform and homogeneous with respect to some characteristic(s), (ii) Adjacent regions should have significant differences with respect to the characteristic on which they are uniform, (iii) Region interiors should be simple and without holes (iv) Boundaries should be simple, not ragged, and be spatially accurate. Unsupervised learning would appear to be the best approach for achieve this for now. Perhaps we can think about measures of colour, texture, shape etc. The classification problem is in some respects easier to solve. If we have labelled segments, we can train all sorts of classification algorithms - supervised, semi-supervised etc. Before we consider classification ,we should probably get the segmentation correct. 

 No time to explain, but this paper is also interesting Image Segmentation [U-Net: Convolutional Networks for Biomedical, Olaf Ronneberger, Philipp Fischer, and Thomas Brox](https://arxiv.org/pdf/1505.04597.pdf), and is talked about [here](https://blog.deepsense.ai/deep-learning-for-satellite-imagery-via-image-segmentation/ ). Suggested course - based on the paper linked above, I would recommend following up on the U-Net model, originally written for biomedical imaging. 


### Pre-existing software for gathering satallite data

Using what exists already will help the project run lean.

* [Google Earth Engine](https://earthengine.google.com/)
* [IBM pairs](https://ibmpairs.mybluemix.net/)


### Working example  

This example project from Therese and Joe automatically identifying areas of water / ground in Puerto Rico uses  image segmentation and K-means clustering and data taken from Sentinel satallite.

*Authors*

* Therese Cantwell   
* Joe Fennell

*Example repos*

https://github.com/TMCantwell/JEDI/blob/master/Image_segmetaion_Kmeans_tutorial.ipynb
https://github.com/joe-fennell/hacknight_1 

This example tutorial from Therese and Joe  automatically identifying areas of water / ground in Puerto Ricouses a lot of similar techniques needed to those listed above.


### Futher reading

For more information on K-means algorithms and how to use them can be found within the above online course from [Coursera.](https://www.coursera.org/learn/machine-learning)


### Questions for the future

* Is there an API to work with the manually validated data?
* How do we download the validated data already collected from Crowd2Map?
* And / or could we work with Google Maps data to cross-check the automatic classifications?

### Thanks for reading! Happy classifying :crystal_ball:
