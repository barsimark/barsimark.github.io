---
layout: distill
title: Highly accurate protein structure prediction with AlphaFold
description: Blogpost based on Highly accurate protein structure prediction with AlphaFold article by Nature
tags: AlphaFold protein blogpost
giscus_comments: true
date: 2024-07-10
featured: true

authors:
  - name: Mark Barsi
    affiliations:
      name: University of Stuttgart

bibliography: 2024-07-10-alphafold.bib

toc:
  - name: Short summary
  - name: Purpose
  - name: Protein structure
  - name: Dataset
  - name: Model
    subsections:
      - name: Evoformer blocks
      - name: Structure blocks
      - name: Training
  - name: Results
  - name: Discussion

_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
---

Link to the original article in [Nature](https://www.nature.com/articles/s41586-021-03819-2#Sec9)

Link to the open-source repository on [Github](https://github.com/google-deepmind/alphafold)

## Short summary

Protein structures are fundamental to biological research, yet the known protein structures represent a tiny fraction of the vast diversity in existence. Traditional methods for determining protein structures through computational means, such as physical interactions and evolutionary history, are complex and time-consuming. To address these challenges, AlphaFold, a neural network model, was introduced in 2021, offering near-experimental accuracy in predicting three-dimensional protein structures. AlphaFold leverages a two-part architecture: Evoformer blocks and structure modules. Evoformer blocks predict relationships between protein components using graph inference, while structure modules calculate explicit three-dimensional structures through iterative refinement. The model is trained using the Protein Data Bank and self-distillation datasets, with additional genetic and structure databases for multiple sequence alignments. AlphaFold’s training involves a two-stage process with both initial and fine-tuning phases, achieving impressive accuracy. Results demonstrate the model’s capability in predicting both simple and complex protein structures with high precision. The AlphaFold model’s public availability has spurred further research, with significant implications for medical and biological sciences, such as developing vaccines, understanding genetic diseases, and combating antibiotic resistance. Since its publication, AlphaFold has been highly influential, evidenced by numerous citations and widespread recognition in the scientific community.

## Purpose 

Protein is essential in biology since it is the basis of life. Despite all kinds of various different research being conducted in this field, the number of known protein structures is relatively low. Only about one hundred thousand proteins have a structure known to science, out of potentially millions or billions of existing proteins. 

There are two existing traditional ways of finding the structure of protein using computational methods: physical interaction, and evolutionary history. The first one integrates our understanding of molecular physics, thermodynamics, and kinetic simulations; while the second one is derived from the history of protein evolution. In theory, both techniques sound like a great way to calculate three-dimensional protein structures, however, in practice they are rarely used as they are rather challenging even for small protein, they are highly dependent on context, they are not very accurate, and they take a really long time to do.
Since the traditional way is not appealing to calculate protein structure, a new approach was proposed utilizing neural networks to directly get the accurate three-dimensional structures to near experimental accuracy in the vast majority of the cases. This is exactly the reason why the AlphaFold network was created in 2021.

{% include figure.liquid loading="eager" path="assets/img/alphafold/casp14_predictions.gif" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The near-perfect accuracy of the AlphaFold model illustrated on two proteins (T1037 and T1049)
</div>

The green structure is the experimental result, in other words the ground truth. As it can be seen on this comparison, the predicted or calculated result of AlphaFold – the blue structure – is almost the same for the main components, there are only a handful of errors in the side-chains. 

## Protein structure

In order to fully comprehend the neural network in the later chapters, a bit of background information about the structure of proteins is required. There are four components in these structures, in the following complexity order: primary, secondary, tertiary, and quaternary.

The most basic structure is the primary. It is determined by the linear sequence of the components, the amino acids. There are 20 neutral ones, and a few ambigous mixtures. These amino acids form long chains by connecting to each other, resulting in the primary protein structure. The two ends of the protein chain are called the C-terminus and the N-terminus <d-cite key="sanger"></d-cite>. 

{% include figure.liquid loading="eager" path="assets/img/alphafold/primary_structure.jpg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    Primary protein structure <d-cite key="creative"></d-cite>
</div>

The secondary structure refers to the local substructure of these proteins. This is also called the backbone. By definition, this is a “continuous chain of atoms that runs throughout the length of a protein” <d-cite key="si"></d-cite>. There are two different types of secondary structure: alpha-helix, and beta-sheets <d-cite key="khanacademy"></d-cite> as illustrated by the following image:

{% include figure.liquid loading="eager" path="assets/img/alphafold/secondary_structure.png" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    Secondary protein structure <d-cite key="khanacademy"></d-cite>
</div>

The three dimensional shape of a single protein chain is called the tertiary structure. This is determined by the bonds within the chain <d-cite key="khanacademy"></d-cite>.

{% include figure.liquid loading="eager" path="assets/img/alphafold/tertiary_structure.png" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    Tertiary protein structure <d-cite key="khanacademy"></d-cite>
</div>

The final, quaternary structure is determined by the aggregation of various different chains. The bond between these individual chains gives the final structure of the protein, as showcased by this image <d-cite key="wikiprotein"></d-cite>:

{% include figure.liquid loading="eager" path="assets/img/alphafold/quarternary_structure.jpg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    Quaternary protein structure <d-cite key="creative"></d-cite>
</div>

## Dataset

For input, the model mainly uses the so called Protein Data Bank (PDB) database. This contains the amino acid sequences for the protein, the coordinates of all components of the protein chains, as well as additional metadata that might be relevant for the structures. There are thousands of proteins in this database that were all used for training the AlphaFold network. The size of this dataset is about 2.62TB.

This dataset alone however, wasn’t enough for training the neural network; even more data was necessary. Therefore, a so-called self-distillation dataset was created from the existing resources using different augmentation techniques such as chopping or MSA subsampling. After filtering out the unfeasible structures, about 350.000 new protein structures were created. 

A combination of these two datasets were used in the final training, which consisted of 75% self-distillation and 25% Protein Data Bank data.

Two additional databases were also used for both training and inference: a genetic database, and a structure database. The genetic database was used for getting Multiple Sequence Alignments (MSA). According to the Multiple Sequence Alignment Wikipedia page, MSA is “the process or the result of sequence alignment of three or more biological sequences, generally protein, DNA, or RNA. These alignments are used to infer evolutionary relationships via phylogenetic analysis and can highlight homologous features between sequences.” <d-cite key="wikimsa"></d-cite>. The second, structure database was required for helping the model find the exact, correct three-dimensional structure of the given protein.

{% include figure.liquid loading="eager" path="assets/img/alphafold/msa.png" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    An example for Multiple Sequence Alignment <d-cite key="biorender"></d-cite>
</div>

For accuracy assessment, the so-called CASP14 assessment was used, which is the gold-standard for protein structure accuracy prediction.

## Model

The AlphaFold network was built using two components: the first part consisting of Evoformer blocks, and the second of structure modules.

{% include figure.liquid loading="eager" path="assets/img/alphafold/architecture.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    The full architecture of AlphaFold (click to zoom)
</div>

The model has a Recurrent Neural Network (RNN) architecture, as illustrated by the recycling step in the bottom of the chart. It allows the network to constantly refine the results by reconnecting the output of both key components (Evoformer and structure modules) of the network back to the beginning three times. 

In my opinion, this is one of the new innovations of the network that makes the results so accurate, and probably why the model architecture inspired so many other researchers in numerous different fields.

Lets look at the Evoformer and structure modules a bit more detailed.

### Evoformer blocks

The Evoformer blocks make up the first half of the neural network. They are responsible for finding the correct protein components, as well as the connections between these components. 

The core idea behind these modules is to predict the connections between the components by interpreting the protein connections as a graph inference problem in three-dimensional space. The nodes represent the individual residues, while the edges are the proximity of residues.

{% include figure.liquid loading="eager" path="assets/img/alphafold/pair_representation.drawio.svg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    A simple example for the connection between pair representation and graph inference
</div>

The Evoformer blocks have two input: an MSA representation and a so-called pair representation. The MSA representation is a matrix where the columns represent the individual residues, and the rows are sequences where the said residues appear. Therefore, the dimensions of this input is Nseq x Nres, the number of sequences times number of residues. The second input is the pair representation matrix, which contains the relationship between the residues. This matrix has a shape of Nres x Nres. 

The output of these blocks is a refined version of both the MSA representation and the pair representation. The dimension of these are the same as the input’s which allows the blocks to be chained without any modification. 

{% include figure.liquid loading="eager" path="assets/img/alphafold/evoformer.png" class="img-fluid rounded z-depth-1" zoomable=true %}
<div class="caption">
    The architecture of the Evoformer blocks (click to zoom)
</div>

An individual Evoformer block contains both attention-based and non-attention-based modules. The combination of these allow the model to focus on details and overview at the same time. During the training, the MSA representation gets refined first using self-attention modules. The refined MSA matrix is than used to refine the pair-representation matrix with the help of a few additional self-attention and triangle update nodes. 

In order to avoid, or at least reduce the impact of overfitting, two methods were used in the Evoormer blocks. As it can be seen on the architecture image, there are skip-connections around all the building blocks within the Evoformer. These allow information flow around the blocks or layers, which as a result avoid the vanishing gradient problem, hence reducing overfitting. In addition, dropout layers were used to further decrease overfitting.

Overall, there are 48 of these Evoformer blocks in the network with individual weights, meaning there are no shared weights between the different modules.

### Structure blocks

The second part of the neural network consists of eight structure modules that are responsible for predicting, calculating the explicit three-dimensional structure of the proteins. 

The concept of the structure blocks is fairly straight forward. Only two matrixes need to be calculated in order to have a properly defined, explicit three-dimensional structure: a rotation and translation for each of the consisting residues. When these are defined, the concrete protein structure will be defined. Both of them will be iteratively calculated during the training process. Therefore, their starting values need to be initialized. 

For both matrixes, the trivial initialization was used. The rotation matrixes are set to be the identity matrix, meaning that none of the residues are rotated, so all of them are facing the same direction. The translation matrixes are all set in a way that all of the residues start from the origin, the center. This way of initialization allows the training process to  have minimal bias coming from the starting values, which is always a key challenge of neural networks.

Two inputs are needed for these calculations, both of them are coming from the previous Evoformer blocks: the final, refined MSA representation, as well as the pair representation matrix. With the help of these inputs, the structure block then calculates the relative rotation and translation for all the residues. It is very important to note, that these are relative values, relative to the other residues. At the end of the day it doesn’t matter if the whole structure is shifted to one side or the other, or if the whole thing is rotated; it is still the same structure. The output of these modules is the explicit three-dimensional structure, as well as the MSA and pair representation to serialize the modules.

{% include figure.liquid loading="eager" path="assets/img/alphafold/structure.png" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The architecture of the structure modules
</div>

In these modules, the so-called Frame-aligned Point Error function was used. This function calculates the distance between the predicted position and the actual, correct position for all the residues. The sum of all these is the final loss value. This method creates a strong bias for the residues to be in the correct location relative to the local frame, relative to the other residues.

{% include figure.liquid loading="eager" path="assets/img/alphafold/fape.drawio.svg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    A simple example for the FAPE loss
</div>

There are eight of these structure modules, with shared weights between them. Meaning that all of the blocks have the same weight matrix.

### Training

The training of the AlphaFold network consists of two stages, a first initial training followed by a fine-tuning step.

For the base version, only the PDB dataset was used. The weight initialization was done by the highly popular He initializer. Since the training at this step was started from scratch, a relatively large learning rate was needed, therefore it was set to 10<sup>-3</sup>. For the same reason, the number of training samples was also required to be high, about ten million samples were used. The total base training of the model takes 7 days.

The second training step is the fine-tuning, where only smaller adjustments are done in order to increase the overall accuracy. For this, the previously introduced self-distillation data was utilized in addition to the basic PDB set. Since only minor corrections were done, the learning rate was set to a smaller number, 5x10sup<sup>-4</sup>. The number of samples needed was also lower, only about 1.5 million of them were used. The fine-tuning takes about 4 days of training time.

Model                                           | Initial training | Fine-tuning
----------------------------------------------- | :--------------: | :---------:
Number of templates N<sub>templ</sub>           | 4                | 4
Sequence crop size N<sub>res</sub>              | 256              | 384
Number of sequences N<sub>seq</sub>             | 128              | 512
Number of extra sequences N<sub>extra_seq</sub> | 1024             | 5120
Parameters initialized from                     | Random           | Initial training
Initial learning rate                           | 10<sup>-3</sup>  | 5x10<sup>-4</sup>
Learning rate linear warm-up samples            | 128000           | 0
Structural violation loss weight                | 0.0              | 1.0
Training samples                                | ≈10 million      | ≈1.5 million
Training time                                   | ≈7 days          | ≈4 days

<div class="caption">
    Extended overview of the model parameters
</div>

As for optimizer, the most popular Adam optimizer was used.

The code of the AlphaFold network is open-source, available on [GitHub](https://github.com/google-deepmind/alphafold). Just to illustrate how big this network is, these are the official recommended hardware parameters for running the model in Google Cloud: 12 vCPU, 85GB RAM, 100GB boot disk, 3TB space for the datasets, and an Nvidia A100 Tensor Core GPU with 80GB GPU memory <d-cite key="nvidia"></d-cite>.

## Results

The AlphaFold network produces really impressive results on smaller as well as larger protein structures. In this chapter, the results will be discussed in a bit more details. For this, the metrics are divided into two parts: backbone that is the core structure and all-atom which consists of the backbone and the so-called side-chains. Obviously, the all-atom accuracy is always going to be lower, since it is much more difficult to predict the side-chains than the backbone.

The total backbone accuracy of the model using root mean square deviation at 95% coverage is 0.96 Å (Å: angstrom = 0.1 nanometer <d-cite key="britannica"></d-cite>). For the 95% confidence interval, the error is in the range of 0.85 – 1.16 Å. For all atoms, with the same coverage, the root mean square metric is 1.5 Å, the interval is 1.2 – 1.6 Å. That level of accuracy is outstanding, especially considering that the width of a single carbon atom is approximately 1.4 Å.

{% include figure.liquid loading="eager" path="assets/img/alphafold/errors_border.png" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    Total error distribution of the model. First row shows the 100%, the second the 95% coverage. First column is the backbone, the second is the all-atom accuracy.
</div>

 Quantity in Å                  | Lower quart. | Median     | Upper quart.
 ------------------------------ | :----------: | :--------: | :--------:
  RMSD - Cα                     | 1.31         | 2.32       | 6.49
  RMSD - All Atom               | 1.80         | 2.80       | 6.78
  RMSD<sub>95</sub> - Cα        | 0.79         | 1.46       | 4.33
  RMSD<sub>95</sub> - All Atom  | 1.17         | 1.89       | 4.72

<div class="caption">
    The quantiles of error distribution for both the backbone and all-atom
</div>

Another metric used for evaluating the model was the TM-score, which is a common assessment method for the structural similarity of proteins and RNAs. The calculation is very simple: the ground truth protein chain and the predicted chain are aligned, and the ratio of correctly found amino acids give the TM-score. Therefore it is 0 if there is nothing in common between the chains, and 1 if they are identical <d-cite key="zhang"></d-cite>.

{% include figure.liquid loading="eager" path="assets/img/alphafold/t1049_result_border.png" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The predicted and actual protein chain for CASP14 target T1049 showing the root mean square deviation and TM-score metrics
</div>

{% include figure.liquid loading="eager" path="assets/img/alphafold/t1044_result_border.png" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The predicted and actual protein chain for CASP14 target T1044 showing the root mean square deviation and TM-score metrics
</div>

The following video shows the training process of the Evoformer blocks during the three recycling phases for a small and simple protein of CASP14 target T1024 (LmrP). This protein consists of 408 residues. It can be seen how early in the process the model finds the correct structure, as well as the minor adjustments of the later steps.

{% include video.liquid path="assets/video/alphafold/41586_2021_3819_MOESM3_ESM.mp4" class="img-fluid rounded z-depth-1" controls=true %}

The second video is of a larger but still simple protein of CASP14 target T1044 (RNA polymerase of crAss-like phage). This structure consists of 2180 residues. It takes a bit longer to find the final structure and to find the correct relative positions. 

{% include video.liquid path="assets/video/alphafold/41586_2021_3819_MOESM4_ESM.mp4" class="img-fluid rounded z-depth-1" controls=true %}

The third video illustrates the training steps of a large and complex protein, namely CASP14 target T1091 which consists of 863 residues. The individual structures are determined early on, however there are few residues which it fails to place correctly during the entire training. This results in the visible unphysical strings on the video.

{% include video.liquid path="assets/video/alphafold/41586_2021_3819_MOESM6_ESM.mp4" class="img-fluid rounded z-depth-1" controls=true %}


## Discussion

During the training of the 48 Evoformer blocks, intermediate structure trajectories are calculated. These trajectories represent the networks belief of the most likely structure. Given that these trajectories remain smooth during the entire training, means that the model incrementally improves the structure until it can no longer be improved anymore, so when the final correct structure is found. This leads to the network being highly-accurate for both the backbone and the side-chain structure predictions. 

As evidenced by the previous videos, the model can cope with both simplicity and complexity, which is often a challenge of neural networks. For easy structures, like the T1024 (LmrP), the solution is found in the first couple of layers.

{% include figure.liquid loading="eager" path="assets/img/alphafold/lmrp_structure_border.jpg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The model's output for the simple T1024 (LmrP) protein <d-cite key="pdb"></d-cite>
</div>

For challenging, complex structures, for example the T1091 (on the third video) or the ORF8 of SARS-CoV-2 (T1064) the model constantly searches and rearranges until it can no longer be improved, or the training process is over.

{% include figure.liquid loading="eager" path="assets/img/alphafold/sarscov2_structure.jpg" class="img-fluid rounded z-depth-1" %}
<div class="caption">
    The extremely complex structure of the SARS-CoV-2 protein <d-cite key="vinjamuri"></d-cite>
</div>

The direct, explicit coordinate output of the model makes it convenient and easy to use for various different scenarios in practice. Combining this with the speed of the predictions, which is about one GPU minute for a model of 384 residues, the model is extremely capable of helping researchers in all fields of science.

The architecture alongside the full source and pseudo codes are available for the public, therefore it can also inspire further development and innovation in the field of artificial intelligence and neural networks.

The research impact of the paper is already outstanding. The article was published in Nature in 2021, and has already made a huge impact on the scientific community. As of July 2024, the article already has 1.6 million views, and more than 22 thousand citations. Out of similar aged articles, it is ranked at 105th out of all the 450.000 articles (99th percentile) tracked across all journals, and 15th out of all Nature articles. 

Just a few examples of what the scientific community thinks is possible to do with artificial intelligence that is inspired by the AlphaFold architecture:

- Stopping the spread of malaria by developing a vaccine that can potentially save hundreds of thousands of lives every year <d-cite key="googlestopping"></d-cite>

- Fighting osteoporosis before the bones start to break by understanding genetic causes through AlphaFold <d-cite key="googlefighting"></d-cite>

- Getting a better understanding of cancer and autism through faulty proteins <d-cite key="googleunderstanding"></d-cite>

- Finding a treatment to Parkinson’s, helping more than 10 million people worldwide <d-cite key="googletargeting"></d-cite>

- Beating antibiotic resistance to eliminate bacterial infections <d-cite key="googleaccelerate"></d-cite>

There are probably countless more problems that can potentially be solved with the help of AlphaFold, or an improved, even better version of the model (there is already an AlphaFold 2 <d-cite key="lewis"></d-cite> and AlphaFold 3 <d-cite key="elana"></d-cite>).
