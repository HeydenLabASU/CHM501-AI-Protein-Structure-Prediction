# Homework 4 #

Special thanks to Josh Vermaas (Michigan State University), who prepared an earlier version of this tutorial!

AlphaFold and related tools combine information from protein sequence and structure databases to predict three-dimensional protein structures.

The main goals of this exercise are:

- Become familiar with several web servers for protein structure prediction.
- Learn to use local high-performance computing resources to predict more complex protein structures.
- Interpret pLDDT scores and compare protein structures via root mean square deviations (RMSD)

## Do I need to run AlphaFold at all?

Predicted structures are already available for many proteins, so you may not need to run AlphaFold yourself. The [AlphaFold Protein Structure Database](https://alphafold.ebi.ac.uk/) provides predictions generated using AlphaFold, many of which can also be accessed through [UniProt](https://www.uniprot.org/).

For example, numerous experimentally determined structures are available for citrate synthase, the enzyme that catalyzes the first reaction of the citric acid cycle. The **Structure** section of the [UniProt entry for human citrate synthase](https://www.uniprot.org/uniprotkb/O75390/entry) also includes an AlphaFold2 prediction that can be viewed online or downloaded.

<img src='files/screenshots/uniprot_citrate-synthase_structure.png' alt="AlphaFold2 prediction of human citrate synthase" width='800'>

## Protein structure prediction web servers

For a quick protein structure prediction, the simplest approach is often to use AlphaFold or a related tool through a web server. You provide one or more protein sequences, and the server uses remote computing resources to generate the predicted structure.

A popular option is [ColabFold](https://github.com/sokrypton/ColabFold), which provides an accessible, interactive interface through a Jupyter notebook. ColabFold accelerates the multiple sequence alignment (MSA) and homology-search steps used by AlphaFold. The method is described in the [paper accompanying the web server](https://doi.org/10.1038%2Fs41592-022-01488-1).

Several other web servers are also available. Protein structure prediction is a rapidly evolving field, and different research groups have developed methods that aim to improve prediction accuracy or reduce computational cost. For example, Meta developed ESMFold, a neural-network model for [predicting protein structures](https://esmatlas.com/). The method was published in [_Science_ in 2023](https://doi.org/10.1126/science.ade2574).

Other established methods also remain available. Before machine-learning approaches became widespread, I-TASSER, for example, was among the leading tools for protein structure prediction. The [I-TASSER web server](https://zhanggroup.org/I-TASSER/) provides an opportunity to explore an approach developed before AlphaFold.

In the following, we will compare multiple protein structure prediction tools using several web servers. We will predict the structure of the following short amino acid sequence. The sequence is expected to form both alpha-helical and beta-sheet secondary structures.

```
PIAQIHILEGRSDEQKETLIREVSEAISRSLDAPLTSVRVIITEMAKGHFGIGGELASK
```

Copy the amino acid sequence and submit it to each of the following web servers:

- [ESMFold](https://esmatlas.com/resources?action=fold), developed by Meta
- [ColabFold](https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2.ipynb), developed at Seoul National University
- [RoseTTAFold](https://neurosnap.ai/service/RoseTTAFold%20All-Atom), developed by the Baker laboratory at the University of Washington (requires a NeuroSnap account; limited free credits are available)
- [D-I-TASSER](https://zhanggroup.org/D-I-TASSER/), developed by the Zhang group at the National University of Singapore (formerly at University of Michigan)

As you work through the web servers, consider the following questions:

1. How long does each server take to return a result?
2. When entering the sequence, can you determine whether the method supports the prediction of protein complexes?
3. How similar are the structures predicted by the different methods?
4. How can you download each predicted structure to your computer for further analysis?
5. Does the prediction result in a single structure or multiple structures? If multiple structures are generated, which one can you trust most and why?

Among the webservers, D-I-TASSER may take a while to complete. Sign up with your email address and let the server send you an email once the prediction is completed (this can take a few hours).

***Download and save each predicted structure in PDB format. You will analyze these files later.***

## AlphaFold3 @ ASU ##

There are multiple reasons not to use webservers. For example, you may be working in protein design and need to protect intellectual property (the amino acid sequence of your protein). Or you need to predict the structure of a larger set of sequences, which goes beyond what free webservers allow you to do.

The AlphaFold models and parameters have been released to the public.

- [AlphaFold2 on GitHub](https://github.com/google-deepmind/alphafold)
- [AlphaFold3 on GitHub](https://github.com/google-deepmind/alphafold3)

In principle, you could install AlphaFold2 or AlhpaFold3 on your own computer. However, as many ML/AI applications, these models work best on specialized hardware (GPUs or, as in our cse, HPUs), require the download of large datasets, additional tools for multiple sequence alignments (MSA), etc.

However, ASU's Research Computing team has done this for us and AlphaFold3 is ready to run on the new Intel Gaudi2 system with a user friendly interface. Go to your [voyager page](voyager.rc.asu.edu) (requires VPN connection), go to `Workflows & Apps`, `Catalog` and select the `FastFold (AlphaFold) Protein Folding` workflow template (see red arrows in screenshot below).

<img src='files/screenshots/voyager_fastfold-1.png' alt="screenshot of Voyager web interface" width=800>

In the flowchart, click on the `alphafold` icon and paste your sequence in the corresponding field on the right (see red arrows in screenshot below).

<img src='files/screenshots/voyager_fastfold-2.png' alt="screenshot of AlphaFold3 workslow" width=800>

Then click on the red 'Deploy' icon to start the prediction. This should take to a screen similar to the following, where you can see the status of your AlphaFold prediction.

<imd src='files/screenshots/voyager_fastfold-3.png' alt="status of AlphaFold prediction" width=800>

After a few minutes, the status of the prediction (upper right field in red rectangle above) should change to `Succeeded` to indicate that your prediction is complete. A click on the `Results` icon will take you to a folder in your S3 cloud storage that contains the results of your AlphaFold predictions. You should find the results of your latest prediction in the folder with todays date.

<imd src='files/screenshots/voyager_fastfold-4.png' alt="AlphaFold prediction results" width=800>

Open the folder and find the sub-directory `query`, which contains your results, specifically five predicted structures with file names `ranked_1-5.pdb` (rank 1 is your best prediction; can you tell why it is better than rank 2-5?). Alternatively, download the entire folder to your computer and open the 'index.html' file to look at your results.

## Compare the structures obtained from different methods

Download this [folder](files/pdb-compare) and use the Jupyter notebook in it to compare the protein structures generated by AlphaFold3 and the distinct webservers. Note that the notebook also includes a text file that is required to set up the python environment. Thus always copy the entire folder if you move it somewhere.

You can run the notebook on your own computer (e.g., using Cursor in classic mode), on ASU's Sol computer, or on the Gaudi2 system (using the same JupyterLab environemnt as for training the neural networks). In the latter cases, just upload the folder with the notebook and your pdb files (open the JupyterLab environment and upload the files using the file manager on the left side of the window). 

Before running the notebook, you need to specify the location (path) of each pdb file. I recommend placing all pdb files in the directory with the Jupyter notebook or in a sub-directory. Then you can specify their location using a simple path relative to the location of the notebook itself.

The notebook will compute the so-called root mean square deviation (RMSD) between all structure pairs (see markdown text in notebook for details), which are shown as a matrix. The RMSD is computed for both the backbone atoms and all non-hydrogen atoms in each protein. The notebook also superimposes each of the structures, allowing you to perform a visual comparison.

## *INSTRUCTIONS FOR HOMEWORK SUBMISSION (1/3)* ##

1. Take a screenshot of the superimposed proteins and describe what you see (2-3 sentences).
2. Take a screenshot of the RMSD matrices
3. Answer the following questions:
    - Why is every diagonal entry of the RMSD matrix 0 Å?
    - Why is the value in row A, column B of each RMSD matrix the same as the value in row B, column A?
    - Which pair has the smallest backbone RMSD, and which pair has the largest?
    - Why are all-atom RMSD's larger than backbone RMSD's?
    - Are any of the predictions significantly different from AlphaFold3 (read markdown text to learn how to interpret the magnitude of RMSDs)

## Compare the different structural models generated by AlphaFold3 ##

The 'query' folder with your AlphaFold3 predictions contains 5 distinct structure predictions ranked from 1 to 5. Use the same notebook as above (or a copy) to compare these 5 distinct structures. Modify the file paths and the corresponding structure names in section 2 of the notebook.

## *INSTRUCTIONS FOR HOMEWORK SUBMISSION (2/3)* ##

4. Take a screenshot of the superimposed structure predictions.
5. Take a screenshot of the RMSD matrices
6. Answer the following questions:
    - How large are the RMSD's between the different AlphaFold3 models? Are they larger or smaller than the differences between AlphaFold3 and the webservers?
    - Locate the plot of the pLDDT score per residue for each AlphaFold3 structure prediction. Include it in your submission and describe what it tells you about the quality of each predicted structure.

## Protein complexes ##

So far, we only predicted the structure of a single amino acid chain. The structure prediction taks becomes substantially more difficult for protein complexes, i.e., structures formed by multiple protein chains.

We will use a complex formed by 2 protein chains (named `A` and `B`). To do this, we separate the sequences by `:` and paste them into the FastFold (AlphaFold) Protein Folding workflow template:

```
AQVINTFDGV
ADYLQTYHKL
PDNYITKSEA
QALGWVASKG
NLADVAPGKS
IGGDIFSNRE
GKLPGKSGRT
WREADINYTS
GFRNSDRILY
SSDWLIYKTT
DHYQTFTKIR
:
KKAVINGEQI
RSISDLHQTL
KKELALPEYY
GENLDALWDA
LTGWVEYPLV
LEWRQFEQSK
QLTENGAESV
LQVFREAKAE
GADITIILS
```

The remaining process is the same as before. Once the prediction is completed, perform the same analysis as above for the single protein chain. Compare the 5 distinct predicted structures using the Jupyter notebook (or a copy of it) from before (with minor changes, see below).

In section 2 of the notebook, you can specify if you want to compare the structure of a specific chain (`A` or `B`) or all chains at once. This makes a substantial difference: we already learned that predicting the structure of a single chain is comparably "easy", while the prediction of the structure of a complex is a lot harder. The latter includes the relative orientation of the protein chains.

Thus, you need to perform 3 distinct comparisons:

- comparison of the structure of chain A in each model
- comparison of the structure of chain B in each model
- comparison of the structure of the entire complex

## *INSTRUCTIONS FOR HOMEWORK SUBMISSION (3/3)* ##

For each analysis (chain A, chain B, complex):

7. Take a screenshot of the superimposed structure predictions.
8. Take a screenshot of the RMSD matrices

Based on these combined results:

9. Describe your observations (2-3 sentences) and answer the following questions:
    - Is there a substantial difference between the RMSD of the individual chains and the entire complex?
    - Can you visually see difference in the relative orientation of the chains in the predicted structures for the complex?

Compile all snapshots, questions and your answers in a single document and upload it to Canvas.
