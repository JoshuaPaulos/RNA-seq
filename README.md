*Quality Control:*

**Check Quality with FastQC**
# go to the make directory for lung and breast data
[jpaulos@cbix2 ~]$ mkdir lung
[jpaulos@cbix2 ~]$ mkdir breast 

# make a folder to put the output in
[jpaulos@cbix2 breast]$ mkdir fastqc

# make a folder to put the output in
[jpaulos@cbix2 lung]$ mkdir data
[jpaulos@cbix2 data]$ mkdir fastqc

# run FastQC with a loop for breast data
[jpaulos@cbix2 breast]$ for i in *.fastq.gz; do fastqc $i -o /home/jpaulos/breast/fastqc/;done

# run FastQC with a loop for lung data
[jpaulos@cbix2 lung]$ for i in *.fastq.gz; do fastqc $i -o /home/jpaulos/lung/data/fastqc/;done




**Trim Adapters and Low Quality Sequences**
# still in the data directory
[jpaulos]$ pwd
/home/jpaulos/breast
# make a folder in the analysis folder to put the trimmed reads in
[jpaulos]$ mkdir analysis
[jpaulos]$ mkdir 01_trim


# trim with trim_galore for breast data 
[jpaulos]$ for i in *R1.fastq.gz; do trim_galore --paired --fastqc --illumina --output /home/jpaulos/breast/analysis/01_trim --retain_unpaired -q 30 $i ${i/R1/R2}; done

# still in the data directory
[jpaulos]$ pwd
/home/jpaulos/lung

# make a folder in the analysis folder to put the trimmed reads in
[jpaulos]$ mkdir analysis
[jpaulos]$ mkdir 01_trim

# trim with trim_galore for lung data 
[jpaulos]$ for i in *R1.fastq.gz; do trim_galore --paired --fastqc --illumina --output /home/jpaulos/lung/analysis/01_trim --retain_unpaired -q 30 $i ${i/R1/R2}; done



*Process Reads:*

**Align**
# go to the directory where the trimmed reads for breast data are 
[jpaulos]$ pwd
/home/jpaulos/breast/analysis

# make a new directory to put the aligned files in
[jpaulos@cbix2 analysis]$ mkdir 02_align

# align with STAR
[jpaulos@cbix2 01_trim]$ for i in *val_1.fq.gz; do STAR --genomeDir /mnt/data/gdata/human/hg38/star_index_hg38/ --readFilesIn $i ${i/R1_val_1/R2_val_2} --readFilesCommand zcat --outFileNamePrefix /home/jpaulos/breast/analysis/02_align/${i/R1*/} --outSAMtype BAM SortedByCoordinate; done

# go to the directory where the trimmed reads for lung data are
[jpaulos]$ pwd
/home/jpaulos/lung/analysis

# make a new directory to put the aligned files in
[jpaulos@cbix2 analysis]$ mkdir 02_align

# align with STAR
[jpaulos@cbix2 01_trim]$ for i in *val_1.fq.gz; do STAR --genomeDir /mnt/data/gdata/human/hg38/star_index_hg38/ --readFilesIn $i ${i/R1_val_1/R2_val_2} --readFilesCommand zcat --outFileNamePrefix /home/jpaulos/lung/analysis/02_align/${i/R1*/} --outSAMtype BAM SortedByCoordinate; done

**Count Features**
# go to the directory where the aligned reads breast data are
[jpaulos]$ pwd
/home/jpaulos/breast/analysis/02_align

# make a directory to put the count tables for the breast data in
[jpaulos@cbix2 analysis]$ mkdir 03_count

# count the number of reads for each gene
[jpaulos]$ for i in *.bam; do featureCounts -a /mnt/data/gdata/human/hg38//homo_sapiens_hg38_chr21.gtf -o /home/jpaulos/breast/analysis/03_count/${i/Aligned.sortedByCoord.out.bam/counts.txt} -R BAM $i; done


# go to the directory where the aligned reads for the lung data are
[jpaulos]$ pwd
/home/jpaulos/lung/analysis/02_align

# make a directory to put the count tables lung data in
[jpaulos@cbix2 analysis]$ mkdir 03_count

# count the number of reads for each gene
[jpaulos]$ for i in *.bam; do featureCounts -a /mnt/data/gdata/human/hg38/chr21/homo_sapiens_hg38_chr21.gtf -o /home/jpaulos/lung/analysis/03_count/${i/Aligned.sortedByCoord.out.bam/counts.txt} -R BAM $i; done


