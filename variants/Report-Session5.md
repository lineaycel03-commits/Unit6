# Report : Mapping, variant calling & effect prediction 

## Authors : DORRA SDIRET & AYCEL LINE DJOUAMA

## Date : 25/01/2026

# 1. Solving exercices 
## Exercice 1: 
### 1.1 Two FASTQ files with the same name but different numeric suffixes
The numeric suffixes (_1 and _2) indicate that the sequencing data was produced using a paired-end sequencing protocol. In this method, each DNA fragment is sequenced from both ends:

<img width="945" height="106" alt="image" src="https://github.com/user-attachments/assets/282f9a39-4e9c-4b4d-a1f7-a6f1a2a1bfcd" />



•	1 (Forward Read): The sequence from the first end of the fragment.

•	2 (Reverse Read): The sequence from the opposite end of the same fragment.


### 1.2 Spotting the Differences between SAMEA2569438.chr10_1.fastq.gz and SAMEA2569438.chr10_2.fastq.gz

When comparing the reports for SAMEA2569438.chr10_1.fastq.gz and SAMEA2569438.chr10_2.fastq.gz, there are several key differences to note:

While the basic statistics like %GC (40%) and sequence length (30-83 bp) are identical, a key technical difference is visible in the quality profiles.

**SAMEA2569438.chr10_2.fastq.gz** shows a more pronounced drop in quality toward the end of the read compared to **SAMEA2569438.chr10_1.fastq.gz.**
There is a slight variation in duplication, with **78.73%** of sequences remaining after deduplication in **SAMEA2569438.chr10_1.fastq.gz** versus **79.1%** in **SAMEA2569438.chr10_2.fastq.gz.**


<img width="1531" height="780" alt="image" src="https://github.com/user-attachments/assets/8652d39e-bf2c-45a5-843e-9019673a985c" />








### 1.3. Recognizing the Typical FASTQ Format

Yes, these files are clearly identified as having a typical FASTQ format based on:

•	The two reports explicitly state the encoding is **Sanger / Illumina 1.9.** This is the modern standard where quality scores are represented by ASCII characters.

•	The files consist of **Conventional base calls.** In a FASTQ file, this corresponds to the second line of every four-line block.

•	The "Per base sequence quality" graphs confirm that for every base sequenced (A, T, C, G), there is a corresponding quality score. This relationship is the defining characteristic of the FASTQ format

•	The **%GC content** is reported as 40% for both files. This consistency, along with the identical Total Sequences count of 172,253, is exactly what is expected from paired-end FASTQ files where each "forward" read has a "reverse" partner.

### 1.4 Error rate of read starts and ends

No, the read starts and ends are not similar in terms of error rate, because:

•	Read Starts: The error rate is very low at the beginning, with quality scores consistently in the "very good" green zone (Phred > 34).

•	Read Ends: The error rate increases significantly toward the end of the read. This is shown by the blue mean quality line dropping and the yellow boxes dipping lower, especially in **SAMEA2569438.chr10_2.fastq.gz,** where the whiskers reach down into the lower-quality zone (orange).

## Exercise 2

### Comparison of Alignment File Sizes

<img width="945" height="212" alt="image" src="https://github.com/user-attachments/assets/c134533b-dcbd-4f83-925d-804d6f9e6fa9" />




<img width="622" height="98" alt="image" src="https://github.com/user-attachments/assets/663898a9-ac1f-4cfe-8d58-8658ac32eaea" />


•	**SAM** is the largest file because it is a **plain text format.** It stores every read and its metadata in a human-readable way, which takes up a significant amount of disk space.

•	**BAM** file is roughly 3 times smaller than the SAM file. It uses **binary compression,** which makes it much more efficient for computers to process while containing all the same information as the SAM file.

•	**CRAM** is the most efficient format, being approximately 7 times smaller than the SAM file and nearly 2.5 times smaller than the BAM file. It uses **reference-based compression,** meaning it only stores the differences between our reads and the reference.fna file, rather than storing the entire sequence for every read.



## Exercise 3

### Depth and Coverage Analysis

### 3.1 Using samtools mpileup to estimate the percentage of chr10 with depth > 100

•	**samtools mpileup** was used with the reference genome (reference.fna) to calculate the sequencing depth for every genomic position on **Chromosome 10.**

•	The output was processed using an **awk** script to isolate positions where the **4th column** (representing depth) was greater than 100.

•	The script counted these high-depth positions and divided them by the total number of records (NR) to find the fraction of the covered chromosome meeting the criteria.


<img width="945" height="91" alt="image" src="https://github.com/user-attachments/assets/c7166fe5-ceff-429f-a912-d4c8caef640d" />

•	The estimated percentage of Chromosome 10 with a sequencing depth greater than 100 is **1.29791%.**


## Exercice  4: 

The exercice 4 is devided to 5 steps and used the Integrative Genomics Viewer (IGV) to visualize a large genomic datasets, detect variants and mutations. 

* 4.1. Open the URL https://igv.org/app in a browser
  <img width="1900" height="958" alt="image" src="https://github.com/user-attachments/assets/d1c5901d-2a38-405e-9b77-7cc545ec4d9f" />


* 4.2. Load the reference FASTA and index files with the Genome->Local File option.
  <img width="1892" height="294" alt="Capture d&#39;écran 2026-01-25 173707" src="https://github.com/user-attachments/assets/d15d3e5f-441b-467c-9674-27b7689adf15" />

* 4.3. Load the gene annotation GTF file with the Tracks->Local File option
  <img width="1914" height="363" alt="Capture d&#39;écran 2026-01-25 173753" src="https://github.com/user-attachments/assets/ab9b658d-0e38-458d-88c7-ad57719f1c1b" />

* 4.4. Load the BAM and index files with sorted, mapped reads with the Tracks->Local File option.
  <img width="1833" height="932" alt="Capture d&#39;écran 2026-01-25 173810" src="https://github.com/user-attachments/assets/69a0c518-516d-489e-83ae-6eb00345d906" />
 
* 4.5. Go to the chr location 10:9,768,000-9,784,000 and zoom in to display the reads
  <img width="1919" height="971" alt="Capture d&#39;écran 2026-01-25 173204" src="https://github.com/user-attachments/assets/0802923e-660b-4939-9e0c-6d228b7d1ec8" />

  <img width="1919" height="969" alt="Capture d&#39;écran 2026-01-25 173323" src="https://github.com/user-attachments/assets/858c0a9a-28fd-40e7-9bf1-70586d4f4738" />



## Exercice 5 : 

In this stage of the analysis, we focused on identifying genetic variation within our sample by processing raw sequencing data into a set of high-confidence variants.
### Variant Calling with FreeBayes
We utilized FreeBayes to identify Single Nucleotide Polymorphisms (SNPs) and small Insertions/Deletions (Indels).
•	The ploidy was set to 2, reflecting the diploid nature of the plant genome under study.
•	The initial "raw" output in the VCF (Variant Call Format) file contained 31,521 records.
### Quality Control and Filtering
To ensure the reliability of the findings and remove potential sequencing artifacts, we performed site-specific filtering using bcftools:
•	Filter Criterion: We removed records where the quality score was low 
•	Results: This step successfully filtered the dataset down to 30,490 high-confidence variants.
•	Variant Composition:
o	SNPs: 25,441
o	Indels: 2,395
### Visual Validation in IGV
The final filtered VCF was loaded into the Integrative Genomics Viewer (IGV) alongside the reference genome and aligned BAM files to validate specific variants. This allowed for the inspection of:
•	Genotype (GT): Confirming if specific positions are heterozygous or homozygous.

•	Read Depth (DP): Assessing the number of reads supporting a variant to ensure it isn't an artifact of low coverage.

•	Gene Models: Determining if variants fall within functional regions (exons/introns) of the plant genome.

<img width="1901" height="966" alt="image" src="https://github.com/user-attachments/assets/acad9a55-1d81-44cb-ad7d-8be6a4b268e4" />
