# mummer-ggplot
Nucmer from Mummer3 followed by ggplot for nicer and more customisable genome alignment plots.

## Installation

Install conda if it's not already installed, get your distribution here: https://docs.conda.io/en/latest/miniconda.html . For a user on a UNIX-based HPC/cluster, the following can be done, please answer 'yes' to every question. You need to close the current window and open a new one after the installation is done:

```
cd ~
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod +x Miniconda3-latest-Linux-x86_64.sh
./Miniconda3-latest-Linux-x86_64.sh
```

Create a conda virtual environment for mummer-ggplot

```
conda create -n mummer-ggplot -c bioconda -c r mummer3 r-base r-dplyr r-magrittr r-ggplot2 r-tidyr r-stringr
```

Clone this repository and add the bin folder to your PATH

```
git clone https://github.com/gtrichard/mummer-ggplot/
cd mummer-ggplot/bin
mumggpath=${pwd}
cat 'export PATH=$PATH:$mumggpath' >> ~/.bashrc
```


## Tutorial

Create a test directory next to the mummer-ggplot git repository that you cloned in the installation steps.

```
mkdir mummer-ggplot_test
cd mummer-ggplot_test
```

Activate the conda virtual environment.

```
conda activate mummer-ggplot
```

Execute the first step: nucmer. It will launch jobs on a slurm queue.

```
mggNucmer ../mummer-ggplot/fasta/reference.fa ../mummer-ggplot/fasta/chromosomes.txt ../mummer-ggplot/fasta/ mggNucmer-out
```

Execute the delta-filter step.

```
mggFilter mggNucmer-out 98 1000
```

Execute the plotting step.

```
mggPlot mggNucmer-out
```

Check your plots :)

## Tools

### mggNucmer

Usage:

```
mggNucmer reference_fasta chromosomes in_folder out_folder
```

Description:

It launches nucmer from Mummer using `reference_fasta` as the reference and all fasta files in `in_folder` as queries. Fasta files names should have the following nomenclature: `genome.chromosome.fa`. Since the tool is made to work on multiple chromosomes, a `chromosomes.txt` file is necessary, which contains one chromosome name per line. mggNucmer will only look for the fasta files whose name is matching those chromosomes. mggNucmer outputs delta files in the `out_folder`.

### mggFilter

Usage:

```
mggFilter in_folder identity length
```

Description:

It launches nucmer from Mummer on all delta files in a given folder and outputs delta.filtered files in the same folder. The second paramter corresponds to `-i` (identity) and the third parameter to `-l` (length of alignments) in nucmer.

### mggPlot

Usage:

```
mggPlot in_folder width length
```

Description:

It launches a R script that takes all `*delta.filtered` files as input (from the `in_folder`) and outputs pdf plots as `*delta.filtered.pdf` files. Width and Length are used to customise the size of the plots.
