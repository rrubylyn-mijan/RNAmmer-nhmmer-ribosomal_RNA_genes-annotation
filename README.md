# rRNA Gene Annotation using RNAmmer and nhmmer

This workflow describes how to annotate ribosomal RNA genes from assembled wheat genomes using **RNAmmer** and **nhmmer** on HPC systems like **Atlas** or **CCAST**.

---

## Installation of RNAmmer

1. **Request download link** from [RNAmmer website](https://services.healthtech.dtu.dk/cgi-bin/sw_request?software=rnammer&version=1.2&packageversion=1.2&platform=Unix)

2. **Transfer the package to your HPC**:
```bash
scp ~/Downloads/rnammer-1.2.Unix.tar.gz your_username@atlas-login.hpc.msstate.edu:/home/your_username
```
3. **Extract the archive**:
```bash
tar -xzf rnammer-1.2.Unix.tar.gz
```
4. **Edit the rnammer script to update install path**:
```bash
nano rnammer
```
# Change these lines:
```bash
my $INSTALL_PATH = "/directory/this/saved/rnammer_envs";
my $RNAMMER_CORE = "$INSTALL_PATH/core-rnammer";
```
5. **Test the installation**:
```bash
perl /directory/this/saved/rnammer_envs/rnammer -S bac -m lsu,ssu,tsu -gff ecoli.fsa
```
6. **Create directory structure**:
```bash
ssh atlas-login
mkdir -p /directory/this/saved/rnammer_envs
mkdir -p /directory/this/saved/rrna_results
```
---

## nhmmer-based rRNA Annotation

1. **Load HMMER module**:
```bash
ml hmmer/3.4
```
---

## SLURM Job Script Examples

### Wheat
```bash
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 8
#SBATCH -p atlas
#SBATCH --mem=100GB
#SBATCH -J rrna_wheat
#SBATCH -A genolabswheatphg

ml hmmer/3.4

QUERY_HMM=/directory/this/saved/rnammer_envs/lib/euk.ssu.rnammer.hmm
TARGET_FASTA=/directory/this/saved/wheat.fasta
OUTPUT_DIR=/directory/this/saved/rrna_results
OUTPUT_FILE=${OUTPUT_DIR}/wheat_rrna_annotations.tbl

mkdir -p ${OUTPUT_DIR}

/apps/spack-managed/gcc-11.3.1/hmmer-3.4*/bin/nhmmer \
  --tblout ${OUTPUT_FILE} \
  --cpu 8 --dna \
  ${QUERY_HMM} ${TARGET_FASTA}
```

## Post-Processing Commands

# Count total rRNAs
```bash
grep -c "euk.ssu.rnammer.degap" wheat_rrna_annotations.tbl
```
# Count rRNAs per chromosome
```bash
awk '{count[$1]++} END {for (chr in count) print chr, count[chr]}' wheat_rrna_annotations.tbl
```
---

**Maintainer:**  
Ruby Mijan
