# cluster_search
A lightweight pHMM tool for linking hits to user supplied sequences in multiple annotate genomes.
![Overview of cluster search workflow](.map.png)
This tool takes any number of user supplied protein multiFASTAs, aligns them, prepares a pHMM from the alignment, and applies the resulting pHMMS to annotated GenBank files. The hits are filtered for each pHMMs calculated trusted bitscore cutoff and the 10 kb context of each hit is extracted to a single GenBank output file.

It was developed with the intention of linking biosynthetic gene cluster (BGC) proteins of known sequences across multiple contigs in discontiguous genome assemblies. Alternatively, it could be applied to a set of genomes to extract desired protein sequences for a phylogenetic study.

The use can supply either a protein multiFASTA, an alignment multiFASTA, or pHMMs.
## Installation
This script has the dependencies `pyhmmer` and `biopython` (>=1.8) and can be installed using mamba as below:
```
mamba create -n cluster_search python3
source activate cluster_search
mamba install -c conda-forge biopython
mamba install -c bioconda pyhmmer
mamba deactivate
```
## Usage
This script has been tested on a PC with 12 × AMD Ryzen 5 5600X CPUs and 16 GB DDR3 RAM. It was able to search 5 fungal genomes in just under 10 seconds.
```
            |-|-|-|- Cluster Search -|-|-|-| 

usage: cluster_search_v3.py [-h] -g GBK_DIR -p PHMM_DIR [-b] [-a] [-r REQUIRED] [-m TRUSTED_MODIFIER] [-t TRUSTED_CUTOFFS_FILE]

Finds and extracts clusters of biosynthetic genes in an annotated genome using given pHMM models

optional arguments:
  -h, --help            show this help message and exit
  -g GBK_DIR, --gbk_dir GBK_DIR
                        path to genbank file(s)
  -p PHMM_DIR, --phmm_dir PHMM_DIR
                        path to pHMMs
  -b, --build           Build pHMM from multiple sequence alignments. Multiple sequence alignments must be 							in 'phmm_dir' and have suffix '.msa.fasta'
  -a, --align           Align multi-FASTA to make multiple sequence alignments. multi-FASTA must be in 								'phmm_dir' and have suffix '.prots.fasta'
  -r REQUIRED, --required REQUIRED
                        comma-separated list of pHMMs required to be in the cluster, e.g., phmmA,phmmB,phmmC
  -m TRUSTED_MODIFIER, --trusted_modifier TRUSTED_MODIFIER
                       	modifier larger than 0 and including 1 for calculating trusted cutoff bitscores. 1 = 					   	 no cutoff, 0.1 = very stringent. Use 'd' to override existing data and     
                        use defaults
  -t TRUSTED_CUTOFFS_FILE, --trusted_cutoffs_file TRUSTED_CUTOFFS_FILE
                        file containing tab-separated trusted cutoffs for each pHMM, e.g. 'phmmA bitscore'
```
> Note above that the varying input FASTA files for `--build` (`*.msa.fasta`) and `--align` (`*.prots.fasta`) will need to have an additional suffix to denote the type of data in each.

Some usage cases are below:

Basic usage: 
```
python3 cluster_search_v3.py -g gbk_dir -p phmm_dir
```
Usage if you want to use a modifier for calculating very stringent trusted cutoffs: 
```
python3 cluster_search_v3.py -g gbk_dir -p phmm_dir -m 0.1
```
Usage if you want to use a modifier for calculating very relaxed trusted cutoffs:
```
python3 cluster_search_v3.py -g gbk_dir -p phmm_dir -m 0.9
```
Usage if you want to filter hits to list of required pHMM hits: 
```
python3 cluster_search_v3.py -g gbk_dir -p phmm_dir -r list_required_models
```
Usage if you want to build pHMMs from multiple sequence alignments:
```
python3 cluster_search_v3.py -g gbk_dir -p phmm_dir -b
```
Usage if you want to align a multi-FASTA of proteins and build pHMMs from the multiple sequence alignments:
```
python3 cluster_search_v3.py -g gbk_dir -p phmm_dir -a -b
```
Inputs:
```
--gbk_dir   =   directory containing genbank files
--phmm_dir  =   directory containing pHMM files
--required  =   (optional) pHMMs that must be included in output clusters
```
