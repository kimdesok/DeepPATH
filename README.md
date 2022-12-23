# Classification of lung cancer image patches and prediction of gene mutations using deep neural nets
![image](https://user-images.githubusercontent.com/64822593/208830983-7c5d1077-7a3c-4618-85ff-ffc2ce92d5d8.png)

<br>Pipeline schema for dataset preparation and DL model training related to Aim 1 and 2<br><br>
## Aim 1. To build a pipeline for classifying lung cancer image patches

### Materials and Methods
1) Among several deep learning(DL) models, Xception, Inception v3, and Resnet 50 were selected from Tensorflow Keras APIs.
2) Whole slide images(WSI) of lung cancer specimens('LUAD' and 'Normal') were downloaded from TCGA project of the NCI's GDC data portal database.  
3) Each WSI was divided into small patches and transformed to the TFRecord format along with their labels for more efficient loading by using the scripts available at [1] 
4) Transfer learning was applied to each model pretrained with the imagenet dataset [2]  In addition, each model was trained from random initial weights ('from scratch') to assess the value of the transfer learning by comparison.  
5) Upon adjusting hyperparameters for the best validation loss('val_loss'), the model was fine-tuned by setting all layers to be retrained. 

## Results (provisional)
Several predefined deep learning(DL) models such as Inception v3, Xception and ResNet 50 were used to investigate the effectiveness of transfer learning and fine-tuning techniques when applied to the histologic image classification. A dataset of whole slide images avaliable at the NCI's TCGA site was downloaded and preprocessed into small patches along with their labels.  

Transfer learning and fine-tuning of the DL models were programmed within the framework of Tensorflow's Keras libraries. 

When the training set is small, transfer learning with the pretrained weights does not seem to improve AUC values for ResNet 50 compared to the one without ('None' for weights).  Its AUC values were about 0.74 whereas the AUC values from training without weights were about 0.95. The fine tuning dramatically improved the AUC to 0.95 for the one with the weights and slightly to 0.96 for the one without(See Table 1).

When the size of the dataset was increased by two folds, Resnet 50 trained from scratch reached the AUC of 0.9521 while the one with the pretrained weights did not change much, resulting in 0.7401.  Fine tuning further improved the AUC upto 0.9781 for the ResNet 50 trained from scratch and 0.9795 for the ResNet 50 trained with the pretrained weights(See Table 1).

When the full dataset was used for Resnet 50 with the pretrained weights, the AUC increased from 0.7535 to 0.9819 after fine tuning (See Table 1).   


Table 1.  Performance data of ResNet 50 using three differently sized datasets, Inception V3 and Xception models trained by transfer learning and fine tuning

![image](https://user-images.githubusercontent.com/64822593/198612735-2343c733-0337-442d-887b-462330147d22.png)

Inception V3 resulted in the AUC of 0.9287 by its transfer learning with the full dataset that was further improved by its fine tuning upto 0.9885, while Xception resulted in the AUC of 0.9236 that was further improved upto 0.9862(See Table 1).

(The full dataset had a training set of 1024 WSIs and a validation set of 206 WSIs while the small set had 100 WSIs and 50 WSIs, respectively.  Both sets had a test set of 207 WSIs.)

Below showing the loss and accuracy changes during the training of a fine tuned ResNet50 model with the small dataset

![image](https://github.com/kimdesok/DeepPATH/blob/master/Resnet50_small_fine_tuned_plot.png)

### Summary

This exercise showed a pretrained model with a small dataset required a fine tuning to improve the performance further.  
If the size of the dataset gets large enough, the 'from scratch' approach generates the result that could be good enough with (or without) the fine tuning.  
The accuracy of the finely tuned Xception, Inception V3, or Resnet50 models reached upto AUC 0.982 or higher. 

## Aim 2. To detect gene mutation from WSI datasets
1) To segment the malignant patches by using the patch image classification model obtained in AIM #1.
2) To classify each case WSI labeled by gene mutation data by the multi-label classification model.

### Materials and Methods
Each individual patch image was classified by the previously trained Inception v3 model, resulting in a set of patch images representing only tumor (LUAD only in this case).  These patches were divided into training, validatation, and test data groups. The patch images in each group were transformed into the TFRecord format and labeled with ten labels according to the gene mutation data.  For the multi label classification, the last dense layer was set to have 11 nodes that represented 10 typical gene mutation classes and one bias.  Transfer learning was applied to the predefined DL model initialized with the imagenet weights.  Fine tuning was followed in the same way with the method described in the Aim 1 section.  

### Results (provisional)
The same three types of deep learning(DL) models were investigated to predict gene mutations by their histologic representation in the lung cancers.  Resnet 50 model was trained with differently sized training datasets in the same manner as in the Aim 1.  Interestingly, the accuracy values were better in the model trained from the scratch (Weights labeled as None in the table below).  The improvement of the AUC was also observed in other two DL models and Inception v3 showed the higher value than the other two DLs.  The authors reported the exactly same findings but no clear explanation of why training from the scratch had resulted in the improvement in the accuracy[3]. 

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
