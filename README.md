# DNA-seq pipeline
This repository provides comprehensive documentation on the DNA-Seq analysis pipeline adapted from Genomic Data Commons (GDC) for the identification and annotation of somatic variants in whole exome sequencing (WXS) and whole genome sequencing (WGS) data. 

### Test FASTQs
For WES, we considered a subsample of FASTQ files from HCC1395. HCC1395 is an epithelial cell isolated from the mammary gland of a 43-year-old, White, female patient with ductal carcinoma TNM Stage I, grade 3.
The HCC1395 sample name and sample IDs are as follows: WES_IL_T_1 (SRR7890883).

To subsample the original FASTQ files (code credit goes to [one-liner](https://userweb.eng.gla.ac.uk/umer.ijaz/bioinformatics/subsampling_reads.pdf)) 

```shel
# Paste forward and reverse reads side by side, and use awk to format the output
paste SRR7890883_WES_cross-site_study_1.fastq.gz SRR7890883_WES_cross-site_study_2.fastq.gz | \
awk '{ printf("%s",$0); n++;
if(n%4==0) { printf("\n");} else { printf("\t");} }' |
# Use awk to subsample the pairs (adjust k to 50000 for 50,000 reads)
awk -v k=10000 'BEGIN{srand(systime() + PROCINFO["pid"]);}{s=x++<k?x1:int(rand()*x);if(s<k)R[s]=$0}END{for(i in R)print R[i]}' |
# Use awk to split the subsampled pairs into forward and reverse FASTQ files
awk -F"\t" '{print $1"\n"$3"\n"$5"\n"$7 > "WES-IL-T-1-subsampled_1.fastq.gz"; \
            print $2"\n"$4"\n"$6"\n"$8 > "WES-IL-T-1-subsampled_2.fastq.gz"}'
```   
For the paired normal, HCC1395 BL (B lymphoblast cell from peripheral blood) should be considered. The sample IDs and sample names are WES_IL_N_1 (SRR7890874).


The tiny FASTQ files are 10K read subsamples of `SRR7890883_WES_cross-site_study` from benchmark datasets. 

```bash
# Paste forward and reverse reads side by side, and use awk to format the output
paste SRR7890883_WES_cross-site_study_1.fastq.gz SRR7890883_WES_cross-site_study_2.fastq.gz | \
awk '{ printf("%s",$0); n++;
if(n%4==0) { printf("\n");} else { printf("\t");} }' |
# Use awk to subsample the pairs (adjust k to 50000 for 50,000 reads)
awk -v k=10000 'BEGIN{srand(systime() + PROCINFO["pid"]);}{s=x++<k?x1:int(rand()*x);if(s<k)R[s]=$0}END{for(i in R)print R[i]}' |
# Use awk to split the subsampled pairs into forward and reverse FASTQ files
awk -F"\t" '{print $1"\n"$3"\n"$5"\n"$7 > "SRR7890883_subsampled_1.fastq.gz"; \
            print $2"\n"$4"\n"$6"\n"$8 > "SRR7890883_subsampled_2.fastq.gz"}'

```

The workflow encompasses six main procedures: 

Genome Alignment

Alignment Co-Cleaning

Somatic Variant Calling

Variant Annotation

Mutation Aggregation

 Aggregated Mutation Masking
 
