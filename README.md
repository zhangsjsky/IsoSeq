Table of Contents

    I. Introduction
    II. Prerequisites
    III. Tutorial
    IV. Reference
    V. Getting help
    VI. Frequently asked questions (FAQs)

I. Introduction

    This is a bash-based pipeline for analysis of PacBio Iso-Seq generated with RSII or Sequel. Collectively, the pipeline will do:
    
    A.	For RSII, extract raw reads, subreads and CCS reads from raw data in h5; For Sequel, only extract the CCS reads.
    B.	Do some metadata analysis and give statistic reports, like adapter, raw reads length, raw reads quality, etc.
    C.	Get full-length reads according to primer sequences and polyA. Completed sequences, which may represent the whole transcripts, are generated by cutting primer and polyA.
    D.	Do some pre-mapping evaluation, like GC content of read, GC content across read, base quality across read.
    E.	Mapping full-length reads onto genome.
    F.	Filter and refine the raw alignments generated by mapping, like removing circular and translocated alignments, remove alignments with low coverage and identity, etc.
    G.	Do some post-mapping evaluation, like read-length distribution, distance to TSS/TTS, etc.
    H.	PA analysis, like distribution of cleavage site distance, motif around PA site, etc.
    I.	Identify Alternative RNA Processing Events (APE, including SE, IR, A5SS, A3SS, APA) with CCS reads and junctions from RNA-seq (if available).
    J.	APE characterization, like motif of splicing site, classifying APE into annotated or novel events, etc.
    K.	Combination pattern of APE.

II. Prerequisites

    The given version is just to suggest you to use this version, but not to prohibit you from using newer version, although we haven’t tested the newer ones and some unknown error may occur.
    Needed R packages: ggplot2, EMT, etc.
    The needed in-house scripts (fqNameMapping.pl, fqSeFilter.pl, etc.) are packaged in the source release.

III. Tutorial

3.1 Setup Environment
Assume that the pipeline is put at ~/bin/isoSeq.

This pipeline is composed of perl, python, bash and R scripts as well as pre-compiled C binaries, so no need to build/compile them. The only thing needed to do is making them executable:

    cd ~/bin/isoSeq
    chmod u+x *.{pl,py,sh,R} skyjoin faFilter faCount
    
Add the path of the pipeline as the prefix of the environment variable $PATH, so that all scripts can be runed as executable command directly.

    PATH=~/bin/isoSeq:$PATH

After doing this, the correct $PATH may look like:

    echo $PATH
    ~/bin/isoSeq:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
    
Test whether the pipeline is accessible:

    isoSeq.sh
    
If pipeline hele message is shown, the environment is ready, otherwise if the below error is shown:

    bash: isoSeq.sh: command not found...

check whether the pipeline scripts are executable and their path is in the $PATH variable.
