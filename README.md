**Table of Contents**
=
    I. Introduction
    II. Prerequisites
    III. Tutorial
    IV. Getting help

**I. Introduction**
==
    This is a bash-based pipeline for analysis of PacBio Iso-Seq generated with RSII or Sequel. Collectively, the pipeline will do:
    
    A. For RSII, extract raw reads, subreads and CCS reads from raw data in h5; For Sequel, only extract the CCS reads.
    B. Do some metadata analysis and give statistic reports, like adapter, raw reads length, raw reads quality, etc.
    C. Get full-length reads according to primer sequences and polyA. Completed sequences, which may represent the whole transcripts, are generated by cutting primer and polyA.
    D. Do some pre-mapping evaluation, like GC content of read, GC content across read, base quality across read.
    E. Mapping full-length reads onto genome.
    F. Filter and refine the raw alignments generated by mapping, like removing circular and translocated alignments, remove alignments with low coverage and identity, etc.
    G. Do some post-mapping evaluation, like read-length distribution, distance to TSS/TTS, etc.
    H. PA analysis, like distribution of cleavage site distance, motif around PA site, etc.
    I. Identify Alternative RNA Processing Events (APE, including SE, IR, A5SS, A3SS, APA) with CCS reads and junctions from RNA-seq (if available).
    J. APE characterization, like motif of splicing site, classifying APE into annotated or novel events, etc.
    K. Combination pattern of APE.

**II. Prerequisites**
==
|Tool Name|Version|Description|
|:---|:---|:---|
SMRT Analysis|v2.3.0|The analysis toolkit for RSII Iso-seq data.
SMRT Link|v5.1.0|The analysis toolkit for Sequel Iso-seq data.
R|3.3.2|The R language program.
Perl|5.24|The Perl language program.
Python|2.7|The Python language program.
cDNA-primer|1.1.6|The wrapper of HMMer for get full-length reads.
HMMer|3.1b2|The tool to identify primer and polyA sequences.
seqtk|1.2-r94|The toolkit to manipulate fastq/fasta files.
GMAP|2017-11-15|The mapper for aligning CCS reads onto reference genome
SAMTools|1.5|The toolkit to manipulate BAM files.
BEDTools|2-2.26.0|The utilities to manipulate BED files.
   
   The given version is just to suggest you to use this version, but not to prohibit you from using newer version, although we haven’t tested the newer ones and some unknown error may occur.
   
   Needed R packages: ggplot2, EMT, etc.
   
   The needed in-house scripts (fqNameMapping.pl, fqSeFilter.pl, etc.) are packaged in the source release.

**III. Tutorial**
==
**3.1 Setup Environment**
===
Assume that the pipeline is put at ~/bin/isoSeq.

This pipeline is composed of perl, python, bash and R scripts as well as pre-compiled C binaries, so no need to build/compile them. The only thing needed to do is making them executable:
``` bash
cd ~/bin/isoSeq
chmod u+x *.{pl,py,sh,R} skyjoin faFilter faCount
```
Add the path of the pipeline as the prefix of the environment variable $PATH, so that all scripts can be runed as executable command directly.
``` bash
PATH=~/bin/isoSeq:$PATH
```
After doing this, the correct $PATH may look like:
````
echo $PATH
~/bin/isoSeq:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
````
Test whether the pipeline is accessible:
```` bash
isoSeq.sh
````
If pipeline hele message is shown, the environment is ready, otherwise if the below error is shown:
```` bash
bash: isoSeq.sh: command not found...
````
check whether the pipeline scripts are executable and their path is in the $PATH variable.

**3.2 Prepare Configuration File**
===
The format and description of the configuration file is explained in the help message of isoSeq.sh. It (e.g. sample.conf) is a text file with many lines, each of which is TSV (tab-separated value) or space-separated values. One line is the complete information for one sample. Columns in one line is:

An example of configuration file:

#SampleName  InputDirectory  Primer     Junction(Optional)

sample1       sample1_data   primer1.fa  junctions.bed

sample2       sample2_data   primer2.fa



The line prefixed with a ‘#’ isn’t necessary and will be skipped automatically by the pipeline.

**3.3 Prepare Resource Data**
===
Some resource (data) can be specified in env.conf file in the source. Needed resource files include:

"gmapDir" can be downloaded from GMAP website or generated by gmap_build command in GMAP.
    
genomeHg19 is a fasta file with sequences of all chromosomes. It can be downloaded from UCSC (http://hgdownload.soe.ucsc.edu/goldenPath/hg19/bigZips/chromFa.tar.gz).

chrSizeHg19 is a TSV file with chr name and its length. It can be downloaded from UCSC (http://hgdownload.soe.ucsc.edu/goldenPath/hg19/bigZips/hg19.chrom.sizes).

twoBitHg19 a compressed binary file storing genome sequence. It can be downloaded from UCSC (http://hgdownload.soe.ucsc.edu/goldenPath/hg19/bigZips/hg19.2bit).

hg19RefGeneGpe is GPE (Gene Prediction Extension) file describing gene model. It can be downloaded from UCSC (http://hgdownload.soe.ucsc.edu/goldenPath/hg19/database/refGene.txt.gz).

gapHg19 is a BED file describing genome gap. It can be downloaded from UCSC (http://hgdownload.soe.ucsc.edu/goldenPath/hg19/database/gap.txt.gz).

These variables can be overwrote by command option of isoSeq.sh, for example:
```` bash
isoSeq.sh --genomeSeq /path/to/your/genome/sequence
````
will overwrite the “$resource/fna/hg19/all.fa”.

**3.4 Start to Run the Pipeline**
===
A complete command to run the pipeline may be:
```` bash
isoSeq.sh --gmapDir gmapDir/hg19 \
    --gmapDB hg19 \
    --genomeSeq hg19.fasta \
    --gpe hg19.refGene.gpe \
    --gap hg19.gap.bed \
    --twoBit hg19.2bit \
    --chrSize hg19.sizes \
    --outDir . \
    --thread 10 \
    sample.conf \
    >isoSeq.log 2>isoSeq.err
````
GMAP DB can be prepared by:
```` bash
gmap_build --db=hg19 --circular=chrM hg19.fasta
````
If the variables in env.conf are specified correctly, you can simply run the analysis as:
```` bash
isoSeq.sh --thread 10 sample.conf >isoSeq.log 2>isoSeq.err
````
**IV. Getting help**
==
You can send the author [zhangsjsky@foxmail.com](zhangsjsky@foxmail.com) any information about this analysis pipeline, like bug reporting, performance improvement suggestion.
