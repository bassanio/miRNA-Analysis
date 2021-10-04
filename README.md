# miRNA-Analysis

The analysis followed here is based on the [encode microRNA-seq Data Standards and Processing Pipeline](https://www.encodeproject.org/microrna/microrna-seq/)

**Input files for Analysis**

```
wget -c ftp://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_24/gencode.v24.annotation.gtf.gz
wget -c https://www.encodeproject.org/files/GRCh38_no_alt_analysis_set_GCA_000001405.15/@@download/GRCh38_no_alt_analysis_set_GCA_000001405.15.fasta.gz
wget -c "https://www.encodeproject.org/files/GRCh38_EBV.chrom.sizes/@@download/GRCh38_EBV.chrom.sizes.tsv
wget -c https://www.encodeproject.org/files/ENCFF628BVT/@@download/ENCFF628BVT.gtf.gz
```

**Step 1: Creating Star Index**
```
genomedir="miRNA/STAR"
annotation="miRNA/gencode.v24.annotation.gtf"
refgenome="miRNA/GRCh38_no_alt_analysis_set_GCA_000001405.15.fasta"
STAR --runThreadN 20 --runMode genomeGenerate --genomeDir $genomedir --sjdbGTFfile $annotation --sjdbOverhang 1 --genomeFastaFile $refgenome

```

**Step 2: QC and adapter trimming**

Library: The NEBNext Multiplex Small RNA Prep Kit for Illumina [NEB #E7560S](https://international.neb.com/-/media/nebus/files/manuals/manuale7300_e7330_e7560_e7580.pdf?rev=c1c5ab8864234a62b78401f83acb2205&hash=6C1E8073384F307864018EA87D3638EF)

```
cutadapt -a=AGATCGGAAGAG --minimum-length=8 --untrimmed-output=$NO_3AD_FILE -m 15 --overlap=5 --cores=8 $DIR/$IN -o=$NO_5AD_FILE\n";
cutadapt -g=TGGCAACGATC --minimum-length=8  -m 15 --overlap=5 $NO_5AD_FILE --cores=8 -o $SL_TRIM_FILE\n";
fastq_quality_filter -q 30 -p 80 -v -i $SL_TRIM_FILE -o $OUT3\n";
fastqc --extract $OUT3\.gz\n";
```
**Step 3: Quantification of miRNA***

```
$miRNA_annotation="miRNA/ENCFF628BVT.gtf"

$params= "--runThreadN $runThreadN --alignEndsType EndToEnd --outFilterMismatchNmax 1 --outFilterMultimapScoreRange 0 \
--quantMode TranscriptomeSAM GeneCounts --outReadsUnmapped Fastx --outSAMtype BAM SortedByCoordinate --outFilterMultimapNmax 10 \
--outSAMunmapped Within --outFilterScoreMinOverLread 0 --outFilterMatchNminOverLread 0 --outFilterMatchNmin 16 \
--alignSJDBoverhangMin 1000 --alignIntronMax 1 --outWigType wiggle --outWigStrand Stranded --outWigNorm RPM ";

STAR --genomeDir $genomedir --readFilesIn $OUT3\.gz --readFilesCommand zcat --outFileNamePrefix BAM/$Sample --sjdbGTFfile $miRNA_annotation $params
```
