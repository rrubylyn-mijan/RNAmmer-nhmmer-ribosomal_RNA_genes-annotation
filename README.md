# rRNA Gene Annotation using RNAmmer and nhmmer

This workflow describes how to annotate ribosomal RNA genes from assembled wheat genomes using **RNAmmer** and **nhmmer** on HPC systems like **Atlas** or **CCAST**.

---

## Installation of RNAmmer

1. **Request download link** from [RNAmmer website](https://services.healthtech.dtu.dk/cgi-bin/sw_request?software=rnammer&version=1.2&packageversion=1.2&platform=Unix)

2. **Transfer the package to your HPC**:
scp ~/Downloads/rnammer-1.2.Unix.tar.gz your_username@atlas-login.hpc.msstate.edu:/home/your_username

3. **Extract the archive**:
tar -xzf rnammer-1.2.Unix.tar.gz

4. **Edit the rnammer script to update install path**:
nano rnammer

# Change these lines:
my $INSTALL_PATH = "/90daydata/oat_renewal/Ruby/rnammer_envs";
my $RNAMMER_CORE = "$INSTALL_PATH/core-rnammer";

5. **Test the installation**:
perl /90daydata/oat_renewal/Ruby/rnammer_envs/rnammer -S bac -m lsu,ssu,tsu -gff ecoli.fsa

6. **Create directory structure**:
ssh atlas-login
mkdir -p /90daydata/oat_renewal/Ruby/rnammer_envs
mkdir -p /90daydata/oat_renewal/Ruby/rrna_results

---

## nhmmer-based rRNA Annotation

1. **Load HMMER module**:
ml hmmer/3.4

---

## SLURM Job Script Examples

### Sumai 3
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 8
#SBATCH -p atlas
#SBATCH --mem=100GB
#SBATCH -J rrna_sumai3
#SBATCH -A genolabswheatphg

ml hmmer/3.4

QUERY_HMM=/90daydata/oat_renewal/Ruby/rnammer_envs/lib/euk.ssu.rnammer.hmm
TARGET_FASTA=/90daydata/oat_renewal/Ruby/Sumai3_pm_v2.fasta
OUTPUT_DIR=/90daydata/oat_renewal/Ruby/rrna_results
OUTPUT_FILE=${OUTPUT_DIR}/sumai3_rrna_annotations.tbl

mkdir -p ${OUTPUT_DIR}

/apps/spack-managed/gcc-11.3.1/hmmer-3.4*/bin/nhmmer \
  --tblout ${OUTPUT_FILE} \
  --cpu 8 --dna \
  ${QUERY_HMM} ${TARGET_FASTA}

---

### Rollag
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 8
#SBATCH -p atlas
#SBATCH --mem=100GB
#SBATCH -J rrna_rollag
#SBATCH -A genolabswheatphg

ml hmmer/3.4

QUERY_HMM=/90daydata/oat_renewal/Ruby/rnammer_envs/lib/euk.ssu.rnammer.hmm
TARGET_FASTA=/90daydata/oat_renewal/Ruby/Rollag_pm_v1.fasta
OUTPUT_DIR=/90daydata/oat_renewal/Ruby/rrna_results
OUTPUT_FILE=${OUTPUT_DIR}/rollag_rrna_annotations.tbl

mkdir -p ${OUTPUT_DIR}

/apps/spack-managed/gcc-11.3.1/hmmer-3.4*/bin/nhmmer \
  --tblout ${OUTPUT_FILE} \
  --cpu 8 --dna \
  ${QUERY_HMM} ${TARGET_FASTA}

---

### Glenn
#!/bin/bash
#SBATCH -N 1
#SBATCH -n 8
#SBATCH -p atlas
#SBATCH --mem=100GB
#SBATCH -J rrna_glenn
#SBATCH -A genolabswheatphg

ml hmmer/3.4

QUERY_HMM=/90daydata/oat_renewal/Ruby/rnammer_envs/lib/euk.ssu.rnammer.hmm
TARGET_FASTA=/90daydata/oat_renewal/Ruby/Glenn_pm_v1.fasta
OUTPUT_DIR=/90daydata/oat_renewal/Ruby/rrna_results
OUTPUT_FILE=${OUTPUT_DIR}/glenn_rrna_annotations.tbl

mkdir -p ${OUTPUT_DIR}

/apps/spack-managed/gcc-11.3.1/hmmer-3.4*/bin/nhmmer \
  --tblout ${OUTPUT_FILE} \
  --cpu 8 --dna \
  ${QUERY_HMM} ${TARGET_FASTA}

---

## Post-Processing Commands

# Count total rRNAs
grep -c "euk.ssu.rnammer.degap" sumai3_rrna_annotations.tbl

# Count rRNAs per chromosome
awk '{count[$1]++} END {for (chr in count) print chr, count[chr]}' sumai3_rrna_annotations.tbl

---

**Maintainer:**  
Ruby Mijan
