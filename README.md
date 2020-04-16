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
conda create -n mummer-ggplot -c bioconda -c r mummer=3.23 deepstats
```

Clone this repository and add the bin folder to your PATH

```
git clone https://github.com/gtrichard/mummer-ggplot/
cd mummer-ggplot/bin
mggpath=$(pwd)
to_export="export PATH=$PATH:$mggpath"
cat $to_export >> ~/.bashrc
```

To update the tool simply go to the cloned repository location and type `git pull`

mggNucmer uses SlurmEasy, taken from https://github.com/dpryan79/Misc/blob/master/MPIIE_internal/SlurmEasy

You may need to configure it for your cluster (mainly QUEUE line 22, QUEUES line 28 and MAINTEMP line 30).

## Tools

### mggNucmer

```
usage: mggNucmer --input  fastas/ --output deltas/

mggNucmer launches nucmer from mummer3 on several chromosomes of multiple
genomes at once. It takes a folder with fasta files as input and outputs delta
files in a target folder (mummer format). This tool should be launched
directly, not with sbatch. It uses SlurmEasy (and thus the slurm job
scheduler) to launch jobs.

optional arguments:
  -h, --help            show this help message and exit
  --input FOLDER, -i FOLDER
                        Input folder with fasta files. All files finishing by
                        '.fa' or '.fasta' will be used. They must be named
                        using the following convention:
                        'genome_name.chromosome.fa', for instance 'dm3.2L.fa'.
  --reference FOLDER, -r FOLDER
                        Folder path containing all the reference genome '.fa'
                        or '.fasta' files. This reference will be aligned to
                        all fasta files stored in the input directory,
                        chromosome by chromosome (with matching names). Fasta
                        files must be named using the following convention:
                        'genome_name.chromosome.fa', for instance 'dm3.2L.fa'.
  --chromosomes CHROMOSOMES [CHROMOSOMES ...], -c CHROMOSOMES [CHROMOSOMES ...]
                        Here you can define the chromosome names you want the
                        program to run on. Simply put the chromosome names one
                        after the other, separated by a space, for instance
                        `-c 2R 2L 3L 3R`.
  --output FOLDER, -o FOLDER
                        Output folder name where delta files will be stored.
  --cpu INT, -p INT     Number of CPU to use. Default: 2
  --mem INT, -m INT     Memory to use per CPU. It can be expressed in Gb or
                        Mb: 2G or 2000M. This is multiplied by --cpu. Default:
                        2G

```


### mggFilter

```
usage: mggFilter --input  deltas/ --identity 98 --length 1000

mggFile launches delta-filter from mummer3 on several chromosomes of multiple
genomes at once. It takes a folder with delta files as input and outputs
delta.filtered files in the same folder (mummer format). This tool should be
launched by sbatch or SlurmEasy, not directly (opposite of mggNucmer).

optional arguments:
  -h, --help            show this help message and exit
  --input FOLDER, -i FOLDER
                        Input folder with delta files. All files finishing by
                        '.delta' will be used. They must be named using the
                        following convention:
                        'genome_name.chromosome.fa.delta', for instance
                        'dm3.2L.fa.delta'.
  --identity INT, -d INT
                        Minimum identity of the alignments found by nucmer
                        between reference and query to be kept in the filtered
                        delta file. Check delta-filter documentation from
                        Mummer for more information.
  --length INT, -l INT  Minimum length of the alignments found by nucmer
                        between reference and query to be kept in the filtered
                        delta file. Check delta-filter documentation from
                        Mummer for more information.
```

### mggPlot

```
usage: mggPlot --input  deltas/ --output plot.pdf --ncol 12 --width 19 --height 15.7

mggPlot takes a folder as input and collects all delta.filtered files to plot
them in an automatic manner. This tool can be executed via sbatch or with
SlurmEasy.

optional arguments:
  -h, --help            show this help message and exit
  --input FOLDER, -i FOLDER
                        Input folder with delta.filtered files. All files
                        finishing by '.delta.filtered' will be used. They must
                        be named using the following convention:
                        'genome_name.chromosome.fa.delta.filtered', for
                        instance 'dm3.2L.fa.delta.filtered'.
  --output FILENAME, -o FILENAME
                        Output file name for the PDF file.
  --width FLOAT         Plot width. 15.7 inch by default.
  --height FLOAT        Plot height. 17 inch by default.
  --ncol INT            Number of columns for the plot.
  --nrow INT            Number of rows for the plot.
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
