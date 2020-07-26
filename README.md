# go to the rnaseq data
[kkeith]$ cd rnaseq_practice/data/
# make a folder to put the output in
[kkeith]$ mkdir fastqc
# run FastQC with a loop
[kkeith]$ fastqc *.fastq.gz -o fastqc/

