### Paper-rep

## Download the SRA fastq files
Get the accession number of the specie
Search on: (https://www.ncbi.nlm.nih.gov/sra/ with the accession number)
On the terminal, fastq-dump with the run number on the page

```
 fastq-dump --outdir **fastq** --gzip --skip-technical  --readids --read-filter pass --dumpbase --split-3 --clip **run number**
 ```
  
 a folder is created with the code, N.B change the outdir to avoid replacing of data
 
 
 ## Assemble the fastq files
 
 Using velveth
 ```
  velveth **name of file** 41 -shortPaired -fastq.gz -separate ERR387730_pass_1.fastq.gz ERR387730_pass_2.fastq.gz
  velvetg **name of file from above** -exp_cov auto -ins_length 350
  ```
  
 This is done on each SRA file downloaded
 
 ## Align the fastq files using bowtie2
 
 # Download reference genome
 
 The accession number was searched on NCBI: https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/001/409/135/GCF_001409135.1_10259/
 # Index the reference genome
```
 bowtie2-build -f reference genome in .fasta **path to file created with name**
```

# Align the fastq files to the reference genome
```
 bowtie2 -x *path to indexed genome -q -1 *path to 1st paired end -2 *path to 2nd paired end -S *path to new sam file 
 ```
 ```
 bowtie2 -x /home/praise/practice/rawdata/GCFindex -q -1 /home/praise/practice/rawdata/fastq4/ERR495205_pass_1.fastq -2 /home/praise/practice/rawdata/fastq4/ERR495205_pass_2.fastq -S /home/praise/practice/rawdata/Homohuman/fastq4.sam
 ```

# Convert sam to bam file 

```
samtools view -S -b /path to/sam file > /path to bam file/name of bam file
```

# Sort bam files 

```
samtools sort -o /path/to/output/sorted.bam file /path/to/created bam file
```

# View result
```
samtools flagstat /path/to/sorted.bam file
```
# Calculate the read coverage position

```
 bcftools mpileup -O b -o /path/to/output/.bcf \
 -f /path/to/reference/genome/.fasta \
 /path/to/sorted.bam files/
 ```
 
# Detect the SNPs
```
bcftools call -m -v -o /path/to/output/.vcf /path/to/.bcf files
```
# Filter SNPs in VCF
```
vcfutils.pl varFilter /path/to/.vcf file > *_final.vcf
```

# View final vcf
```
less -S path to vcf
```
# Convert vcf files to .vcf.gz and index
```
bgzip *.vcf
bcftools index *.vcf.gz
```
# Combine all vcf files into 1
```
bcftools merge -Oz *.vcf.gz *.vcf.gz ... > Final.vcf.gz
```
## Create Phylogenetic Tree
# Convert bam file to fasta file
```
