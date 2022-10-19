## Aim 1. To build a pipeline for classifying lung cancer image patches

1) Lung cancer images and corresponding clinical data will be downloaded from TCGA cancer database.
2) A few deep learning models such as Inception V3, Xception and ResNet 50 will be investigated to classify cancer vs. normal patches.
3) Their performance will be compared.

![image](https://user-images.githubusercontent.com/64822593/154029950-8e379ddb-0b8c-47f6-b37f-876c40b0ff31.png)
<br><center>Pipeline schema for dataset preparation and DL model training</center><br><br>

### Materials and Methods:
1) Among several deep learning(DL) models, Xception, Inception v3, and Resnet 50 were selected from Tensorflow Keras APIs.
2) Whole slide images(WSI) of lung cancer specimens('LUAD' and 'Normal') were downloaded from the NCI's TCGA database.  Each WSI was divided into small patches and transformed to the TFRecord format along with their labels for more efficient loading.
3) Transfer learning was applied to each model pretrained with the imagenet dataset.  In addition, Resnet 50 was trained from random initial weights ('from scratch') to assess the value of the transfer learning by comparison.
4) Upon adjusting hyperparameters for the best validation loss('val_loss'), the model was fine-tuned by basically setting all layers to be retrained. 
reference: https://keras.io/guides/transfer_learning/

## Results (interim)
Several pretrained deep learning(DL) models such as Inception v3, Xception and ResNet 50 were used to investigate the effectiveness of transfer learning and fine-tuning techniques when applied to the histologic image classification. A dataset of whole slide images avaliable at the NCI's TCGA site was downloaded and preprocessed into small patches along with their labels.  Transfer learning and fine-tuning of the DL models were programmed within the framework of Tensorflow's Keras libraries. 

When the training set is small, transfer learning with the pretrained weights does not seem to improve AUC values for ResNet 50 compared to the one without ('None' for weights).  Its AUC values were about 0.74 whereas the AUC values from training without weights were about 0.95. The fine tuning dramatically improved the AUC to 0.95 for the one with the weights and slightly to 0.96 for the one without.

When the size of the dataset was increased by two folds, Resnet 50 trained from scratch reached the AUC of 0.9521 while the one with the pretrained weights did not change much, resulting in 0.7401.  Fine tuning further improved the AUC upto 0.9781 for the ResNet 50 trained from scratch and 0.9795 for the ResNet 50 trained with the pretrained weights.

When the full dataset was used for Resnet 50 with the pretrained weights, the AUC increased from 0.7535 to 0.9819 after fine tuning.   

This exercise showed a pretrained model could be ideal with a small dataset (only) if a fine tuning is applied to improve the performance further.  However, if the size of the dataset gets large enough, the 'from scratch' approach generates the result that could be good enough with (or without) the fine tuning.  

Inception V3 resulted in the AUC of 0.9287 by its transfer learning with the full dataset that was further improved by its fine tuning upto 0.9885, while Xception resulted in the AUC of 0.9236 that was further improved upto 0.9862.

(The full dataset had a training set of 1024 WSIs and a validation set of 206 WSIs while the small set had 100 WSIs and 50 WSIs, respectively.  Both sets had a test set of 207 WSIs.)

Below showing the loss and accuracy changes during the training of a fine tuned ResNet50 model with the small dataset
![image](https://github.com/kimdesok/DeepPATH/blob/master/Resnet50_small_fine_tuned_plot.png)

<br><br>
### Summary:
The accuracy of the finely tuned Xception, Inception V3, or Resnet50 models reached upto AUC 0.982 or higher.

## Aim 2. To explore annotation free classification schemes based on a whole slide training method
1) The whole slide images were used to train standard CNNs by using the unified memory (UM) mechanism and several GPU memory optimization techniques without modification in either training pipelines or model architectures. 
2) To compare some previous label free trainings such as MIL, the performance of the whole slide training was superior (AUC: 0.9594 vs. 0.9310 for MIL-RNN).
 
![image](https://user-images.githubusercontent.com/64822593/194527469-1b186d2e-672b-46e8-9ad4-9ab13685ab42.png)
From [1].  This figure illustrates nicely for three representative approaches for WSI classification schemes.  The aim 1 was accomplished following the scheme (a).  In aim 2, the scheme (c) will be explored.
## Aim 3. To provide a commercial SaaS on the cloud focusing on data QC and MLOps

1) The surgery/oncology lab sends the patient specimens for a slide preparation lab.
2) The slide preparation lab prepares the slides and scan them to generate WSI images.
3) The proposed service accesses the WSI images and performs a data QC.
>* QC on the scanned images
>* QC on the clinical data
4) The proposed service performs a DL based diagnostic analysis whose summary shall be uploaded onto the cloud.
> * MLOps on datasets
> * MLOps on model performance
> * MLOps on model upgrade
5) The pathologist accesses the service and sign off the findings.
6) The surgery/oncology lab accesses the service to get the report.

## References
>[1] An annotation-free whole-slide training approach to pathological classification of lung cancer types using deep learning (https://www.nature.com/articles/s41467-021-21467-y)
>[2] Validation of a digital pathology system including remote review during the COVID-19 pandemic (https://www.nature.com/articles/s41379-020-0601-5.pdf)
>[3] Hierarchical Graph Representations in Digital Pathology (https://arxiv.org/pdf/2102.11057.pdf)

------------------------------------------
##  Discussion on the DeepPATH framework

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

### Acknowledgement: NVidia's Quadro RTX 6000 was kindly provided by a high performance computing facility of NIPA(National IT Promotion Agency).
