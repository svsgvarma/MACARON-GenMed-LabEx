MACARON User Guide
================

# Table of Contents

[//]: # (BEGIN automated TOC section, any edits will be overwritten on next source refresh)

* [Introduction](#introduction)
* [Installation](#installation)
    * [Operating System Guidelines](#operating-system-guidelines)
    * [Runtime Pre-requisite](#runtime-pre-requisite)
    * [Software Dependencies](#software)
    * [Downloading the Source Code](#downloading-the-source-code)
* [Contents of the Folder MACARON_GenMed](#contents-of-the-folder-macaron-genmed)
    * [demo Folder](#demo-folder)
* [Running the MACARON](#running-the-macaron)
    * [Input Requirements](#input-requirements)
    * [Additional Opions](#additional-options)
* [Known Limitations](#known-limitations)

[//]: # (END automated TOC section, any edits will be overwritten on next source refresh)

# Introduction

MACARON (Multi-bAse Codon-Associated variant Re-annotatiON) is a python framework to identify and re-annotate multi-base affected codons in whole genome/exome sequence data. Starting from a standard VCF file, MACARON identifies, re-annotates and predicts the amino acid change resulting from multiple single nucleotide variants (SNVs) within the same genetic codon. 

The information below includes how to install and run MACARON to filter a list of variant records (from VCF file) called by any existing SNP-based variant caller to identify SNVs with the same genetic codon and correct their corresponding amino acid change.

# Installation

### Operating System Guidelines

MACARON is know to run on LINUX UBUNTU 16.04 LTS. However, MACARON can be run on any other LINUX version.

### Runtime Pre-requisite

__1.__ MACARON is executable in __PYTHON v2.7 or later__. If the user has multiple PYTHON versions, please make sure that your running environment is set to the required version of PYTHON.

__2.__ Check your __JAVA__ version as MACARON is tested with:

      java -version
      openjdk version __"1.8.0_151"__
      OpenJDK Runtime Environment (build 1.8.0_151-8u151-b12-0ubuntu0.16.04.2-b12)
      OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)

### Software Dependencies

Before running MACARON, please make sure that following software are properly installed:

__1.__ __Genome-Analysis Toolkit__ (https://software.broadinstitute.org/gatk/download/).

__2.__ __SnpEff__ (tested with __v4.3__ (build 2017-05-05 18:41). However, MACARON can also run with any older or newer version (http://snpeff.sourceforge.net/download.html).

__3.__ __SAMTools__ (tested with version __0.1.19__), however any version can be used.

__4.__ __Human Reference Genome__: Depends on user’s input.

__5.__ __SnpEff’s Human Annotation Database__: Depends on user’s input.

For __1__ and __2__, as long as they are compatible with JAVA, MACARON has no issues.

### Downloading the Source Code

The most prefered way to use the lastest version of MACARON is:

      git clone https://github.com/waqasuddinkhan/MACARON-GenMed-LabEx.git

or download the ZIP folder.

MACARON source code can also be downloaded from http://www.genmed.fr/images/publications/data/MACARON_GenMed.zip

After acquiring a release distribution of the source code, the build procedure is to unpack the zip file:

      unzip MACARON_GenMed.zip

# Contents of the folder MACARON_GenMed

* *MACARON*  –  The MACARON python code
* *MACARON_validate.sh*  –  a BASH-shell script to validate multi-SNVs located on the same read that affect the same genetic codon

#### demo Folder

To help verify a successful installation, MACARON includes a small demo data set:

* *variants_of_interest.vcf* –  a test VCF file to check the functionality of MACARON
* *MACARON_output.txt*  –  The output file generated by running the MACARON
* *sub1.chr22_21349676-21349677.sample02.bam*  –  a subset of BAM file used as input for MACARON_validate.sh
* *MACARON_validate.txt*  –  The output file with read count information of concerned pcSNV in sample02 (in this case).

The demo can be run as follows:

python MACARON -i variants_of_interest.vcf -o MACARON_output.txt

See * [below] for detailed information.

# Running the MACARON

### Input Requirements

Before running MACARON, check these __input technical notes__ as the following limitations exist for either the input VCF file, or the required software dependencines:

- Chromosome (chr) notation should be compatible with both input VCF file and Human Reference Genome file, or vice versa,

- Sequence dictionaries of input VCF file and Human Reference Genome file should be the same,

- Input VCF file (should) suitably be annotated with ANNOVAR, and additionally with any other annotation software, e.g, VEP (https://www.ensembl.org/info/docs/tools/vep/index.html) if the user has a desire to get the full functionality of -f option (see below),

- Same Human Reference Genome file should be used for MACARON which is practiced earlier for alignemnt and (or) to call variant sets.

- Versions of input VCF file, Human Reference Genome file and SnpEff database file should be the same (hg19 / GRCh37 = SnpEff GRCh37.75) or (hg38 / GRCh38 = SnpEff GRCh38.86).

Output file name prefix: MACARON uses input VCF file name as prefix by default. User can change it with `-o` option.

```bash
python MACARON -i variants_of_interest.vcf -o MACARON_output.txt`
```

### ADDITIONAL OPTIONS

-f has three options:

* -f CSQ (if input VCF file is additionally annotated with VEP, the output txt file also has the same complete annotation for each variant record)

* -f EFF (if user wants to output SnpEff annotations in output txt file), or -f ANN (if SnpEff is used without -formatEff option)

* -f DP,AF,Func.refGene,Gene.refGene,GeneDetail.refGene (this will keep any other default annotations of input VCF file and of ANNOVAR to output txt file)

-f can be used multiple times, e.g.,

* -f CSQ,DP,Func.refGene
or
* -f EFF,CSQ,AF

The order of the columns in the output txt file depends on the order of INFO headers used here.


## VALIDATING SNVs' PRESENCE ON THE SAME READ

To confirm the existence of multi-SNVs within the same genetic codon, an accessory BASH-shell script (available with MACARON) calculates the read count information of affected bases. Using SAMTools, generate a subset of your BAM file (it should be the same used to generate VCF file for MACARON run) covering 50 bps over each clustered SNVs.

example: 

```bash
samtools view –hb –L sub1.bed sample02.bam > sub1.chr22_21349676-21349677.sample02.bam`
```

```bash
sub1.bed
chr22	21349676
```

The naming format of output BAM file should be the same.

Once subset BAM files are generated, run MACARON_validate.sh.

example: `MACARON_validate.sh sub1.chr22_21349676-21349677.sample02.bam`

This will generate an output text file (`MACARON_validate.txt`) allowing the user for further analysis.

Additional files that will be needed are:

A Note on TECHNICAL ISSUES:






## REFERENCES
* Van der Auwera G.A., et al. (2013) From FastQ Data to High-Confidence Variant Calls: The Genome Analysis Toolkit Best Practices Pipeline, Curr Protoc Bioinformatics, 43:11.10.1-11.10.33.
* Cingolani, P., et al. (2012) A program for annotating and predicting the effects of single nucleotide polymorphisms, SnpEff: SNPs in the genome of Drosophila melanogaster strain w1118; iso-2; iso-3, Fly, 6, 80-92.
* McLaren, W., et al. (2010) Deriving the consequences of genomic variants with the Ensembl API and SNP Effect Predictor, Bioinformatics, 26, 2069-2070.
* Wang, K., Li, M. and Hakonarson, H. (2010) ANNOVAR: functional annotation of genetic variants from high-throughput sequencing data, Nucleic Acids Res, 38, e164.

*CONTACT: david-alexandre.tregouet@inserm.fr*

*VERSION: 0.3*
*VERSION DATE: 11th April, 2018*

If you use [MACARON](https://academic.oup.com/bioinformatics/advance-article-abstract/doi/10.1093/bioinformatics/bty382/4992149?redirectedFrom=fulltext), please cite:

*Khan W. et al. MACARON: a python framework to identify and re-annotate multi-base affected codons in whole genome/exome sequence data, Bioinformatics 2018*
