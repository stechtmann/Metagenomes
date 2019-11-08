# Metagenomes
Metagenome Analysis with Brennan


# Organization
-  Download your data into a data diraectory 
-  Make directories for analysis
```{BASH}
mkdir fastqc
mkdir trimming
mkdir assembly
mkdir annotation
```
# Quality control
```{BASH}
fastqc 1054_116-G3_S770-S540__GTCCTAAG-AGCTACCA__S15_R2_001.fastq.gz --outdir=fastqc/
```
Repeat for all files
# Quality Trimming
```{BASH}
trimmomatic-0.36.jar PE -phred33 1054_116-G3_S770-S540__GTCCTAAG-AGCTACCA__S15_R1_001.fastq.gz 1054_116-G3_S770-S540__GTCCTAAG-AGCTACCA__S15_R2_001.fastq.gz trimming/1054_116-G3_R1_paired.fq.gz trimming/1054_116-G3_R1_unpaired.fq.gz trimming/1054_116-G3_R2_paired.fq.gz trimming/1054_116-G3_R2_unpaired.fq.gz ILLUMINACLIP:/home/bmcoles/miniconda3/pkgs/trimmomatic-0.39-1/share/trimmomatic/adapters/NexteraPE-PE.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```
Repeat for all metagenomes
# Quality Control of trimmed reads
```{BASH}
fastq trimming/1054_116-G3_R1_paired.fq.gz --outdir=fastqc/
```
Repeat for all files
# Assembly of trimmed reads
```{BASH}
spades.py -k 21,51,71,91,111,127 --careful --pe1-1 trimming/1054_116-G3_R1_paired.fq.gz --pe1-2 trimming/1054_116-G3_R2_paired.fq.gz --pe1-s trimming/1054_116-G3_R1_unpaired.fq.gz -o assembly/1054_116-G3_Spades_output
```
Repeat for all metagenomes

# Annotation
### First change the names of the contigs to something that prokka likes more.
```{BASH}
awk '/^>/{print ">1054_116_G3_" ++i; next}{print}' < assembly/1054_116-G3_Spades_output/contigs.fa > assembly/1054_116_G3_Spades_output/contigs_names.fa
```
Repeat for all assemblies. 

### Now run prokka
```{BASH}
prokka --outdir annotation/1054_116-G3_prokka --prefix 1054_116-G3 assembly/1054_116_G3_Spades_output/contigs_names.fa --locustag 1054_116_G3
```
# Searching for CooS genes
- Create a new directory called `CooS_hmm`
```{BASH}
mkdir Coos_hmm
```
- Download your HMM in a `.hmm` format and upload to `CooS_hmm` directory

### Search the .faa files from your annotations with you HMM
```{BASH}
hmmsearch CooS_hmm/CooS.hmm annotation/1054_116-G3_prokka/1054_116-G3.faa > CooS_hmm/1054_116-G3.out
```
