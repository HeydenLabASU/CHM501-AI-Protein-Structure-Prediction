# Day 1

What AlphaFold and similar tools do is to combine information from structural and sequence databases to predict protein structures.
The main goals for the first day are to:
- Get to know some of the protein structure prediction webservers
- Learn how to use local high performance computing resources to predict more complicated protein structures

## Do I need to run AlphaFold at all?

DeepMind, the Google division that developed AlphaFold, has run the prediction software on many proteins already!
These are often available on [Uniprot](https://www.uniprot.org/).
For instance, in addition to the many solved structures for insulin, the [Uniprot entry for insulin](https://www.uniprot.org/uniprotkb/P06213/entry) also has the AlphaFold structure available for download.

<img src='files/screenshots/uniprotpicture.png' width='1200'>

DeepMind has already [predicted the structure for all proteins in humans and in multiple model organisms](https://www.deepmind.com/blog/alphafold-reveals-the-structure-of-the-protein-universe), and made these available to the public through the [https://alphafold.ebi.ac.uk/](AlphaFold Protein Structure Database).

## Webservers!

If you really just want an answer for a protein structure *right away*, the easiest thing to do is to run AlphaFold or another protein structure prediction tool within a webserver.
In this scenario, you will upload one or multiple protein sequences of interest to a website, which will use computing resources provided by someone else to predict the protein structure.
A popular webserver for protein prediction is [ColabFold](https://github.com/sokrypton/ColabFold), which I personally has the slickest interface to do AlphaFold-like things, since it implements everything as a python notebook and is quite interactive.
ColabFold's claim to fame is that it has a cheaper way of doing the initial sequence alignments and homology searches, which are described in the [companion paper to the webserver](https://doi.org/10.1038%2Fs41592-022-01488-1).

There are other webservers too.
Protein structure prediction is a rapidly evolving field where multiple groups have their own ideas as to how they should take the ideas that AlphaFold pioneered and make them work better, or take less compute time.
Some really big names are out there, including tech giants like Meta making their own tools to [predict protein structure](https://esmatlas.com/), which was published in [_Science_ in 2023](https://doi.org/10.1126/science.ade2574).
Other, more classical approaches, such as I-TASSER, were the best options before machine learning and AI methods for protein structure prediction became widespread.
These [webservers still exist](https://zhanggroup.org/I-TASSER/), and provide a window into the pre-AlphaFold universe of protein structure prediction.

Just so we get a sense of how these webservers work, lets try a few.
Here is a short peptide sequence that contains some protein secondary structure, a helix and a few beta sheets.
```
PIAQIHILEGRSDEQKETLIREVSEAISRSLDAPLTSVRVIITEMAKGHFGIGGELASK
```
Lets copy that amino acid sequence and use that as an input for the following webservers:

- [ESMFold](https://esmatlas.com/resources?action=fold) by Meta
- [ColabFold](https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2.ipynb), which had an MSU postdoc working on the paper and implementation
- [RoseTTAFold](https://robetta.bakerlab.org) from the Baker lab a U. Washington, which requires making an account, and has a LONG queue time, since it uses distributed computing.
- [D-I-Tasser](https://zhanggroup.org/D-I-TASSER/) by the Zhang group at U. Michigan
- [TopModel](https://cpclab.uni-duesseldorf.de/topsuite/topmodel.php), developed by the [Gohlke group at U. Dusseldorf](https://doi.org/10.1021/acs.jcim.0c01202)

Questions to consider as you are going through the webservers:

1. How fast is the webserver at returning a result?
2. In entering the sequence, is it obvious if the method can predict protein complexes?
3. Do the output structures look similar to one another?
4. How would you save the output structure to my own computer for further analysis?

Be warned, some of these webservers are relatively slow, so keep the tab open in the background as you work through the list.
*Save the structure files to your own computer! We'll analyzing the output later!*

## Running AlphaFold Yourself!

Now lets pretend that you cannot use a webserver.
Maybe your protein is too complicated to solve on the computing resources folks will give away for free, or you are working in a setting where the protein sequence is a trade secret that you cannot disclose to a webserver.
What do you do then?
The model for AlphaFold2 has been released to the public, and is available for download on [github](https://github.com/google-deepmind/alphafold).
Take a minute to read through the instructions.
We won't be following them, since on our supercomputing resources on campus, someone has already gone through the effort of installing AlphaFold for us.
But, if you wanted to install AlphaFold2 on your own computer, you can!

Anyway, we will be using the supercomputing resources at ICER, which is the organization that runs the high performance computing cluster here on campus.
They have [excellent tutorials](https://icer.msu.edu/education-events/desire2learn) through D2L for using the resources ICER provides.
We will use their [OnDemand](https://ondemand.hpcc.msu.edu) facilities hosted at https://ondemand.hpcc.msu.edu, which will give you a window like this:

<img src='files/screenshots/icerondemand.png' width='1200'>

We are interested in the `Interactive Desktop` application, which will let us use graphical tools on the supercomputer directly.

<img src='files/screenshots/interactivedesktopsetup.png' width='1200'>

The default options are fine, and will get you access to a virtual network connection (VNC) to access a desktop running on the supercomputer.
Once the session opens, we are going to be looking to open a terminal that we will be able to enter commands into.

<img src='files/screenshots/terminalopening.png' width='1200'>

Now, we have prepared some activities inside of a directory that we will be copying to our own storage space so that we have a clean workspace.
You can copy this box directly into your terminal window, which will execute the commands one at a time.
This is written in the `bash` programming language, where any line that begins with a `#` is a comment, which I'm using here to explain what each command is doing.

```bash
#change to the scratch directory, which is a nice and fast high performance file system.
#The scratch directory is specific to an individual user
cd $SCRATCH
#list the "present working directory", in case you need to navigate to it or refer to it in a script
pwd
#Notice that $SCRATCH is an example of a bash variable, which you can print out with the "echo" command
echo $SCRATCH
#Now we initiate the file transfer from the prepared files inside /mnt/research/BMB829_FS23_S305 to your working directory.
rsync -vr /mnt/research/BMB829_FS23_S305/ $SCRATCH/.
#"list" the files in your scratch directory.
ls
# There should now be a bunch of examples (Example1, Example2, and so on) that we will be exploring.
```

To copy these commands into the remote terminal, look for a button on the left-hand side of the web-browser window.
If you expand the menu, it will give you options, including a clipboard that you can paste text into (see below).
Once the contents of the clipboard contain what you want to include in the box, you can use the right-mouse button to paste into the open terminal.
You may also find it helpful to open these instructions in a webbrowser in the remote session, where you can copy and paste directly.

<img src='files/screenshots/clipboard.png' width='1200'>

The examples are there to serve as a guide for how you would run your own AlphaFold calculations on a supercomputer.
- [Example 1](Example-1) is for the same single domain protein we have been using, which folds into secondary structure that is well established.
- [Example 2](Example-2) will use AlphaFold to predict the structure for a protein that was recently solved, and is available from the [Protein DataBank](https://www.rcsb.org/structure/8IBQ). In this example, we will be changing the template date parameter.
- [Example 3](Example-3) is for a protein complex, in this case the [Barnase-Barstar](https://www.rcsb.org/structure/1brs) complex, which is among the tightest binding protein complexes known despite its relatively small size.

### Example 1

Starting from the `$SCRATCH` directory, we need to enter the `Example1` directory, and list the files in the directory with the following terminal commands:

```bash
#Change directory
cd Example1
#List the files
ls
```

The output should list a `.fasta` file, and a `.sh` file.
We can open both files with the `gedit` program, a simple graphical text editor, or those who are inclined to terminal text can use `cat` to print out the contents of both files.

```bash
#Open the files for editing.
#I'm using "*" as a "wildcard", so that I don't need to type out the full name.
gedit *.fasta *.sh
#cat *.fasta *.sh
```

The `.fasta` file is just the protein sequence we are interested in solving the protein structure for, and have run through multiple webservers.
We can close that tab and focus on the contents of `run.sh`.
`run.sh` is a shell script, which tells the computer what to do.
The top lines that start with `#SBATCH` will be there to tell the job scheduler for the supercomputer how many and what kind of resources you want.
For instance, in this script we are naming our job, promising the scheduler we will be done in 4 hours if we are given 1 of the faster GPUs, 8 CPUs, and 12GB of RAM.
The next part of `run.sh` is making sure variables are set up and printing things to the log file so we can follow them later, and some preliminaries like making sure we are in the right directory.

The line that actually runs AlphaFold is the line that starts with `python3`, which will run a [singularity](https://en.wikipedia.org/wiki/Singularity_(software)) container that has AlphaFold built into it.
The advantage to these containers from a high-performance computing standpoint is that so long as the container is not altered, the software environment stays consistent to the program inside the container.
This means that AlphaFold will always see the same version of system libraries that it depends on to work, making the work reproducible by others who might have different versions of a library.
From our perspective, what we really care about are the options that we are passing to the singularity container, which also get passed into AlphaFold.
Note that we can pass all the same flags to AlphaFold as are documented in the main [AlphaFold github page](https://github.com/google-deepmind/alphafold#running-alphafold).

```bash
python3 ${ALPHAFOLD_DIR}/run_singularity.py \ 
    --use_gpu \ #Use the GPU, which makes the neural network calculations faster
    --output_dir=$output_dir \ #Here is where I want to put the result
    --data_dir=${ALPHAFOLD_DATADIR} \ #Here is where the AlphaFold data like pdb sequences live
    --fasta_paths=input.fasta \ #Here is our input fasta sequence
    --max_template_date=2020-05-14 \ #When looking for PDB templates, this is the maximum date we will consider
    --model_preset=monomer \ #We are predicting a monomeric protein
    --db_preset=reduced_dbs #Use the reduced database
```

In any event, we can close the files and actually submit the AlphaFold calculations to the supercomputer.
MSU's HPCC uses the SLURM job scheduler, which uses the command `sbatch` to submit a calculation to the computer.
In the dawn of computing, you used to have to submit a calculation as a batch of punchcards, which was know colloquially as a "batch job" or simply a "job", so I'm switching to this terminology now.
`squeue` can be used to see what jobs are in the job queue that the supercomputer is executing or are waiting to execute.
Since most of the jobs are submitted by others, passing the `--me` flag will give you information on jobs that *you* currently have running.
Below are the terminal commands.

```bash
#Sbatch submits the job to the queue
sbatch run.sh
#Squeue checks if it is running.
squeue --me
```

If you need to cancel a job, like if you accidentally ran `sbatch` more than once, the correct syntax is to look up the job number using `squeue`, and pass the job number to `scancel`. For instance if the job number you wanted to cancel was 1234, the correct command is `scancel 1234`.
We'll be doing a more thorough assessment of the output in the next class period, but for now, we can move to Example 2.

### Example 2

First, we'd move into the `Example2` directory:

```bash
#Change directory
cd $SCRATCH/Example2
#List the files
ls
```

Again, there is a `run.sh` file and a `.fasta` file (along with the example outputs), and are analogous to what we have worked with before.
If we open `run.sh`, the only real difference is that I've executively decided to change the `--max_template_date` argument, and that the `output_dir` variable also includes a year in it.
The `--max_template_date` argument controls the most recent date of release into the Protein Data Bank that AlphaFold is allowed to use when looking at templates.
If you want to see how good (or bad!) of a job AlphaFold does when you take away all of the solved cryo-EM, x-ray, and NMR structures, you can set this date back to sometime in the 1970s before many structures were published.
It does not end well, but this idea is what we are testing with the submission script `run-notemplates.sh`, which sets the `--max_template_date` to 1970.

```bash
sbatch run.sh
sbatch run-notemplates.sh
squeue --me
```

Since the protein size is relatively small, this will take about the same amount of time as Example 1.

We intentionally picked this [protein, BRD4](https://www.rcsb.org/structure/8IBQ), since it was released in early October 2023.
That means that this protein sequence is *not* in the existing databases that AlphaFold is using to guess the structure, and will let us more concretely see what happens if you provide AlphaFold with a sequence to fold that is new.
This is also key for method validation, since you can hold data back to first predict new structures, and then see how good or bad your predictions are by comparing to the experimental structures.

### Example 3

The examples up to this point were all of proteins whose functional form was a single polypeptide chain.
However, other proteins function as a complex or multimer, and AlphaFold has the capability of predicting the folded structures for these complexes as well.
Indeed, there are demonstrated cases in the [literature](https://doi.org/10.1002/pro.4368) where the protein structure depends on oligomeric state, and AlphaFold can capture these features.
To do so is actually pretty straigthforward.
Looking at the files in the example 3 directory:

```bash
#Change directory
cd $SCRATCH/Example3
#List the files
ls
```

There will be a `.fasta` file as normal.
If we open it (`gedit 1BRS.fasta`), we see that there are two sequences listed, one for barnase, and one for barstar.
Barnase-barstar is one of the tightest protein-protein complexes known, with a [standard binding free energy of around -20 kcal/mol](https://pubs.acs.org/doi/10.1021/ct400273t).
Both components also happen to be very small, so the computational cost is not very high to try out and see what happens if we try to predict the protein structure.

In this directory, there will be two run scripts, `run-monomer.sh` and `run-multimer.sh`, which we can again open with `gedit`. 
The only difference between these scripts is the method AlphaFold will be using to predict the protein structure.
`run-monomer.sh` is using the `monomer` model preset, while `run-multimer.sh` is using the `multimer` preset.
`run-monomer.sh` will bail pretty quickly when you run it, as AlphaFold with the monomer preset expects only a single protein sequence in a fasta file.
`run-multimer.sh`, however, should run to completion, and generate output.

```bash
sbatch run-monomer.sh
sbatch run-multimer.sh
squeue --me
```

### Day 1 Odds and Ends

Now lets say that you wanted to do this outside of the examples that have been provided.
How would you run AlphaFold?
What you fundamentally need is a `.fasta` file for your protein sequence of interest, and some sort of script (the `.sh` files) to tell AlphaFold how to treat that input to create output.
How would you go about it?
In large part, the variety of `.sh` files we have pre-assembled should be a helpful guide, and web sources for protein sequences are all over the place.

To assemble a final script, some terminal commands may also be useful.
`wget` can be used to download files directly from the internet to a supercomputer.
For example `wget https://rest.uniprot.org/uniprotkb/P00648.fasta` would download the protein sequence for a specific protein from the uniprot database.
Using `cp` to copy files or `mv` to rename/move files can also be useful to you as you start building up your scripts to tackle your own research questions.
`mkdir` makes directories as well.
If you need to change directories, `cd ..` will go one directory "up" the tree.
