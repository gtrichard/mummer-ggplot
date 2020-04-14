# mummer-ggplot
Nucmer from Mummer3 followed by ggplot for nicer and more customisable genome alignment plots.

## Installation

Install conda if it's not already installed, get your distribution here: https://docs.conda.io/en/latest/miniconda.html

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
mggNucmer ../mummer-ggplot/fasta/ mggNucmer-out
```

Execute the delta-filter step.

```
mggFilter mggNucmer-out
```

Execute the last plotting step.

```
mggPlot mggNucmer-out
```

Check your plots :)
