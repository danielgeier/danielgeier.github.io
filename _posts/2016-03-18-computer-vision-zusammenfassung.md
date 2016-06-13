---
layout: post
title:  "Computer Vision for Human-Computer Interaction (WS15/16, Prof. Stiefelhagen)"
date:   2016-03-17 00:00:00 +0100
categories: posts
---

Einführung
==============================

Anwendungen
-----------

-   Mensch-Maschine-/Mensch-Roboter-Interaktion
    -  Um mit Anwendern zu interagieren ist Sehen ziemlich wichtig
    -  Auch Wahrnehmung/Interpretation von Gesten, Affekt, Mimik, ...

-   Smart Houses / Assisted Living
-   Smart Cars (Blickrichtung, Aufmerksamkeit)
-   Smart Rooms (Wo ist wer im Meeting, Analyse)
-   Smart Homes (z.B. Monitoring von Patienten während dem Schlaf)
-   Assistive Technologie für Blinde

ML Basics
---------

#### Supervised

-   Bayesian Classification
-   Gaussian Mixture Models
    -   Expectation Maximization
-   Linear Discrimant Functions
-   Perceptrons
-   SVMs
    -   Soft Margin
    -   Kernel Trick
    -   Linear SVMs (much faster, can perform well on high-dimensional data,
        too)
    -   Multi-Class SVMs
    -   Tips: normalization is important, parameter selection is important
        (grid search)
-   KNN

#### Unsupervised

-   k-means
-   Agglomerative Hierarchical Clustering

#### Model Taxonomy
-   Parametric
    -   More data
-   Non-parametric
    -   More assumptions
-   Generative
    -   Model the distribution $$p(x,y)$$
    -   Can generate new data
    -   Can be converted to discriminative model with Bayes' Rule
-   Discriminative
    -   Only model $$p(x \vert y)$$
    -   Generally outperform generative models in classification tasks

#### Dimensionality Reduction
-   Curse of dimensionality
    - PCA
    - LDA

#### Receiver operating characteristic (ROC)
**TODO**

#### Principal Component Analysis (PCA)
- Find principal components (= eigenvectors of covariance matrix) of data
- Project data on the largest k however-many-dimensions-you-want vectors

#### Linear Discriminant Analysis (LDA)
Like PCA but different


Face Detection
==============

Color Based Face Detection
--------------------------

Idea: find image parts with skin color, fit face to biggest cluster/elliptic cluster/heuristics, ...

Good:

- Fast
- rotation/scale invariant
- occlusion resistant

Bad:

- color varies wildly with illumination
- cannot distinguish body parts
- skin-colored objects

### Color Spaces
-   RGB
-   HSV
-   Class Y Spaces
    -   e.g. YUV
    -   Y contains brightness
-   Perceptually uniform spaces
    -   e.g. CIE-Lab
    -   Euclidian distance can be used for color comparison
-   Chromatic Color Spaces
    -   e.g. HS (from HSV), UV (from YUV), normalized RG (from RGB)
    -   may be more robust to lighting

### Histogram (non-parametric)
Build Histogram for skin and non-skin from training data.

#### Histogram Backprojection
= Compare color of single pixel with histograms

- Fast and simple
- Sucks with multi-modal distributions (black people?)

#### Histogram Matching
= Compare color histogram of image patch with histograms (e.g. Battacharya distance)

- Better performance than backprojection
- works with multi-modal distributions
- slower

### Perceptual Grouping
- Morphological Operators: Erosion and Dilatation
- Erosion, then dilatation = opening
- Dilatation, then erosion = closing

![yellow: erosion, green: dilation](../../media/dilation-erosion.png)

### What works best?
- Bayesian (needs lots of memory), MLP, 3D Gaussian Skin+Nonskin
- Color space largely irrelevant, but results degrade with only chrominance channels

Neural Network Based Face Detection (Rowley, Baluja & Kanada)
-------------------------------------------------------------
- Sliding window (20x20) on different scales of picture
- Proprocessing with best fit linear function to correct lightning
- Apply ANN on windows as "filter for faces"
- Speed-ups:
    - Increase step size of sliding window
    - Hierarchical search

$$E = \frac{1}{2} \sum_{x \in X} (y(x) - f(x))^2$$

$$\Delta w = -\eta \Delta E (w)$$

![Pipeline of ANN Face Detection](../../media/face-detection-nn.png)

![Exampmle of best fit linear functions](../../media/face-detection-nn-linfun.png)


### Histogram Equalization
Transform each pixel $$y$$ of an image of size $$i$$, so that the cumulative distribution function $$c(i)$$ is linearized.

$$y'_i = c(i) (\max_y - \min_y) + \min_y$$

Feature-based Face Detection (Viola & Jones)
--------------------------------------------
- Sliding window
- Compute Haar-like features over each window
- use *integral image*(= summed area table)
- over 180k features/classifiers per sliding window
- Combine classifiers with (something like) AdaBoost
    - Train classifier
    - Calculate error
    - Weight classifier higher, if error is smaller
    - Weight training example higher, if false classified
    - repeat for each classifier
- very fast but still too many features
    - => Classifier Cascade
    - Multiple layers of classifiers with low false negative rate
    - Reject immediatly on a negative
    - Classify as face if window passes through all layers

![Haar features used](../../media/violajones-haar.png)

- Robust
- Fastest known face detector
- Can be trained for general objects

Face Recognition
================
- Why?
    - Accurate (Google FaceNet 2014: 95-99% -- human/superhuman performance)
    - Can use existing infrastructure (cameras)
    - non-intrusive (No physical interaction)
    - Only biometric for passive identification in 1-to-many scenarios (find a person in the airport etc.)
- Applications:
    - HCI, human-robot-interaction
    - Smart Cards (driver's license, ID,)
    - Surveillance
- Problems:
    - Changes in illuminations > changes due to face identity
    - Head pose
    - Occlusion
- Types:
    - Closed-Set Identification -- Test image person is in database
    - Open-Set Identification -- Person may not be from database
        - => False Classify / False Accept / False Reject

Feature-based FR
----------------
- Caculate features like "face width at nose position", "eyebrow thickness at eye center position"
- Classify with KNN using Mahalanobis distance

  $$d_j(x) = \sqrt{(x - \mu_j)^T \Sigma^{-1} (x-\mu_j)}.$$
  - $$\Sigma^{-1}$$ covariance matrix
  - $$\mu_j$$ average vector representing $$j$$th person

Appearance-based FR
-------------------
- holistic: process whole face
- local/fiducial: process facial features (eyes, mouth, ...) seperately

### Preprocessing
- align face with facial landmarks
- rescale and crop to face
- => removes translation, rotation and scaling factors

### Eigenfaces

#### Training
- Calculate principal components (*eigenfaces*) of training faces
- Keep k eigenfaces that correspond to the highest eigenvalues => *face space*
- For each person, calculate representation in face space
- Eigenfaces look like Casper the Ghost

#### Testing
- Project new image onto face space
- Find most likely person by distance comparison

#### View-Based Eigenspaces
- Generate a face space for each head orientation
- Decide input image's view with *distance from view space metric*
- do classification in that view's face space

#### Bayesian Face Recognition
- Nearest-neighbor doesn't exploit knowledge of critical appearance varations
- *Bayesian similarity measure* is a probabilistic measure that does this
- compares within-class (intrapersonal) variations with between-class (extrapersonal) variations
- Do that with dual PCA (intrapersonal & extrapersonal)

#### Fisherfaces
Like Eigenfaces but with LDA (maximizes between-classes seperation)

#### Modular Eigenspaces
- Classify on fiducial regions (eyes, nose, ...) instead of whole face
- Converges faster

#### Local PCA / Modular PCA
- Divide face into n subimages
- Apply PCA to each subimage

#### Problems
- Does not use class information
    - Much of the variation between images is due to illumination changes
    - => Fisherfaces
- Does not distinguish between shape and appearance
    - = degrades when head orientation must be matches
    - => Activate Shape Models (ASM)
    - => Active Appearance Models (AAM)

FR across pose
--------------

### Active Appearence Model (AAM)
- Fit *mesh* on face (w/ inverse compositional algorithm)

- Shape Model

  $$s = (x_1, y_1, \dots, x_v, y_v)^T = s_0 + \sum_{i=1}^n p_i s_i$$

- Appearence Model

  $$A(x) = A_0(x) + \sum_{i=1}^m \lambda_i A_i(x)$$

- Normalize image to frontal pose (affine transformation of mesh triangles)
- Can compare faces now (e.g. local DCT approaches (discrete cosine transform))


### 3D Face Model Fitting

FR using 3D Models
------------------
- Database of 3D Scans
- Label 7 fiducial points manually
- Fit 3D Model (shape and texture vectors) to input image

Local Feature-based FR
-------------------------
- Local analysis
- fuse outputs of local features at decision or feature level
- PCA


### Elastic Bunch Graphs
- 40 complex Gabor wavelet coefficients (a *Jet*) per pixel
- graph of n jets
- bunch graph = differents jets for different poses/appearences ("closed eye", "open eye", ...)

#### Gabor Filters
- 2D sine wave similiar to cells in the visual cortex
- Many scales, orientations
    - => PCA

### Local Binary Pattern Histogram
- Divide image into cells
- Threshold each pixel depending on cumsum of surrounding pixels
- Better than simple gradients
- Can effectively remove outliers

![typical gabor filters: 5 scales, 8 orientations](../../media/gabor.png)

### Dense Features
- Compute features for lots of overlapping patches in many scales
- Millions of features
- => Encode into compact form
    - Bag of Visual Word Model
    - Fisher encoding
        - Captures the average first and second order differences between features and the centres of a GMM
        - stack difference vectors
        - => 3.2m features -> 130k features
        - Subspace learning for further compression
        - => 130k features -> 1000 features
- State of the art performance on faces in the wild

Deep Learning
=============

Q: Why can’t the mapping between layers be linear?

A: Compositions of linear functions is linear, whole network collapses to regression.

Q: What does a hidden unit do?

A: Can be thought as a classifier or feature computer.

Q: How many layers? How many hidden units?

A: Hyper-parameter setting best done using cross-validation. In general wider and deeper networks allow for complicated “function” mappings.

Q: Why do we need many layers?

A: Data with hierarchical structure is well exploited with a hierarchical model architecture where intermediate features can be re-used.

Soft Max

Squash vector of reals to range [0, 1] so they add up to 1.


Facial Expression Recognition
=============================

I got *kind of* pressed for time here and didn't summarize the rest:

Head Pose Estimation
====================

People Detection
================

Tracking
========

Gesture Recognition
===================

Action Recognition
==================

# Protokollfragen

## Intro

- Anwendungen
    -   Mensch-Maschine-/Mensch-Roboter-Interaktion
        -  Um mit Anwendern zu interagieren ist Sehen ziemlich wichtig
        -  Auch Wahrnehmung/Interpretation von Gesten, Affekt, Mimik, ...

    -   Smart Houses / Assisted Living
    -   Smart Cars (Blickrichtung, Aufmerksamkeit, Fußgängererkennung)
    -   Smart Rooms (Wo ist wer im Meeting, Analyse)
    -   Smart Homes (z.B. Monitoring von Patienten während dem Schlaf)
    -   Assistive Technologie für Blinde
- Auf wlcher Ebene, wann und wie werden Informationen zusammengeführt
  in einem System, von verschiedenen Verfahren gesammelt. (Zb face + pose + ...)

## Face Detection
- Probleme (lighting, illumination, pose, occlusions)
- Color-based
    - parametric vs non-parametric
    - Histogram Backprojection
    - GMM
    - Bayes
    - Farbräume
- Feature-based
    - Ellipsis
    - Eigenfaces / Fisherfaces
    - ANN
    - Evaluationsmaß ROC, AUC
    - Viola & Jones
        - rotierte Gesichter? Rotierte Gesichter in Trainingsdaten verwenden.
          => Diskrete Winkel
- DCT wie Koeffizienten bekommen? (Gewichtung der Frequenzen)
- Vergleich auch z.B. was besser bei occlusion, pose variation?

## Face Recognition
- feature-based (facial features finden, zB Abstand der Augen)
  vs appearance-based (Aussehen)
- holistic (ganzes Gesicht zur Erkennung)
  vs local (lokal auf Features, z.B. PCA auf Augen, Mund,... oder Bild in
  Teilsektionen aufteilen; robuster)
- Problemstellungen (open, closed, verification)
- AAM
- DCT
- LBP
- Eigenfaces / Fisherfaces
    - PCA vs LDA
        - PCA Hauptziel? Gesichter können gut rekonstruiert werden
        - Robuster gegen illumination: Eigenfaces mit größten Eigenwerten
          wegwerfen, Bild vorher normalisieren (Histogram EQ)

## Deep Learning
- (Neues Thema? nix in Protokollen)
- ConvNets

## Facial Expression Recognition
- Ekman
- FACS
- Action Units
- HMM

## Head Pose Estimation
- ASM
- ANN
- 3D Fitting

## People Detection
- global vs local
- HOG
    - Wenn hier oben Kamera, würde HOG anschlagen? Nein, weil von Tisch verdeckt)
- ISM
- Champfer Silhouette Matching
- Mohan
- Welche Detektoren gibt es neben Harris-Corner noch?

## Tracking
- Kalman (= linear state propagation, analytisch)
- Particle (= beliebige propagation, numerisch)
    - Condensational Algorithm (Isard & Blake 1998)
    - wie Hypothesen gewichtet?
- Beide haben motion/observation model und predict-update cycle
    - motion/observation model Zeichnen
- Mean-Shift
- Foreground-Background
- Template Matching

## Gesture Recognition

## Action Recognition
- HMM
- Bag-of-Words
- Template Matching
- Space-time Interest Points
    - Besonderheit?
- Laptevs Action Features
