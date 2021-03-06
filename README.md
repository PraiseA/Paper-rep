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
 
 ## Align the fastq files using bowtie2 (reference: https://datacarpentry.org/wrangling-genomics/04-variant_calling/index.html)
 
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
# Convert vcf file to fasta file
# vcfphylip is cloned to the terminal
```
git clone https://github.com/edgardomortiz/vcf2phylip.git
```
```
 python vcf2phylip.py -i nameoffile.vcf -f
 ```
# fasta files to phylogenetic tree
Website used: https://ngphylogeny.fr/tools/
My work: https://ngphylogeny.fr/workspace/histories
Phylogenetic tree of the samples: https://ngphylogeny.fr/workspace/history/146848155c83b4f6

## Resistance Analysis
# Installing ARIBA
```
pip3 install ariba
```
# Download the data of the specie in question
```
ariba pubmlstget 'Salmonella enterica' out
```
# Running local assemblies and call variants
```
ariba run out/ref_db reads_1.fq reads_2.fq output_directory
```
# Summarizing data from several runs
```
ariba summary out.summary out.run1/report.tsv out.run2/report.tsv out.run3/report.tsv .....
```
Will create out.summary.csv, out.summary.phandango.csv out.summary.phandango.tre
