### Bioinformatics Assignment: Sequence Alignment and Functional Annotation
### 1. Formatting a Sequence Collection for BLAST:
## Exe1)
To prepare the database, the following sequence of commands were used:
### Step 1: The original file was in a compressed Gzip format. We used the gunzip command to decompress it in a standard FASTA File 
gunzip uniprot_Atha.fasta.gz
### Step 2: We then used the makeblastdb to convert the FASTA protein sequences into a binary format that BLAST can search efficiently
 <img width="919" height="285" alt="image" src="https://github.com/user-attachments/assets/0b9ac45a-3881-4682-a01b-0b9fb449512c" />

## Results: 
• Total sequences formatted:  15,719 sequences.
• Effect on E-value:  The size of the database (n) is directly proportional to the Expectation Value (E-value) of a BLAST search.
•	The E-value is calculated as E = m x n x 2-S, where m is the query length and S is the bit score.
•	As the number of sequences in our database increases (to 15,719), the probability of finding a random match also increases. Therefore, for any given alignment, the E-value will increase as the database grows, making it harder for a result to be considered statistically "significant" unless the match is very strong

## 2. Querying the collection with a sample coding sequence
## Exe2)
To facilitate comparison and long-term storage of the data, the standard output of the BLAST commands was redirected into separate files using the shell redirection operator (>).
 <img width="945" height="162" alt="image" src="https://github.com/user-attachments/assets/9066aa61-cdaa-4a7e-b368-a4994b85d17e" />

## Exe3)
The default alignment format for the NCBI BLAST+ suite is Pairwise (Format 0). This format is designed for human readability and provides a residue-by-residue comparison between the query sequence and each subject (hit) found in the database.
### Example of Pairwise Alignment:
The following snippet represents the typical pairwise output for a match between the ARF6 query and a subject in the database
 <img width="756" height="706" alt="image" src="https://github.com/user-attachments/assets/fd564bbc-4365-4d47-9f83-fc235c1e5234" />

## Exe4) 
To see if there are differences in the results retrieved in both searches, we utilized a combination of commands: 
### a. To Extract and Compare Unique Hits
 <img width="945" height="262" alt="image" src="https://github.com/user-attachments/assets/2ccb05a3-f744-4238-83fc-4851e85f2a79" />

### b. To View Side-by-Side Differences
 <img width="945" height="786" alt="image" src="https://github.com/user-attachments/assets/396438b9-adf1-4a1e-bfb5-fa8cbef00e1f" />

### c. To Extract and Compare Top Hits
 <img width="945" height="233" alt="image" src="https://github.com/user-attachments/assets/327629ce-2230-45b2-b320-17e08564afa9" />

### Main results 
•	Several proteins were identified in the blastp results that did not appear in the blastx output, including IAA19_ARATH, REM9_ARATH, IAA5_ARATH, and IAA29_ARATH.
•	Even for identical subject hits like ARFF_ARATH, the bit scores and E-values varied. For instance, the alignment length and bit score for the top hit were higher in the protein-to-protein search compared to the translated search.
•	The blastx search operated within an Effective search space of 4,562,846,490. This is notably larger than the standard protein search space because blastx must translate the nucleotide query into all six possible reading frames.
•	While blastp is more efficient for finding direct matches to known proteins, blastx is a more robust tool for analyzing uncharacterized transcripts. However, the significantly larger search space in blastx leads to higher E-values, meaning a match must be "stronger" to be considered statistically significant compared to a standard blastp search.
## 3. Producing a sequence profile
### Exe 5) 
The following command was executed to generate the iterative profile: psiblast -db uniprot_Atha.fasta -query test.faa -num_iterations 3 -out_ascii_pssm profile.out
 <img width="611" height="554" alt="image" src="https://github.com/user-attachments/assets/fd615b0b-fc5d-474d-a219-f05b9494dff6" />

•	Analysis of profile.out The PSI-BLAST execution produced a profile.out file containing a Position-Specific Scoring Matrix (PSSM).
•	From the sequences identified in the first round, the program constructs the Position-Specific Scoring Matrix (PSSM). This matrix is then used in subsequent iterations (2 and 3) to find more distant relatives.
•	Upon inspecting the profile.out file using the command: head -n 20 profile.out, a detailed table is visible where each row represents a specific position in the query sequence and each column corresponds to one of the 20 standard amino acids.
•	The numbers within the matrix are log-odds scores. Positive numbers indicate that a specific amino acid is highly conserved at that position within the protein family, while negative numbers suggest it is rarely observed.
•	Unlike the "one-size-fits-all" approach of BLOSUM62, the PSSM captures the unique evolutionary constraints of the ARF6 family. This allows the search to detect homologs that share critical functional motifs even if their overall sequence identity is low.
## 4. Making a Hidden Markov Model (HMM) with aligned sequences
##Exe6)
### Step 1: Selection and Extraction of High-Scoring Matches
•	The test.faa.blast file was filtered based on the 12th column (bit score) to isolate the Subject IDs (2nd column) of the top hits
 <img width="945" height="58" alt="image" src="https://github.com/user-attachments/assets/9e08dba9-0948-490c-a51e-55d70d40dc18" />

•	Using the list of 22 IDs, the full amino acid sequences were extracted from the uniprot_Atha.fasta database to create the required FASTA file using the command: seqtk subseq uniprot_Atha.fasta high_score_ids.txt > high_score_sequences.fasta
### Step 2: Multiple Sequence Alignment (MSA) with Clustal Omega
•	With the FASTA file created in Step 1, the Clustal Omega utility was used to compute the alignment.
 <img width="737" height="156" alt="image" src="https://github.com/user-attachments/assets/225de87e-bd73-4fee-9dba-c40a3f36b375" />
<img width="780" height="408" alt="image" src="https://github.com/user-attachments/assets/ab78f42c-dac3-44f0-b9c4-3522b4bdc03c" />

 
### Step 3: Building the HMM out of Aligned Sequences
•	The hmmbuild tool from the HMMER suite was used to process the alignment file.
 <img width="945" height="311" alt="image" src="https://github.com/user-attachments/assets/e9b63b23-742c-433e-879d-afc0a40a8d5f" />

•	The model has a length (mlen) of 670 consensus positions derived from an alignment length (alen) of 1361 columns.
•	Calculated at 1.43, representing the non-redundant information content extracted from the 22 sequences.
•	The model shows 0.590 bits per position, which is a measure of how much information the HMM provides over a random background model.
### Step 4: Scan the HMM against the collection
•	The HMM profile was first processed using the hmmpress utility to create the necessary binary auxiliary files.
•	The profile was then queried against the sequence collection using the hmmscan tool, and the results were redirected to a text file.
 <img width="746" height="573" alt="image" src="https://github.com/user-attachments/assets/ffd77dc1-f9de-47c0-9405-dfc5ea861344" />
<img width="748" height="433" alt="image" src="https://github.com/user-attachments/assets/9995cf0c-d84a-4682-83cb-575ea919870d" />

 
•	The HMM used for the search consisted of 670 nodes (consensus positions), providing a comprehensive structural signature of the ARF6 family.
•	The search identified significant hits among the Arabidopsis proteins, which were ranked by their E-values and Bit Scores. Hits that satisfy the reporting thresholds represent high-confidence functional homologs.
•	Unlike the initial BLAST search, this profile-based approach leverages the evolutionary information of 22 aligned sequences, allowing it to detect distant orthologs that maintain the core structural "signature" of the ARF family even if their overall sequence identity is relatively low.
## 5. Annotating function by predicted structural similarity
## Exe7)
Resource	Entry/ID	Start Residue	End Residue	Probability	Description
Pfam	PF02362.26	127	234	97.83%	B3 DNA binding domain
Pfam	PF06507.19	278	362	99.57%	Auxin response factor ancillary domain
Pfam	PF02309.22	795	885	99.00%	AUX/IAA family dimerization domain
PDB	4LDU_A	16	363	100%	ARF5 DNA binding protein structure
PDB	4CHK_H	781	907	99.57%	ARF5 PB1 (dimerization) domain
AlphaFoldDB	Q9ZTX8	1	935	100%	Full-length structural prediction of ARF6

## 6. Annotating function on orthology grounds
## Exe8)
 <img width="702" height="477" alt="image" src="https://github.com/user-attachments/assets/3e60a08e-ce0b-45f7-accb-7a2ce33f3222" />

### eggNOG Orthology Groups
The protein AT1G30330.2 is mapped to high-confidence orthologous groups across several taxonomic levels:
•	Primary Orthogroup (OG): 28JYJ@1|root.
•	Taxonomic Levels: It is further categorized under 2QSCZ@2759|Eukaryota, 37P4Z@33090|Viridiplantae, and specifically 3HYYA@3699|Brassicales.
•	Identified as an Auxin Response Factor (ARF), a transcription factor that specifically binds to the DNA sequence 5'-TGTCTC-3' in auxin-responsive promoter elements (AuxREs).
## 7. Annotating function with Gene Ontology (GO) terms
### Exe9)




### The functional categories of the following GO IDs GO:0009414, GO:0035618, GO:0016491
<img width="878" height="268" alt="image" src="https://github.com/user-attachments/assets/d8d9ce3b-3e38-452d-8de3-154b8b01fb5d" />
 
GO:0016491 : oxidoreductase activity
GO:0035618: root hair
GO:0009414 : response to water deprivation
### The GO ID and the functional category corresponding to photosynthesis
 <img width="945" height="612" alt="image" src="https://github.com/user-attachments/assets/dae19662-d7ef-416f-a9ee-b69f0d43c98e" />


### The immediate parent(s) and children of the photosynthesis GO term
 <img width="527" height="623" alt="image" src="https://github.com/user-attachments/assets/71f577cc-7db8-4d03-b65e-c61fe13f73be" />

 <img width="945" height="531" alt="image" src="https://github.com/user-attachments/assets/b3fd28d5-dc18-46ce-b7df-ae965321700d" />

### GO annotation terms of the following protein A0A068LKP4,A0A097PR28, A0A059Q6N8
 <img width="945" height="284" alt="image" src="https://github.com/user-attachments/assets/4c1780de-a045-43d1-952f-8f4d6e3eab4d" />

### Gene products involved in leaf development
 <img width="945" height="365" alt="image" src="https://github.com/user-attachments/assets/a7385550-1507-450b-884d-971d1531cc5d" />

### The number of proteins assigned to the leaf development GO term in Arabidopsis thaliana, Prunus perisca and Zea mays
 <img width="855" height="400" alt="image" src="https://github.com/user-attachments/assets/5dcab607-70c6-466e-87a6-6c286da4f4d7" />

Task	Search / Goal	Result / GO ID	Category (Aspect)	Findings / Counts
1
	Search by ID	GO:0016491	Molecular Function	Oxidoreductase activity; 28,421,516 annotations
	Search by ID	GO:0035618	Cellular Component	Root hair; 98 annotations
	Search by ID	GO:0009414	Biological Process	Response to water deprivation; 27,971 annotations
2	Photosynthesis	GO:0015979	Biological Process	1,361,779 total annotations
3
	Parent Term	GO:0008152	Biological Process	Metabolic process (immediate parent of photosynthesis)
	Child Term	GO:0009765	Biological Process	Photosynthesis, light reaction (immediate child)
4
	A0A068LKP4	Arabidopsis	Protein	RPW8 domain-containing; 0 annotations in search
	A0A097PR28	Peach	Protein	Gen. transcription factor IIH sub. 2; 8 annotations
	A0A059Q6N8	Maize	Protein	Photosystem II reaction center M; 6 annotations
5	Leaf Dev.	GO:0048366	Biological Process	89,655 gene products involved across all species
6
	Taxonomy	Arabidopsis	TaxID: 3702	~1,452 proteins assigned to leaf development
	Taxonomy	Peach	TaxID: 3760	~123 proteins assigned to leaf development
	Taxonomy	Maize	TaxID: 4577	~284 proteins assigned to leaf development
### Experimental Statistics
 <img width="792" height="424" alt="image" src="https://github.com/user-attachments/assets/0e29aca6-f9d4-4f63-8d5c-ba0f7eaa54fb" />

### Arabidopsis thaliana 
•	Distinct Gene Products: Over 14,000 proteins 
•	Total BP Annotations: Over 65,000 annotations 
### Prunus persica 
•	Distinct Gene Products: Only 332 distinct gene products currently have experimental backing in this dataset.
•	Total BP Annotations: A total of 423 annotations supported by experimental evidence.
## 8. Predicting 3D structure
### Exe10)
 <img width="945" height="495" alt="image" src="https://github.com/user-attachments/assets/f0208ba0-c689-4173-a686-5027751c7c5d" />

