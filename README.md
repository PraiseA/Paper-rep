### Paper-rep

## Download the SRA fastq files
Get the accession number of the specie
Search on: https://www.ncbi.nlm.nih.gov/sra/ with the accession number
On the terminal, fastq-dump with the run number on the page
 fastq-dump --outdir **fastq** --gzip --skip-technical  --readids --read-filter pass --dumpbase --split-3 --clip **run number**
 a folder is created with the code, N.B change the outdir to avoid replacing of data
 
 
 ## Assemble the fastq files
 
 Using velveth
  velveth **name of file** 41 -shortPaired -fastq.gz -separate ERR387730_pass_1.fastq.gz ERR387730_pass_2.fastq.gz
  velvetg **name of file from above** -exp_cov auto -ins_length 350
  
 This is done on each SRA file downloaded
 
 ## Align the fastq files using bowtie2
 
 
