# DNA-seq pipeline
This repository provides comprehensive documentation on the DNA-Seq analysis pipeline adapted from Genomic Data Commons (GDC) for the identification and annotation of somatic variants in whole exome sequencing (WXS) and whole genome sequencing (WGS) data. 


The tiny FASTQ files are 10K read subsamples of `SRR7890883_WES_cross-site_study` from benchmark datasets. These FASTQs are prepared using the following one-liner  (see more [one-liner](https://userweb.eng.gla.ac.uk/umer.ijaz/bioinformatics/subsampling_reads.pdf)) 

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
 
