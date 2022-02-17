## A proposal to build a pipeline for classifying lung cancer images and finding gene mutation from cancer tissue images

1) Lung cancer images and corresponding clinical data will be downloaded from TCGA cancer database.
2) ResNet-50 model will be investigated to classify cancer vs. normal images.
3) VGG-19 model will be also investigated in addition and compared its performance with the ResNet-50 model.
4) Each model will be also tested if it can detect the presence of a gene mutation in each patient sample.

![image](https://user-images.githubusercontent.com/64822593/154029950-8e379ddb-0b8c-47f6-b37f-876c40b0ff31.png)
<br><center>Pipeline schema for dataset preparation and DL model training</center>
<br>
<br>

## A proposal to provide a commercial SaaS on the cloud focusing on data QC and MLOps
![image](https://user-images.githubusercontent.com/64822593/154438780-fd4e78ad-c6f7-483b-8884-8c171758825e.png)
<br><center>Workflow schema of remote review by a digital pathology system</center>




## References
>* An annotation-free whole-slide training approach to pathological classification of lung cancer types using deep learning (https://www.nature.com/articles/s41467-021-21467-y)
>* Validation of a digital pathology system including remote review during the COVID-19 pandemic (https://www.nature.com/articles/s41379-020-0601-5.pdf)


------------------------------------------
The original Readme by Dr. Coudray

The DeepPATH framework gathers the codes that have been used to study the use of a deep learning architecture (inception v3 from Google) to classify Lung cancer images.

For more details and references, please check:

Nicolas Coudray, Paolo Santiago Ocampo, Theodore Sakellaropoulos, Navneet Narula, Matija Snuderl, David Fenyö, Andre L. Moreira, Narges Razavian, Aristotelis Tsirigos. Classification and mutation prediction from non–small cell lung cancer histopathology images using deep learning. Nature Medicine, 2018; DOI: 10.1038/s41591-018-0177-5

https://www.nature.com/articles/s41591-018-0177-5

https://www.biorxiv.org/content/early/2017/10/03/197574

The schema of the model build from the paper
![image](https://user-images.githubusercontent.com/64822593/154029375-23ba352d-3f16-4933-86f3-f9478c2f4523.png)

