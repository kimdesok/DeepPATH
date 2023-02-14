# Whole Slide Image Classification

In this repository, we intend to archive some of our R&D efforts in learning and applying WSI classification techniques.  Software tools and datasets used by us will be available as our project progresses.

Whole slide image(WSI) classification technique has been mainly investigated to automatically detect the presence of malignant tissue in light microscopic histological slides.  The most attractive point of WSI classification techniques is that we do not need a pixel level annotation.  This advantage would let us explore a great deal of possibilities of achieving automatic classification of WSIs, which is one of the most important technical hurdles in computational pathology. 

Most of these techniques are based on neural nets approaches, popularly called deep learning.  Starting from convolutional neural net(CNN) applied on patch images(Part I), we are gearing toward more practical approaches such as CNNs applied on WSIs.  A straightforward adoption of CNNs for WSI classification requires a top notch computing resource with more than several hundred gigabytes memory. Although its classification accuracy is somewhat close to the ideal performance(Part II), it is limited for us to access and costly for now.  Alternatively, a relatively new approach is to use multiple instance learning(MIL) based CNNs(Part III).  These techniques do not seem to require the huge memory of the computing resource but are not as accurate as the straightforward application of the CNNs.  To achieve the comparable high accuracy, they need a vast amount of image datasets.  However, most of these requirements are achievable in terms of the accessibility of computing resources, the availability of datasets and open source software sets.

# Contents
[Part I. Patchwise image classification of lung cancers](https://github.com/kimdesok/DeepPATH/edit/master/README.md#part-i-patchwise-image-classification-of-lung-cancers)
>* Aim 1. To build a pipeline for classifying lung cancer image patches
>* Aim 2. To detect gene mutation from WSI image datasets <br>

[Part II. Whole slide image classification of lung cancers](https://github.com/kimdesok/DeepPATH#part-ii-whole-slide-image-classification-of-lung-cancers)
>* Aim 1. To classify lung cancers without pixel level annotations
>* Aim 2. To investigate the performance WSI based training in different resolutions <br>

[Part III. Multiple Instance Learning(MIL) for WSI classification](https://github.com/kimdesok/DeepPATH/edit/master/README.md#part-iii-preliminary-study-of-multiple-instance-learningmil-next-phase-development-under-preparation) (Next phase development. Under preparation)

## Part I Patchwise image classification of lung cancers

![image](https://user-images.githubusercontent.com/64822593/208830983-7c5d1077-7a3c-4618-85ff-ffc2ce92d5d8.png)

<br>Pipeline schema for dataset preparation and DL model training related to Aim 1 and 2<br><br>

### 1. Classification of lung cancers
1) To train several deep learning models that classify the patch images using the pixel level annotation
2) To compare the performance of models in terms of the size of datasets and accuracy

#### Materials and Methods
1) Among several deep learning(DL) models, Xception, Inception v3, and Resnet 50 were selected from Tensorflow Keras APIs.
2) Whole slide images(WSI) of lung cancer specimens('LUAD' and 'Normal') were downloaded from TCGA project of the NCI's GDC data portal database.  
3) Each WSI was divided into small patches and transformed to the TFRecord format along with their labels for more efficient loading by using the scripts available at [1] 
4) Transfer learning was applied to each model pretrained with the imagenet dataset [2]  In addition, each model was trained from random initial weights ('from scratch') to assess the value of the transfer learning by comparison.  
5) Upon adjusting hyperparameters for the best validation loss('val_loss'), the model was fine-tuned by setting all layers to be retrained. 

#### Results
Several predefined deep learning(DL) models such as Inception v3, Xception and ResNet 50 were used to investigate the effectiveness of transfer learning and fine-tuning techniques when applied to the histologic image classification. A dataset of whole slide images avaliable at the NCI's TCGA site was downloaded and preprocessed into small patches along with their labels.  

Transfer learning and fine-tuning of the DL models were programmed within the framework of Tensorflow's Keras libraries. 

When the training set is small, transfer learning with the pretrained weights does not seem to improve AUC values for ResNet 50 compared to the one without ('None' for weights).  Its AUC values were about 0.74 whereas the AUC values from training without weights were about 0.95. The fine tuning dramatically improved the AUC to 0.95 for the one with the weights and slightly to 0.96 for the one without(See Table 1).

When the size of the dataset was increased by two folds, Resnet 50 trained from scratch reached the AUC of 0.9521 while the one with the pretrained weights did not change much, resulting in 0.7401.  Fine tuning further improved the AUC upto 0.9781 for the ResNet 50 trained from scratch and 0.9795 for the ResNet 50 trained with the pretrained weights(See Table 1).

When the full dataset was used for Resnet 50 with the pretrained weights, the AUC increased from 0.7535 to 0.9819 after fine tuning (See Table 1).   


Table 1.  Performance data of ResNet 50 using three differently sized datasets, Inception V3 and Xception models trained by transfer learning and fine tuning

![image](https://user-images.githubusercontent.com/64822593/198612735-2343c733-0337-442d-887b-462330147d22.png)

Inception V3 resulted in the AUC of 0.9287 by its transfer learning with the full dataset that was further improved by its fine tuning upto 0.9885, while Xception resulted in the AUC of 0.9236 that was further improved upto 0.9862(See Table 1).

(The full dataset had a training set of 1024 WSIs and a validation set of 206 WSIs while the small set had 100 WSIs and 50 WSIs, respectively.  Both sets had a test set of 207 WSIs.)

Below showing the loss and accuracy changes during the training of a fine tuned ResNet50 model with the small dataset as an example.


![image](https://github.com/kimdesok/DeepPATH/blob/master/Resnet50_small_fine_tuned_plot.png)

#### Summary

This exercise showed a pretrained model with a small dataset required a fine tuning to improve the performance further.  
If the size of the dataset gets large enough, the 'from scratch' approach generates the result that could be good enough with (or without) the fine tuning.  
The accuracy of all three models were dramatically increased after application of fine tuning, reaching upto AUC 0.982 or higher. 

### 2. Detection of gene mutations from WSI datasets
1) To classify the malignant patches by using the patch image classification model obtained in the section 1.
2) To predict the gene mutation of each lung cancer case by training the multi-label classification model.

### Materials and Methods
Each individual patch image was classified by the previously trained Inception v3 model, resulting in a set of patch images representing only tumor (LUAD only in this case).  These patches were divided into training, validatation, and test data groups. The patch images in each group were transformed into the TFRecord format and labeled with ten labels according to the gene mutation data.  For the multi label classification, the last dense layer was set to have 11 nodes that represented 10 typical gene mutation classes and one bias.  Transfer learning was applied to the predefined DL model initialized with the imagenet weights.  Fine tuning was followed in the same way with the method described in the Aim 1 section.  

### Results
The same three types of deep learning(DL) models were investigated to predict gene mutations by their histologic representation in the lung cancers.  Resnet 50 model was trained with differently sized training datasets in the same manner as in the Aim 1.  Interestingly, the accuracy values were better in the model trained from the scratch (Weights labeled as None in the table below).  The improvement of the AUC was also observed in other two DL models and Inception v3, in particular, showed the higher accuracy compared to the other two DLs.  The authors reported the exactly same findings but no clear explanation of why training from the scratch had resulted in the improvement in the accuracy[3]. 

Table 2.  Performance of ResNet 50, Inception V3 and Xception models for prediction of gene mutations trained by transfer learning and fine tuning

![image](https://user-images.githubusercontent.com/64822593/209256925-c780b36b-278d-4a1e-9a28-721984179604.png)

Below showing the loss and accuracy changes during the transfer training of ResNet50 model from scratch with the small dataset as an example.

![image](https://user-images.githubusercontent.com/64822593/208837664-b0c614c0-25cd-4270-972d-5476637f4b4c.png)

### References: 
>[1] DeepPATH repositoty at Dr. N. Coudray's Github https://github.com/ncoudray/DeepPATH. <br>
>[2] Keras' developer guide https://keras.io/guides/transfer_learning/ <br>
>[3] Nicolas Coudray, Paolo Santiago Ocampo, Theodore Sakellaropoulos, Navneet Narula, Matija Snuderl, David Fenyö, Andre L. Moreira, Narges Razavian, Aristotelis Tsirigos. Classification and mutation prediction from non–small cell lung cancer histopathology images using deep learning. Nature Medicine, 2018; DOI: 10.1038/s41591-018-0177-5 https://www.nature.com/articles/s41591-018-0177-5 <br>

### Acknowledgement: NVidia's Quadro RTX 6000 was kindly provided by a high performance computing facility of NIPA(National IT Promotion Agency).

## Appendix 

###  Discussion on the DeepPATH framework

The DeepPATH framework gathers the codes that have been used to study the use of a deep learning architecture (Inception v3 from Google) to classify Lung cancer images.

For more details and references, please check:

Nicolas Coudray, Paolo Santiago Ocampo, Theodore Sakellaropoulos, Navneet Narula, Matija Snuderl, David Fenyö, Andre L. Moreira, Narges Razavian, Aristotelis Tsirigos. Classification and mutation prediction from non–small cell lung cancer histopathology images using deep learning. Nature Medicine, 2018; DOI: 10.1038/s41591-018-0177-5 https://www.nature.com/articles/s41591-018-0177-5

A. Model generation for tumor vs. normal classification
  1) Tile generation from WSI images
  2) The label of each tile (although stated that way, *may be inaccurate)
  3) Transfer learning to classify tumor vs. normal tiles 
  4) Decision scheme for patient level classification
B. Model generation for gene mutation classification
  1) Finding of frequently occurring somatic mutations in lung cancers
  2) The label of each patient case (by the most frequently occurred mutation. Make sense but it needs to be confirmed)
  3) Tranfer learning to classify the presence of gene mutation
C. Test of two models in one batch run

https://www.nature.com/articles/s41591-018-0177-5

https://www.biorxiv.org/content/early/2017/10/03/197574

The schema of the model build from the paper
![image](https://user-images.githubusercontent.com/64822593/154029375-23ba352d-3f16-4933-86f3-f9478c2f4523.png)


## Part II. Whole slide image classification of lung cancers

### 1. Whole slide image (WSI) training CNN Pipeline

### Introduction
The whole slide image(WSI) is the digital image representing the entire histologic slide captured by a high-throughput digital scanner such as Leica's Aperio scanner.  The image size of a WSI is often larger than 100,000 by 100,000 pixels reaching upto 10 GB. Each individual WSI can be easily labeled according to the corresponding clinical information readily available at the data deposit site such as the NCI's GDC data portal, for example.

![image](https://user-images.githubusercontent.com/64822593/208895067-51db8300-c49d-489a-b19a-d90a16bfc849.png)
<p align="center">
Whole Slide Image(WSI) is a tiled TIFF.  Captured from TCGA-49-4494-01Z-00-DX5.1e9e22d6-a4c9-40d1-aedb-b6cd404fe16f.svs
</p> 

It is widely known that training a deep neural network with a large WSI dataset is more practical since it does not require a time consuming pixel level annotation. However, to achieve a high classification accuracy, the magnification of WSI images should be at least 4x[1].  This requirement, in turn, limits the use of a computing resource with the memory smaller than 512 GB, for example.

The computing resource even with multiple GPUs often lacks this much memory and ends up with the out of memory(OOM) error while training.  To solve this problem, IBM developed Large Model Support(LMS) that takes a user-defined computational graph and swaps tensors from GPUs to the host memory and vice versa (For more information, visit https://www.ibm.com/docs/en/wmlce/1.6.0?topic=gsmf-getting-started-tensorflow-large-model-support-tflms-v2).  

The authors who set up this repository originally had also developed a huge model support(HMS) package, probably similar to the IBM's LMS in a nutshell [1][2].  The scripts provided by the authors let us to reproduce their results by WSI dataset preparation, DL training, inference, visualization, and statistics calculation, etc [1] (See the figure below).

![image](https://user-images.githubusercontent.com/64822593/208655851-6986f3e5-84c0-46ab-9194-b35887bf83a0.png)

Once this pipeline is explored for lung cancer datasets, it is believed to be utilized to other important human cancers such as prostate cancers or breast cancers by simply creating new configuration files (and label files in addition which is a piece of cake).

Thus, here lies the purpose of reproducing the original works of the authors: We can learn about software & hardware requirements for the application of the pipeline to other cancers.  In addition, we can identify numerous tasks that would enable us to apply this sort of the DL pipeline to the real world problems.  

List of tasks:
1. Migration to TF2.
2. Reducing the hardware requirement (or find out how to increase the computing speed). 

### Requirements

<Hardware Requirements><br>

The computing resource should provide an adequate amount of main memory space (minimal: 256 GB, recommended: 512 GB) to prevent out-of-memory(OOM) error and fully explore the utility of the huge memory support(HMS) function.  

However, a trial training could be performed by reducing the size of the WSI training image, the size of batch, and the complexity of the model basically.  However, it would result in the lower accuracy values ranging from 80 to 90%.  For the specific setup, please refer to the method section below.

<Packages><br>

The codes were tested on the environment with Ubuntu 18.04, Python 3.7.3, cuda 10.0, cudnn 7.6 and Open MPI 4.0.1.

More specifically, a set of CUDA related libraries compatible with Tensorflow version 1.15.5 should be installed as below
```
conda install cudatoolkit=10.0
conda install cudnn=7.6.5
```
Python packages were installed via a dependency management and packaging tool, poetry.  Some of packages include:

- Tensorflow v1.x (tensorflow-gpu==1.15.3)
- Horovod (horovod==0.19.0)
- MPI for Python (mpi4py==3.0.3)
- OpenSlide 3.4.1 (https://github.com/openslide/openslide/releases/tag/v3.4.1)
- OpenSlide Python (openslide-python=1.1.1)
- (optional) R 4.0.2 (https://www.r-project.org/)
- Tensorflow Huge Model Support (package provided in the author's repository called 'tensorflow-huge-memory-support')

### Methods
#### a. Datasets and Configurations
The .csv files under data_configs folder were used without any modification. A detailed description was available by the authors as in the Appendix below.  Hyperparameters were set in a YAML file under train_configs folder.  In the YAML file, the parameters of RESIZE_RATIO, INPUT_SIZE, and  NUM_UPDATES_PER_EPOCH were set appropriately to avoid the OOM error at each magnification.  Some examples are shown below.
```
#Training @1x
    RESIZE_RATIO: 0.05
    INPUT_SIZE: [5500, 5500, 3]
    NUM_UPDATES_PER_EPOCH: 80

#Training @2x
    RESIZE_RATIO: 0.1
    INPUT_SIZE: [10000, 10000, 3]
```

In order to utilize the pretrained model provided by the authors, the LOAD_MODEL_BEFORE_TRAIN and MODEL argumens were reset as below:
    
```
#Training @2x with the model pretrained @ 4X
    LOAD_MODEL_BEFORE_TRAIN: True
    MODEL: "frozenbn_resnet50"    
```
### 2. Train a Model

To train a model, the following script was run:
```
python -m whole_slide_cnn.train --config config_wholeslide_1x.yaml [--continue_mode]
```
, where `--continue_mode` is optional that makes the training process begin after loading the model weights.

To enable multi-node, multi-GPU distributed training, add `mpirun` in front of the above command, e.g.
```
mpirun -np 4 -x CUDA_VISIBLE_DEVICES="0,1,2,3" python -m whole_slide_cnn.train --config config_wholeslide_2x.yaml
```

Note) You should be at the root folder of this repository when calling the above commands.

### 3. Evaluate the Model

The model was evaluated by calling the command as below and optionally a prediction heatmap was also generated.
```
python -m whole_slide_cnn.test --config config_wholeslide_1x.yaml
```
This command generated a JSON file named `test_result.json` by default in the result directory.
The file contained the model predictions for each testing slide. To further generate the AUC values and their graphs, more tools were available, as explained in the Appendix.

Note) These tools are currently profiled for lung cancer classification and should be modified when applying to your own tasks.

### Results & Discussion

#### 1. Performance of Resnet 34 with the WSI at 1x magnification
We first tried the training with the Resnet34, initialized by the weights obtained by training with Imagenet.  The image size was set to be at 5500 x 5500 with the resize factor of 0.05, representing the magnification at 1x.  The loss and accuracy curves were plotted upon training the model through 100 epochs.  The validation accuracy reached about 0.68 with the validation loss of 0.64.  The test result is not shown (no data).
![image](https://user-images.githubusercontent.com/64822593/201547097-89a4b7f7-9218-4250-964d-1f564bb60266.png)

#### 2. Performance of Resnet 50 with the WSI at 1x and 2x magnification
We then tried the training with the Resnet50, that would show the effect of increasing the complexity of the model. The ResNet50 model was initialized by the weights of the pretrained Imagenet model. The validation accuracy reached about 0.80 with the validation loss of 0.41.  The AUC was 0.8063 when the test dataset was evaluated with the model. 

![image](https://user-images.githubusercontent.com/64822593/198936803-2a2fb8d3-d3b2-4009-b9d9-e54b24d96e79.png)

![image](https://user-images.githubusercontent.com/64822593/209490657-9591d29d-89b4-4f8d-bf0e-5f21a1259795.png)
<br>Receiver Operating Characteristic(ROC) Curves for LUAD and LUSC at 1x by Resnet 50 model<br>

To further improve the accuracy, we tried the training with 2x images.  The AUC slightly increased to 0.8261.

![image](https://user-images.githubusercontent.com/64822593/211115004-cc7fdb16-2029-4c0a-8239-d32afd1c199a.png)
<br>Receiver Operating Characteristic(ROC) Curves for LUAD and LUSC at 2x by Resnet 50 model<br>

#### 3. Performance of Resnet 50 with the WSI at 2x magnification with the model pretrained at 4x

Although the model provided by the authors was trained at 4x, it could be utilized for the training at 2x.  The validation accuracy reached about 0.89 with the validation loss of 0.25 upon completing 100 epochs.  The AUC also dramatically increased to 0.9731, 17.9% boost compared the previous training at 2x without the 4x pretrained model.  Our hardware spec. remained the same as for the 1x training (CPU RAM : 128 GB).

![image](https://user-images.githubusercontent.com/64822593/201279646-b3c4170d-2cc1-4f87-b32d-6486e306f473.png)

![image](https://user-images.githubusercontent.com/64822593/211124003-166dd949-94b4-4f32-9c02-3bc8452383e7.png)
<br>Receiver Operating Characteristic(ROC) Curves for LUAD and LUSC at 2x by Resnet 50 model initialized by the model pretrained at 4x<br>

#### 4. Visualization of grad-CAM
The model was evaluated visually by grad-CAM that depicts the likelihood of the tumor in the tissue(panel A in the figure). The image below highlights where the lung cancer cells are likely located in an LUAD case.  

The second image was generated using the ResNet50 model trained at the 1x magnification from the weights of Imagenet and shows somewhat large tissue area of false positive(panel B).  

The third image was generated using the Resnet model trained at the same magnification in a continous mode after loading the previously trained model (at 4x) and seemed to show much tighter marking of the tumor tissue(panel C).  

Finally, the fourth image was generated using the Resnet model trained at the 2x in the continous mode and seems to show much larger marking area of the tumor tissue(panel D).  At the moment, the marking has not been validated by an expert.
![image](https://user-images.githubusercontent.com/64822593/201546476-51062b10-2bd1-4e96-a929-65078ae32f0b.png)

#### 5. Problems encountered when the hardware requirement was not met
The computing server consisted of an NVIDIA Quadro RTX 6000 GPU that had 24 GB memory capable of runnning at 14~16 TFlops in single precision.  The CPU memory was 128 GB.  

Due to the memory requirement of the training algorithm, only images at the magnification of 2x or lower were suitable for the training at the server.  Thus, higher resolution images at 4x or higher could not be used for the training. 

When the magnification of the training images was 1x, the accuracy was 0.80 for Resnet 50.  When the magnification was increased to 2x, the accuracy was also increased to 0.89(data not shown).  This suggested that the training images with higher resolution should improve the accuracy further. For trying out the 4x images, for example, the memory size is expected to be larger than 512 GB to accomodate the deep layers of Resnet 50, the batch of 100 WSIs, and the image of 22,000 x 22,000 pixels or larger, while avoiding the OOM error.

#### 6. Computing time
The computing time at 1x was about 4.3 sec per batch and the size of batch was set to 80.  Total computing time was 115 mins when the number of the total batch for the training of ResNet50 was 1,600.  The computing time at 2x was about 46 sec per batch.  Total computing time was about 102 hours when the number of the batch to process was 8,000.

### Acknowledgement
The computing server was kindly provided by the National Internet Promotion Agency(NIPA) of s. Korea.

### References

[1] Chi-Long Chen, Chi-Chung Chen, Wei-Hsiang Yu, Szu-Hua Chen, Yu-Chan Chang, Tai-I Hsu, Michael Hsiao, Chao-Yuan Yeh$ & Cheng-Yu Chen$  An annotation-free whole-slide training approach to pathological classification of lung cancer types using deep learning. *Nat Commun* **12,** 1193 (2021). https://doi.org/10.1038/s41467-021-21467-y

[2] Wen-Yu Chuang, Chi-Chung Chen, Wei-Hsiang Yu, Chi-Ju Yeh, Shang-Hung Chang, Shir-Hwa Ueng, Tong-Hong Wang, Chuen Hsueh, Chang-Fu Kuo & Chao-Yuan Yeh$ Identification of nodal micrometastasis in colorectal cancer using deep learning on annotation-free whole-slide images. *Mod Pathol* (2021). https://doi.org/10.1038/s41379-021-00838-2

## License

Copyright (C) 2021 aetherAI Co., Ltd.
All rights reserved.
Licensed under the CC BY-NC-SA 4.0 license (https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode).

## Appendix
Mostly taken from the original README.MD and some additional stuffs worth of mentioning were added.

### 1. Define Datasets

To initiate a training task, several CSV files, e.g. train.csv, val.csv and test.csv, should be prepared to define training, validation and testing datasets.

These CSV files should follow the format:
```
[slide_name_1],[class_id_1]
[slide_name_2],[class_id_2]
...
```
, where [slide_name_\*] specify the filename **without extension** of a slide image and [class_id_\*] is an integer indicating a slide-level label (e.g. 0 for normal, 1 for cancerous). 

The configuration files for our experiments are placed at data_configs/.

### 2. Set Up Training Configurations

Model hyper-parameters are set up in a YAML file. 

For convenience, you can copy one from train_configs/ (e.g. train_configs/config_wholeslide_2x.yaml) and make modifications for your own recipe.

The following table describes each field in a train_config.
| Field                      | Description
| -------------------------- | ---------------------------------------------------------------------------------------------
| RESULT_DIR                 | Directory to store output stuffs, including model weights, testing results, etc.
| MODEL_PATH                 | Path to store the model weight. (default: `${RESULT_DIR}/model.h5`)
| LOAD_MODEL_BEFORE_TRAIN    | Whether to load the model weight before training. (default: `False`)
| CONFIG_RECORD_PATH         | Path to back up this config file. (default: `${RESULT_DIR}/config.yaml`)
| USE_MIXED_PRECISION        | Whether to enable mixed precision training.
| USE_HMS                    | Whether to enable whole-slide training by optimized unified memory.
| USE_MIL                    | Whether to use MIL for training.
| TRAIN_CSV_PATH             | CSV file defining the training dataset.
| VAL_CSV_PATH               | CSV file defining the validation dataset.
| TEST_CSV_PATH              | CSV file defining the testing dataset.
| SLIDE_DIR                  | Directory containing all the slide image files (can be soft links).
| SLIDE_FILE_EXTENSION       | File extension. (e.g. ".ndpi", ".svs")
| SLIDE_READER               | Library to read slides. (default: `openslide`)
| RESIZE_RATIO               | Resize ratio for downsampling slide images.
| INPUT_SIZE                 | Size of model inputs in [height, width, channels]. Resized images are padded or cropped to the size. Try decreasing this field when main memory are limited.
| MODEL                      | Model architecture to use. One of `fixup_resnet50`, `fixup_resnet34`, `resnet34`, and `frozenbn_resnet50`.
| NUM_CLASSES                | Number of classes.
| BATCH_SIZE                 | Number of slides processed in each training iteration for each MPI worker. (default: 1)
| EPOCHS                     | Maximal number of training epochs.
| NUM_UPDATES_PER_EPOCH      | Number of interations in an epoch.
| INIT_LEARNING_RATE         | Initial learning rate for Adam optimizer.
| POOL_USE                   | Global pooling method in ResNet. One of `gmp` and `gap`.
| REDUCE_LR_FACTOR           | The learning rate will be decreased by this factor upon no validation loss improvement in consequent epochs.
| REDUCE_LR_PATIENCE         | Number of consequent epochs to reduce learning rate.
| TIME_RECORD_PATH           | Path to store a CSV file recording per-iteration training time.
| TEST_TIME_RECORD_PATH      | Path to store a CSV file recording per-iteration inference time.
| TEST_RESULT_PATH           | Path to store the model predictions after testing in a JSON format. (default: `${RESULT_DIR}/test_result.json`)
| USE_TCGA_VAHADANE          | Whether to enable color normalization on TCGA images to TMUH color style. (default: `False`)
| ENABLE_VIZ                 | Whether to draw prediction maps when testing. (default: `False`)
| VIZ_SIZE                   | Size of the output prediction maps in [height, width].
| VIZ_FOLDER                 | Folder to store prediction maps. (default: `${RESULT_DIR}/viz`)

The following fields are valid only when `USE_MIL: True`.
| Field                      | Description
| -------------------------- | ---------------------------------------------------------------------------------------------
| MIL_PATCH_SIZE             | Patch size of the MIL model in [height, width].
| MIL_INFER_BATCH_SIZE       | Batch size for MIL finding representative patches.
| MIL_USE_EM                 | Whether to use EM-MIL.
| MIL_K                      | Number of representative patches. (default: 1)
| MIL_SKIP_WHITE             | Whether to skip white patches. (default: `True`)
| POST_TRAIN_METHOD          | Patch aggregation method to use. One of `svm`, `lr`, `maxfeat_rf`, `milrnn` and `""` (disable).
| POST_TRAIN_MIL_PATCH_SIZE  | (The same as above, for patch aggregation method training process.)
| POST_TRAIN_INIT_LEARNING_RATE | (The same as above, for patch aggregation method training process.)
| POST_TRAIN_REDUCE_LR_FACTOR | (The same as above, for patch aggregation method training process.)
| POST_TRAIN_REDUCE_LR_PATIENCE | (The same as above, for patch aggregation method training process.)
| POST_TRAIN_EPOCHS          | (The same as above, for patch aggregation method training process.)
| POST_TRAIN_NUM_UPDATES_PER_EPOCH | (The same as above, for patch aggregation method training process.)
| POST_TRAIN_MODEL_PATH      | Path to store patch aggregation model weights.

### 3. Generating AUC and ROC Graphs
To statistically analyze the results, some scripts were provided in tools/.
See the following table for the usage of each tool.
| Tool                  | Description                                     | Example
| --------------------- | ----------------------------------------------- | ---------------------------------------------
| tools/calc_auc.R      | Calculate AUC and CI.                           | tools/calc_auc.R RESULT_DIR/test_result.json
| tools/compare_auc.R   | Testing significance of the AUCs of two models. | tools/compare_auc.R RESULT_DIR_1/test_result.json RESULT_DIR_2/test_result.json
| tools/draw_roc.py     | Draw the ROC diagram.                           | python tools/draw_roc.py test_result.json:MODEL_NAME:#FF0000
| tools/gen_bootstrap_aucs.R | Generate 100 AUCs by bootstrapping.        | tools/gen_bootstrap_aucs.R RESULT_DIR/test_result.json
| tools/print_scores.py | Print scores from test_result.json              | python tools/print_scores.py RESULT_DIR/test_result.json --column adeno (is_adeno, is_squamous, or squamous can be given)

### 4. Useful pre-trained Model

A pre-trained weight was obtained from https://drive.google.com/file/d/1XuONWICAzJ-cUKjC7uHLS0YLJhbLRoo1/view?usp=sharing kindly provided by the authors.

The model was trained by TCGA-LUAD and TCGA-LUSC diagnostic slides specified in `data_configs/pure_tcga/train_pure_tcga.csv` using the configuration file, `train_configs/pure_tcga/config_pure_tcga_wholeslide_4x.yaml`.

Since no normal lung slides were provided in these data sets, the model predicts a slide as either adenocarcinoma (class_id=1) or squamous cell carcinoma (class_id=2).

Thus, the prediction scores for normal (class_id=0) should be ignored.

Validation results (*n* = 192) on `data_configs/pure_tcga/val_pure_tcga.csv` are listed as follow.

- AUC (LUAD vs LUSC) = **0.9794** (95% CI: 0.9635-0.9953)
- Accuracy (LUAD vs LUSC) = **0.9323** (95% CI: 0.8876-0.9600, @threshold = 0.7 for class1, 0.3 for class2)

<img src="https://user-images.githubusercontent.com/6285919/122541978-cd029800-d05c-11eb-932c-3cc0c517101e.png" width="400" />

### Error occurred while loading the pretrained model

The number of the layers of the provided model was different to the ones of Resnet 50 or Resnet 32. <br>
It was compatible only with the 'frozenbn_resnet50', whereas the 'fixup_resnet50' also resulted in an error while initializing the model.
In fact, the 'config_pure_tcga_wholeslide_4x.yaml' had the MODEL argument defined as 'frozenbn_resnet50'.

### 5. Data Availability

The slide data supporting the cross-site generalization capability in this study are obtained from TCGA via the Genomic Data Commons Data Portal (https://gdc.cancer.gov).

A dataset consists of several hundred slides from TCGA-LUAD and TCGA-LUSC is suitable for testing our pipeline in small scale, with some proper modifications of configuration files described above.

### 6. Backbones of the model 

In model.py, a dictionary variable called graph_mapping was defined as below.  The model types, 'fixup_resnet50' and 'frozenbn_resnet50', were initialized with the imagenet as weights.  The other two, 'resnet34' and 'fixup_resnet34', were initialized randomly.  

graph_mapping = {

    "resnet34": lambda *args, **kwargs: ResNet34(
        *args, 
        norm_use="bn", 
        weights=None,
        use_fixup=False,
        data_format="channels_last",
        **kwargs
    ),
    
    "fixup_resnet34": lambda *args, **kwargs: ResNet34(
        *args, 
        norm_use="", 
        weights=None,
        use_fixup=True,
        data_format="channels_last",
        **kwargs
    ),
    
    "fixup_resnet50": lambda *args, **kwargs: ResNet50(
        *args, 
        norm_use="", 
        weights="imagenet",
        use_fixup=True, 
        data_format="channels_last",
        **kwargs
    ),
    
    "frozenbn_resnet50": lambda *args, **kwargs: ResNet50(
        *args,
        norm_use="frozen_bn",
        weights="imagenet",
        use_fixup=False,
        data_format="channels_last",
        to_caffe_preproc=True,
        **kwargs
    ),
    
The use of arguments such as norm_use, use_fixup, and to_caffe_preproc seemed to be referred by the authors to:

> Reference papers

- [Deep Residual Learning for Image Recognition]
  (https://arxiv.org/abs/1512.03385) (CVPR 2016)
  
- [Fixup Initialization: Residual Learning Without Normalization]
  (https://arxiv.org/abs/1901.09321) (ICLR 2019)

> Reference implementations

- [ResNet]
  (https://github.com/keras-team/keras-applications/blob/master/keras_applications/resnet_common.py)
  
## Part III. Preliminary study of Multiple Instance Learning(MIL) (Next phase development. Under preparation)
