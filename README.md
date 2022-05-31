## Repo summary/presentation (UNDER CONSTRUCTION - to be completed by 5 June, 2022)

This repo focuses the use of convolutional autoencoders as feature extraction tools when compared to hand-crafted features obtained by the pyAudioAnalysis Python library, in a scenario where the available training dataset consists of "a few" labelled points and "many" unlabelled points (of the same data source).

The unlabelled points are used via their spectograms to train a symmetric convolutional autoencoder which learns feature representations of the data. In each iteration of the experiment we consider potions (percentages) of the labelled training data to train two SVM classifiers of C=10 on their hand-crafted and autoencoder features respectively. Finally, the learned classifiers are tested on hand-crafted and code representations of a test dataset, where we keep the weighted F1 scores.

For the purposes of our experiments we consider data from the valence class of the [MSP Podcast](https://ecs.utdallas.edu/research/researchlabs/msp-lab/MSP-Podcast.html) dataset. They are divided into "negative", "neutral" and "positive" (sub)classes.

In short, this repo consists of the following :
- main_ntbk.ipynb : The main repo (Jupyter) notebook that contains sections describing each experiment step.
- autoencoders.py : Contains hand-written symmetric convolutional autoencoder architectures. They take as input fixed size spectograms of (74,200) size and are parametrized with respect to the latent (bottleneck) dimension of the autoencoder.  
- autoencoder_training_tuning.ipynb : Colab notebook that contains the training and tuning of the architectures included in the autoencoders.py file.
- "files" folder : Contains training and test data features pickle files and the final encoder model that we use for code feature extraction.

## **********************************************************************************************

Below one may find a detailed presentation of the main points and results to consider while reading the "main_ntbk.ipynb" file. 

From now one we refer to the hand-crafted features of pyAudioAnalysis as "high-level features" and the features extracted at the central layer of the autoencoder as "code features".

The "main_ntbk.ipynb" notebook is structured as follows :

#### 1) MSP Podcast
   In this study we divide the data into 3 speaker independent sets; named Training (labelled) set, Unlabelled set and Test (labelled) set. This section is dedicated into properly constructing them.
   
   - In the first place we build a "training_folder" divided into 3 subfolders of 400 audio points each (one subfolder for each valence class). The purpose of this set is to represent the "small" group of labelled training data.
   
   - Similarly, we build an "unlabelled_folder" divided into 3 subfolders of 3300 points each. These points will be used later without their labels and represent the "large" group of unlabelled training data. Note that only for this section we considered their labels as well in an attempt to have a "balanced" split of the data.
    
   - Finally, we build the "test_folder" divided into 3 subfolders of 500 points each. These points are used for testing via their high-level and code representations.
    
#### 2) Exploring the signal size of the audio files - Elimination of short audio files
   The motivation for this section comes from the observation that the data of all 3 sets will be fed, via their spectograms, into the convolutional autoencoder. More specifically the unlabelled data will train the autoencoder while the training and test data will get their predicted representations from the learned encoder. 
   
   However, due to the different duration of the data we will also get spectograms of different x-axis shape and thus it is not possible to feed them directly to the network, as it requires fixed input sizes. 
   
   So, based on the above, in this section we study the files' duration a bit deeper and attempt to fix a reasonable minimum signal size per file such that files of smaller sizes can be ignored and files of larger signal sizes will be treated via multiple minimum signal size subparts. At the same time we monitor how many points are eliminated by each set and class and by how much class balance is disturbed in each set. 
   
   For these purposes, we construct the signal boxplots of the unlabelled and training sets and conclude that the threshold of 30K as minimum signal size could reasonably satisfy the above constraints. This threshold corresponds to about 3.67secs (sampled at frequency 8000).
   
   <p float="left">
     <img src="https://user-images.githubusercontent.com/55101427/171047834-f0ffdb08-3605-4cbe-bddb-b658f0287c71.png" height="250" width="500" />
     <img src="https://user-images.githubusercontent.com/55101427/171047881-5d08b548-3d7b-4404-b8df-21b02ce7c1b5.png" height="250" width="500" />
   </p>
   
   By eliminating signals of size less than 30K samples, the number of the experiment contributing points is re-arranged as follows :
   - training set: 350 "negative", 362 "neutral" and 319 "positive" points (1031 in total)
   - unlabelled set: 2781 "negative", 2674 "neutral" and 2622 "positive" points (8077 in total)
   - test set: 432 "negative", 421 "neutral" and 383 "positive" points (1236 in total)
     (the test set numbers are computed in detail in section 7)

#### 3) Unlabelled data : Spectograms
   In this section the spectograms of the unlabelled data are constructed. As explained above, we focus only in data with signal size of at least 30K samples and cut the signal in 30K size parts. This results in spectograms of fixed shape (74, 200). Note that the tail subparts of each signal, sized below 30K, are also eliminated by the process. 
   
   Eventually, we get 11300 spectograms corresponding to the 8077 unlabelled data. They are saved in the "spectograms_of_unlabelled_data.pickle" file.

#### 4) Convolutional Autoencoder model
   At the beginning of this section the reader is referred to two external sources, the "autoencoders.py" file and the Colab "autoencoder_training_and_tuning.ipynb" notebook. 
   
   The first one, includes multiple hand-written symmetric convolutional autoencoder architectures parametrized with respect to their latent dimension size (i.e. the size of the "bottleneck" layer at the center of the symmetric autoencoder). The architectures are build considering different number of convolutional layers, kernels and strides.
   
   On the other hand, the Colab notebook is used for training these architectures with the spectograms of section 3. It consists of the "tune_autoencoder" function which runs 8 (2^3) training experiments per autoencoder architecture for different latent dimensions (bottleneck dimension), Adam learning rates and training batch sizes. Based on further experiments conducted we stick to the "Binary Crossentropy" loss and the "Adam" optimizer, because choices such as "MSE" loss and "SGD" optimizer did not give robust structures under many different tuning combinations. The function eventually plots the loss history of each hyperparameter combination and stores the respective encoder model. We highlight that due to Colab's limited RAM sources it was very time consuming possible to tune for more hyper-values.
In the end of the notebook, we choose the best autoencoder of each architecture and compare them based on their loss values and their complexity.
   
   Returning back to "main_ntbk.ipynb", in this short section, we load the "optimal" encoder (over all tuned above) in order to use it as code feature extractor for the training and test data. 

#### 5) Training labelled data : High Level Features, Spectograms & Code Features

   (explain how we save one pickle data for each)
   
   training_data_hlf.pickle // training_data_spectograms.pickle // training_data_cf.pickle

#### 6) The SVM classifier (Tuning C)

#### 7) Test labelled data : High Level Features, Spectograms & Code Features

   (explain how we save one pickle data for each)
   
   test_data_hlf.pickle // test_data_spectograms.pickle // test_data_cf.pickle

#### 8) The experiment


------
Variations to check in future experiments
- change min signal size
- handle differently signals of smaller size (for instance introduce padding)
- tune for more hyper values
