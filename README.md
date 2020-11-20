# Shafik_et_al_codes


## Trimming adaptors

```markdown
$ module load java/1.8.0
$ java -jar Trimmomatic-0.38/trimmomatic-0.38.jar PE -phred33 xxx_1.fq.gz  xxx_2.fq.gz xxx_1.paired.fq.gz  xxx_1.unpaired.fq.gz  xxx_2.paired.fq.gz  xxx_2.unpaired.fq.gz ILLUMINACLIP:/Trimmomatic-0.38/adapters/TruSeq3-PE.fa:2:30:10 LEADING:20 TRAILING:20 SLIDINGWINDOW:4:15 MINLEN:15
``` 
 Notes: 
* The inputs are, “xxx_1.fq.gz” and “xxx_2.fq.gz ”. 
* The outputs are, \
 “xxx_1.paired.fq.gz”: results of paired reads in “xxx_1.fq.gz”\
 “xxx_1.unpaired.fq.gz”: results of unpaired reads in “xxx_1.fq.gz”\
 “xxx_2.paired.fq.gz”: results of paired reads in “xxx_2.fq.gz”\
 “xxx_2.unpaired.fq.gz”: results of unpaired reads in “xxx_2.fq.gz”
* "xxx" refers to a general sample name in our analysis.

## Mapping

```markdown
$ module load tophat/2.1.1
$ tophat -G ~/gtf/mm9 -o xxx_tophat_out  ~/genome_index_mm9 xxx_1.paired.fq.gz  xxx_2.paired.fq.gz
```

## PCA plots
```markdown
$ /multiBamSummary bins --bamfiles /BAMS -o output.npz
$ /plotPCA -in output.npz -o output.png
```

Notes: "/BAMS" is a folder which contains all .bam files to plot


## Peak Calling
``` markdown
$ module load macs2
$ macs2 callpeak -B -t   xxx.bam  -c  xxx.bam -f BAMPE --nomodel -g mm --extsize 150 -n outputfilename
```

## Differential peak calling
```markdown
$ macs2 bdgdiff --t1 xxx_treat_pileup.bdg --c1  xxx_control_lambda.bdg --t2  xxx_treat_pileup.bdg --c2  xxx_control_lambda.bdg -l 200 --d1 [lowest number of unique reads in sample 1] --d2 [lowest number of unique reads in sample 2] --o  outputfilename
```




## Annotate peaks (in R)
```markdown
library(ChIPseeker)
library(org.Mm.eg.db)
library(TxDb.Mmusculus.UCSC.mm9.knownGene)
coordinates <- read.delim ('.txt', header = TRUE)
gr <- GRanges(coordinates$chr, IRanges(coordinates$start, coordinates$end))
txdb <- TxDb.Mmusculus.UCSC.mm9.knownGene
peak_anno = annotatePeak(gr, TxDb = txdb, annoDb="org.Mm.eg.db", overlap = ("all"))
peak_anno_data_frame <- as.data.frame(peak_anno)
```

## Calculate TPM values

```markdown
$ /TPMCalculator -g gencode.vM9.annotation.gtf -d /BAMS -q 20
```
Notes: 
"/BAMS" is a folder which contains all .bam files to calculate TPM. The outputs are in the same folder.

## NGSPLOT for peak distribution

```markdown
$ /ngsplot/bin/ngs.plot.r -G mm9 -R bed -E .bed [contains genomic coordinates of 3’UTR m6A sites] -C .bam [Indexed bam file or configuration file for multiplot]  -O output
```

## Alternative Splicing 
```markdown
$ python rmats.py --b1 .txt [file containing path to bam files] --b2 .txt [file containing path to second bam files] --gtf path to .gtf -t paired --readLength 150 --od output
```

## pheatmap (in R)
```markdown
mat <- data.matrix(file containing TPM values to plot)
pheatmap(mat, kmeans_k = NA, breaks = NA, color = color(100), cellwidth = 50, cellheight = 0.5, scale = "row", cluster_rows = T, cluster_col = F, annotation_names_col = F)
```
