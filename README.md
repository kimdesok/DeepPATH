## A proposal to build a pipeline for classifying lung cancer images and finding gene mutation from cancer tissue images

1) Lung cancer images and corresponding clinical data will be downloaded from TCGA cancer database.
2) ResNet-50 model will be investigated to classify cancer vs. normal images.
3) VGG-19 model will be also investigated in addition and compared its performance with the ResNet-50 model.
4) Each model will be also tested if it can detect the presence of a gene mutation in each patient sample.

![image](https://user-images.githubusercontent.com/64822593/154029950-8e379ddb-0b8c-47f6-b37f-876c40b0ff31.png)
<br><center>Pipeline schema for dataset preparation and DL model training</center>
<br>
<br>

## A propsal to utilize an entity-graph representation of the entire WSI to increase the tissue image analysis based gene mutation detection

1) The spatial relationship between intra and intercell types can be important to classify the tumor with gene mutations but it is lost in the patch based image analysis.
2) A graph representation of tumor or normal cells may be included in the training.
3) A graph representation of tumor and normal cells may be included in the training.
4) A hierarchical graph representation of cells and tissue (glands or stromal layers) may be included in the training.

![image](https://user-images.githubusercontent.com/64822593/155290161-1e464b34-5396-4032-afb6-c785e02278ba.png)

## A proposal to provide a commercial SaaS on the cloud focusing on data QC and MLOps

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
>* An annotation-free whole-slide training approach to pathological classification of lung cancer types using deep learning (https://www.nature.com/articles/s41467-021-21467-y)
>* Validation of a digital pathology system including remote review during the COVID-19 pandemic (https://www.nature.com/articles/s41379-020-0601-5.pdf)
>* Hierarchical Graph Representations in Digital Pathology (https://arxiv.org/pdf/2102.11057.pdf)


------------------------------------------
## Dr. Coudray's DeepPATH framework

The DeepPATH framework gathers the codes that have been used to study the use of a deep learning architecture (inception v3 from Google) to classify Lung cancer images.

For more details and references, please check:

Nicolas Coudray, Paolo Santiago Ocampo, Theodore Sakellaropoulos, Navneet Narula, Matija Snuderl, David Fenyö, Andre L. Moreira, Narges Razavian, Aristotelis Tsirigos. Classification and mutation prediction from non–small cell lung cancer histopathology images using deep learning. Nature Medicine, 2018; DOI: 10.1038/s41591-018-0177-5

https://www.nature.com/articles/s41591-018-0177-5

https://www.biorxiv.org/content/early/2017/10/03/197574

The schema of the model build from the paper
![image](https://user-images.githubusercontent.com/64822593/154029375-23ba352d-3f16-4933-86f3-f9478c2f4523.png)

