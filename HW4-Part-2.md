# Homework 4 Part 2 #

At this point, you will have predicted the structure of a small peptide with multiple webservers as well as AlphaFold2. Further, you will have performed structure predictions with AlphaFold2 with and without template information for another small proteins as well as for a protein complex. 

Now we will examine how similar the structure predictions for the peptide structure are. For this , we will use the program VMD (Visual Molecular Dynamics), which is already installed on SOL. This time we will need more than just a text-based interface, but the first step is to transfer the files with the webserver predictions to SOL.

First, create a folder/directory on your computer with the name `webserver-predictions`. Then copy the `.pdb` files with the predicted structures into that folder. For compatibility with the scripts you will use later, name the files as follows:

- ESMFold.pdb
- ColabFold.pdb
- topsuite.pdb
- DITASSER.pdb

I skipped RoseTTAFold here, because previous attempts indicated that the wait time for the RoseTTAFold prediction might be long to be practical for this exercise. 

Navigate to [sol.asu.edu](sol.asu.edu) in your browser and log in with your ASURITE ID. Then click on Files -> Home Directory as shown below. 

<img src='files/screenshots/00-SOL-files.png' width='600'>

You will see the content of your `$HOME` directory on SOL. Navigate to the CHM501/HW4 directory. The folder should already contain the folders `Example-1`, `Example-2`, `Example-3` and `vmd-scripts`. 
Now click on the `Upload` button on the upper right of your window and drag and drop the `webserver-predictions` folder on your local computer into the upload field.

Next, click on `Interactive Apps` and select `Sol Desktop` as shown below.

<img src='files/screenshots/00-Desktop.png' width='600'>

On the next screen, you can select resources on one of the SOL nodes. The default settings are fine, but I recommend t adjust the time limit to ensure the session does not end after 1 hour. Your job will enter the "queue" but it should only take a short while until your session is available (status change from `queued` to `running`).  Once it is ready, click on `Launch Sol Desktop`.

Next you will see a very simple standard desktop screen similar to a Windows or MacOS computer. This interface allows us to open programs as `windows` with a graphical user interface. However, we will start (again) with a simple terminal, which you can open by clicking on the black'ish icon in the app list in center of the bottom of the screen.

<img src='files/screenshots/06-SOL-Desktop-job-terminal.png' width='600'>

First, we need to load the VMD module using the following terminal command:
```bash
module load vmd-1.9.3-gcc-11.2.0
```

## Comparing Structure Predictions ##

Now, we use the terminal to navigate to the `CHM501/HW4` folder. Next we need to load the `.pdb` files with the predictions into VMD and visualize them. 

For this, we can simply use a pre-prepared script that contains all necessary commands. We can also load the files "by hand" using VMD's graphical user interface and the mouse, but this is too tedious.

The script is located in your `VMDscripts` directory and we can inspect it by printing its content to the terminal.
```bash
cat VMDscripts/load-peptide.tcl
```

```TCL
mol new Example-1/exampleoutput/relaxed_model_3_pred_0.pdb
mol rename top AlphaFold
mol new webserver-predictions/ESMFold.pdb
mol new webserver-predictions/ColabFold.pdb
mol new webserver-predictions/DITASSER.pdb
mol new webserver-predictions/topsuite.pdb
mol top 0

set colorlist [list 0 1 3 7 6]
# AlphaFold = blue (0)
# ESMfold   = red (1)
# ColabFold = orange (3)
# DITASSER  = silver (6)
# topsuite  = green (7)
for { set i 0 } { $i < [molinfo num] } { incr i } {
    #This sets distinct colors for each prediction model
	mol modcolor 0 $i ColorID [lindex $colorlist $i]
    #This sets the 'Drawing Method' to NewCartoon which highlights secondary structure
	mol modstyle 0 $i NewCartoon 0.300000 10.000000 4.100000 0
}
menu main on
display resetview
```

Note, that if you used different names for the `.pdb` files or the directory, you will either need to modify the script or change the names according to the script.

To rename files or folders, use the `mv` terminal command. To edit the text files, use the `vi` or `emacs` command line text editors.

## Webserver output comparison ##

In terms of speed, ESMFold should have predicted a structure almost instantaneously, ColabFold within a minute or two, while the other webservers are considerably slower, ranging in response times between hours to days. Did you get a response from RoseTTAFold? If not, don't worry.

How do the results compare against each other?
How does AlphaFold do in [Example 1](Example-1), when it was predicting the structure for the same sequence?
We can answer all of those questions qualitatively and quantitatively using visualization and analysis tools such as VMD and the information encoded in the output files.

To use the `load-peptide.tcl` script to load all the files in VMD, type on the command line:
```bash
vmd -e VMDscripts/load-peptide.tcl
```

VMD will open and you should see two windows, the VMD Main window, which lists the loaded molecules, and the OpenGL display with the protein structures.

The script visualizes each protein based on its secondary structure and assigns different colors to each prediction (the comments in the script above tell you which one is which).

However, all structures look very different at first, because orientation of the predicted peptide structures in 3D space is random. 
To make a proper comparison, we need to rotate the proteins into a common orientation. 

Essentially, we need to rotate the proteins to make them look as similar as possible, so that any differences are associated with distinct structures and not position or rotation. 
The measurement of similarity that we use is the [root mean square deviation (RMSD)](https://en.wikipedia.org/wiki/Root-mean-square_deviation), which measures how different two structures are relative to each other. 

The easiest way to do that is with one of VMD's built-in analysis tool called `RMSD Trajectory Tool`. Click on `Extensions` on the top of the VMD Main window, then click `Analysis` and `RMSD Trajectory Tool`. 

<img src='files/screenshots/rmsdtrajtool.png' width='600'>

The RMSD is an atom-wise measurement, however, the different protein structures have distinct numbers of atoms.
AlphaFold2 includes hydrogens, while the other tools do not. Further, the C-terminus is handled differently by the distinct prediction tools.
Therefore, we need to define a selection of atoms in each structure that is identical in each of them. 
We do this by replacing the default selection "*protein*" by "*protein and not name OXT*". 

Using the check boxes below, we can further select only backbone atoms ("backbone") or all non-hydrogen atoms within our selection ("noh"). Select one of them, then click on the "Align" button.

Now, you will see that the predicted structures are indeed very similar. 
One of them (the silver one for DITASSER), makes beta-sheets that VMD does not quite recognize as perfect. Consequently, the cartoon representation used here does not look the same as for the other proteins. 
However, the actual differences in the atomic positions are similar.

To measure how similar the structures are, click on the "RMSD" button. The first structure (the AlphaFold2 prediction) is used as the reference and the RMSD to itself is of course zero. 
How large are the RMSD's for the other structures (VMD measures the RMSD in Angstrom)?

*SUBMISSION*
Create a document with a screenshot of the aligned structures and their RMSD's. Perform the alignment and subsequence RMSD calculation both for backbone and non-hydrogen atoms and compare the results. Answer the following questions:
- Which RMSD (backbone or noh) is larger? 
- Can you explain why?
- Which of the webserver predictions is most similar to AlphaFold2?


Overall, we note that the RMSD's between the different predictions are not very large.
A crystal structure with a sub-3 Angstrom resolution is pretty good and most of the differences are smaller than that.

## What else can AlphaFold tell you?

At this point, we've taken a look at what the output structures look like, and have a bit of a sense that prediction methods will arrive at similar structures for well-behaved systems.
But there is more information in the AlphaFold output than just a structure.
The next step is to do a bit of digging into the directory structure AlphaFold generates, and take a look at the other files that are output.

```bash
cd $SCRATCH/Example1
#List the files
ls
#Notice how there is an "input" directory that wasn't there the first day?
#Since the name of the fasta file was input.fasta, the output is stored in
#a directory called input.
cd input
#If there is no "input" directory, you can also look at the exampleoutput.
#cd exampleoutput
#Look at all these files!
ls
```

Each of these files can be opened via `gedit` like before.
There is the `output.log` file, which has the log of the the terminal output AlphaFold wrote while running the calculation.
What I pay attention to most here are the timestamps embedded into the log.
Based on the hardware I got when I ran the calculation myself, I see that AlphaFold took about half an hour to complete.
This jives with the contents of `timings.json`, which shows that the "features" calculation took the majority of the time (the times are measured in seconds), which is where AlphaFold is running the multiple sequence alignment.
Once you have the features, calculating models based off of those features is actually relatively quick, only needing a minute or two to create a model.
This gives you an intuitive sense of how Meta and Collabfold can come up with methods that are faster, by coming up with creative shortcuts to accelerate featurization.

We'll be ignoring the files with the `.pkl` extension.
These are python "pickle" files, and are basically machine-readable data structures that you can use to plot quality metrics in higher precision.
Scripts for analyzing these files can be found online, like [this script from Belgium](https://github.com/jasperzuallaert/VIBFold/blob/main/visualize_alphafold_results.py) that will plot pLDDT or pae (predicted alignment error) from these pickles, or [this script](https://blog.biostrand.be/explained-how-to-plot-the-prediction-quality-metrics-with-alphafold2).

### pLDDT scores and model quality

Another noteworthy file is `ranking_debug.json`. AlphaFold is making multiple models.
It ranks them by the pLDDT score, which is a metric that calculates how plausible a structure is.
The higher the score, the more plausible the structure.
Its origins come from this [2013 paper](https://doi.org/10.1093/bioinformatics/btt473), which talk about some of the shortcomings for using RMSD as the primary structural metric when assessing model quality.

Here is how the AlphaFold authors think about how to use pLDDT scores to assess model quality, lifted from their [FAQ](https://alphafold.ebi.ac.uk/faq):

AlphaFold produces a per-residue estimate of its confidence on a scale from 0 - 100. This confidence measure is called pLDDT and corresponds to the model’s predicted score on the lDDT-Cα metric. It is stored in the B-factor fields of the mmCIF and PDB files available for download (although unlike a B-factor, higher pLDDT is better). pLDDT is also used to colour-code the residues of the model in the 3D structure viewer. The following rules of thumb provide guidance on the expected reliability of a given region:

- Regions with pLDDT > 90 are expected to be modelled to high accuracy. These should be suitable for any application that benefits from high accuracy (e.g. characterising binding sites).
- Regions with pLDDT between 70 and 90 are expected to be modelled well (a generally good backbone prediction).
- Regions with pLDDT between 50 and 70 are low confidence and should be treated with caution.
- The 3D coordinates of regions with pLDDT < 50 often have a ribbon-like appearance and should not be interpreted. We show in [our paper](https://doi.org/10.1038/s41586-021-03819-2) that pLDDT < 50 is a reasonably strong predictor of disorder, i.e. it suggests such a region is either unstructured in physiological conditions or only structured as part of a complex. (Note: this relationship has typically been tested in the context of well-studied proteins, which may have more evolutionarily-related sequences available than a randomly chosen UniProt entry.)
- Structured domains with many inter-residue contacts are likely to be more reliable than extended linkers or isolated long helices.
- Unphysical bond lengths and clashes do not usually appear in confident regions. Any part of a structure with several of these should be disregarded.

Note that the PDB and mmCIF files contain coordinates for all regions, regardless of their pLDDT score. It is up to the user to interpret the model judiciously, in accordance with the guidance above.

A point I would like to add is that the eye test is really important here.
When AlphaFold loses the plot, and is not confident in the structure, it will generate structures with large loopy regions that I think look something like  angel wings or spaghetti. Other groups beyond the DeepMind team have been finding the same types of relationships between disorder and low pLDDT scores (see https://doi.org/10.1002/pro.4466 or https://doi.org/10.3390/ijms23094591).
We'll be taking a closer look at the pLDDT scores when analyzing Example 2.

### Origins of the AlphaFold templates

If we dig a little deeper in the AlphaFold output, there is also a `msas` directory, which holds the multiple sequence alignments used to do templating.
If we look in the directory in the usual way from the terminal:

```bash
cd msas
ls
```

There are typically four files in there, `mgnify_hits.sto`, `pdb_hits.hhr`,   `small_bfd_hits.sto`, and `uniref90_hits.sto`.
These files are all human readable, if in a super-structured format, so you can open these files in `gedit` as well.
The `.sto` file extension is known in bioinformatics contexts as a [Stockholm format](https://en.wikipedia.org/wiki/Stockholm_format) file, which can hold multiple sequence alignments.
The purpose of the sequence alignments is to build the co-variance matricies that help AI determine what residues co-vary with one another, and thus are close in space.
By lining up multiple sequences and seeing what varies and what does not, AlphaFold can learn something about what the protein structures should be, even if no templates are provided.

However, AlphaFold also uses existing overlap with sequences found in the PDB to help determine the structure.
I personally find the `pdb_hits.hhr` to be the most informative file in the set, since it gives the PDB code of highly similar structures.
For instance, the top hits I see inside the PDB for our test sequence are [6BGN](https://www.rcsb.org/structure/6bgn), a tautomerase, [2OP8](https://www.rcsb.org/structure/2OP8), another tautomerase that crystalized as a multimer.
This tells me that this test protein we have been playing with is probably a tautomerase itself, and that the biological assembly is probably not monomeric.

## Structural quality visualization

To this point, we've mostly been looking at text files.
But the details encoded into the text files can also be shown visually.
The pLDDT scores in particular are encoded into the output `.pdb` files in the `beta` column, which is the column right after the coordinates and the `occupancy` column in the file.
The key is that visualization programs like VMD can readily use this information to assign color to their renderings, providing instant feedback.

You will recall from the first class period that in example 2, we calculated the same structure with two different dates for the latest PDB structures that could be used as a template.
The 2023 date basically allows AlphaFold to use any PDB ever published as part of the template for this [new protein structure](https://www.rcsb.org/structure/8IBQ) that was recently solved.
The date in 1970 effectively means that AlphaFold is *only* allowed to rely on sequence alignment data and its neural network training to determine the structure.
We can check this from our output, using the `grep` command to search for a specific sentence in the logs.

```bash
cd $SCRATCH/Example2
grep "Total number of templates" *.log
```

Indeed, the `1970.log` file says that we have no templates in the pipeline, in contrast to the `2023.log` file.
We'll be using our results from this test case to look at how different the predicted structure based on this difference.
We have another VMD script prepared to help facilitate this, which we can run in a similar manner to exercise 1.

```bash
#Change directories again
cd $SCRATCH/vmdscripts
#This sets up VMD to be used.
module use /mnt/home/vermaasj/modules
module load VMD
#Load the results.
vmd -e loadexercise2.tcl
```

You should be greeted to a VMD window that has 3 structures all overlaid, the 1970 structure in blue, the 2023 template date structure in red, and the actual crystal structure is in gray.

<img src='files/screenshots/example2opening.png' width='1200'>

How different are these structures?
Visually, there are clearly pretty similar.
The RMSDs would be expected to be small, and that is what we see if we use the `RMSD trajectory tool` again.
You will note in the image below that I had to change the selection text to include `and not altloc B`.
This particular experimental structure has a few residues where there are multiple conformations observed in the PDB structure, encoded as "alternate locations", abbreviated here as `altloc`.
However, we cannot use the default `noh` selection, and instead need to use `backbone` as the selection modifier.
There are some missing heavy atoms in the experimental structure, so the number of atoms does not match between our AlphaFold models and the experimental structure.

<img src='files/screenshots/altlocrmsd.png' width='1200'>

From the image, we see that despite having no templates to work with, the backbone RMSD between the two AlphaFold models are within 0.12 Angstroms of RMSD!
Both are about equidistant from the recent experimental structure, with a backbone RMSD of about 0.6 Angstroms.
This is well below the experimental resolution of 1.45 Angstroms, so effectively all of the models are indistinguishable from one another.

Ok, so AlphaFold should *probably* be pretty confident in its results.
The program was not told about the experimental structure, but still it reproduced it really closely.
How confident was it really?
To visualize this clearly, we can open the `Graphical Representations` window (under `Graphics->Representations`) and color a selected structure by `beta`.
To make this clearer, you can hide molecules you aren't interested in by double clicking on the black "D" in the VMD main window (D=display in this case).
Once it turns red, you will hide that molecule from view until you double click the D again to bring it back.

<img src='files/screenshots/openinggraphicalrepresentations.png' width='1200'>

Within the graphical representations window, we can select the molecule we are editing the selection for on the top of the window from the drop-down box.
But what I think is more instructive is to change the representation to coloring method `beta`, which uses the maximum and minimum beta values encoded in the structure to color the protein.
If we click on the `Trajectory` tab of the `Graphical Representations` window, we find the `Color Scale Data Range`, which shows you the minimum and maximum values for the `beta` column (the pLDDT score).

<img src='files/screenshots/colorbybeta.png' width='1200'>

At this point, lets consider some questions:

- How different are the pLDDT scores when there are no templates (1970), or a number of template structures in the PDB (2023)?
- How important is structural homology/good templates to predicting an accurate structure?
- Where are the pLDDT scores the lowest in the structure? What is special about these parts of the protein?
- Based on the FAQ excerpt, how confident would you be as a structural biologist in making predictions based on these predicted structures?

## Variability in AlphaFold models

So far, we've been taking pains to only load in the single-best model that AlphaFold generated.
But at least on the default settings, AlphaFold will output multiple different potential structures.
Sometimes, these are pretty similar, particularly for single domain proteins.
But for protein complexes, sometimes AlphaFold will generate multiple conformations, and it is up to the user to determine which conformation makes the most sense.
We again have a prepared script (`loadexercise3.tcl`) to investigate in `gedit`, and to run in the terminal.

```bash
#Change directories again
cd $SCRATCH/vmdscripts
#This sets up VMD to be used. (Not technically needed every time if you are using the same terminal)
module use /mnt/home/vermaasj/modules
module load VMD
#Load the results.
vmd -e loadexercise3.tcl
```

That will bring up a screen that looks something like what is below.
Note that if you click in the lower right of the VMD main window, you can animate through the 25 different structures for the Barnase-Barstar complex that AlphaFold generated.
How consistent are the 25 different structures for the complex?
Here is an instance where we can quantify that through the RMSD trajectory tool again.
How big is the RMSD in this case?

<img src='files/screenshots/posecomparison.png' width='1200'>

Now, it is not always sunshine and rainbows.
In my own research, I'm working with this trimeric protein, which is known to biochemically interact with another protein.
If we load the AlphaFold results for this setup with a different script, and play the animation forward, the result is very different!

```bash
#Change directories again
cd $SCRATCH/vmdscripts
#This sets up VMD to be used. (Not technically needed every time if you are using the same terminal)
module use /mnt/home/vermaasj/modules
module load VMD
#Load the results.
vmd -e loadPduST.tcl
```

The astute among you may wonder why this system is a bigger challenge.
After all, nothing really fundamental changed in the input or the way I was running AlphaFold.
The protein is bigger for sure, which might play a role since there are more potential combinations for how things might interact.
It probably also does not help that there are fewer good templates for how this protein might interact compared with crystal structures for Barnase/Barstar.

However, I think that the real difference is in the biochemistry.
We intentionally picked a really strong interaction between Barnase and Barstar as our reference, which has a very well defined and tight-binding interaction.
The PduST system may simply not have as strong of a binding interaction, perhaps even reflecting transient binding.
This is a real, testable biochemistry hypothesis that can be experimentally verified, even if it is not the clearest.

## Wrapping up

At this point, you hopefully have a flavor for what all can be done with AlphaFold and similar artificial intelligence tools.
They can clearly predict protein structure, and often reasonably well.
However, in cases where the methods are asked to predict something far outside of their training data, they can struggle.
But, given how *cheap* it is to run AlphaFold and similar methods, it never hurts to take a look what kinds of hypotheses you can generate based on their predictions.
