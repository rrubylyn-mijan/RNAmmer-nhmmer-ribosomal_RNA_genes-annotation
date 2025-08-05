\# rRNA Gene Annotation using RNAmmer and nhmmer



This workflow describes how to annotate ribosomal RNA genes from assembled wheat genomes using \*\*RNAmmer\*\* and \*\*nhmmer\*\* on HPC systems like \*\*Atlas\*\* or \*\*CCAST\*\*.



---



\## Installation of RNAmmer



1\. \*\*Request download link\*\* from \[RNAmmer website](https://services.healthtech.dtu.dk/cgi-bin/sw\_request?software=rnammer\&version=1.2\&packageversion=1.2\&platform=Unix).

2\. \*\*Transfer the package to your HPC\*\*:

```bash

&nbsp;  scp ~/Downloads/rnammer-1.2.Unix.tar.gz your\_username@atlas-login.hpc.msstate.edu:/home/your\_username

```

3\. Extract the archive:

```bash

tar -xzf rnammer-1.2.Unix.tar.gz

```

4\. Edit the rnammer script to update install path:

```bash

nano rnammer

\# Change these lines:

my $INSTALL\_PATH = "/directory/this/saved/rnammer\_envs";

my $RNAMMER\_CORE = "$INSTALL\_PATH/core-rnammer";

```

5\. Test the installation

```bash

perl /directory/this/saved/rnammer\_envs/rnammer -S bac -m lsu,ssu,tsu -gff ecoli.fsa

```

6\. Directory Setup in HPC

```bash

ssh atlas-login

mkdir -p /directory/this/saved/rnammer\_envs

mkdir -p /directory/this/saved/rrna\_results

```



\## nhmmer-based rRNA Annotation

1\. Load HMMER module (adjust to your cluster):

```bash

ml hmmer/3.4

```


\## SLURM Scripts

```bash

\#!/bin/bash

\#SBATCH -N 1

\#SBATCH -n 8

\#SBATCH -p atlas

\#SBATCH --mem=100GB

\#SBATCH -J rrna\_sumai3

\#SBATCH -A genolabswheatphg



ml hmmer/3.4



QUERY\_HMM=/directory/this/saved/rnammer\_envs/lib/euk.ssu.rnammer.hmm

TARGET\_FASTA=/directory/this/saved/Sumai3\_pm\_v2.fasta

OUTPUT\_DIR=/directory/this/saved/rrna\_results

OUTPUT\_FILE=${OUTPUT\_DIR}/wheat\_rrna\_annotations.tbl



mkdir -p ${OUTPUT\_DIR}



/apps/spack-managed/gcc-11.3.1/hmmer-3.4\*/bin/nhmmer \\

&nbsp; --tblout ${OUTPUT\_FILE} \\

&nbsp; --cpu 8 --dna \\

&nbsp; ${QUERY\_HMM} ${TARGET\_FASTA}

```



\## Post-Processing

```bash

\# 1. Count total rRNAs:

grep -c "euk.ssu.rnammer.degap" sumai3\_rrna\_annotations.tbl



\# 2. Count rRNAs per chromosome:

awk '{count\[$1]++} END {for (chr in count) print chr, count\[chr]}' sumai3\_rrna\_annotations.tbl

```



Maintainer:



Ruby Mijan







