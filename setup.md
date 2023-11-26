---
title: Setup
---

## Compute platform

This workshop is designed to be conducted on the [NeSI](https://www.nesi.org.nz) compute infrastructure. All software and data are already set up for you to use during the workshop.

## Software used

If you are attempting to work through this material on a non-NeSI compute system, the following software will need to be installed:

| Software  | Version | Manual      | Description 	|
|-----------|---------|-------------|---------------|
| BCFtools  | 1.16  | [link](https://samtools.github.io/bcftools/) | Variant calling | 
| bioawk    | 20110810| [link](https://github.com/lh3/bioawk) | awk for biological data | 
| FastQC    | 0.12.1  | [link](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)| Read QA/QC |
| dorado | 6.2.1   | [link](https://github.com/nanoporetech/dorado) | GPU basecaller for ONT POD5/FAST5 data |
| Nanoplot  | 1.41.0 | [link](https://github.com/wdecoster/NanoPlot) | QA/QC for ONT reads | 
| SAMtools  | 1.16.1  | [link](http://www.htslib.org/doc/samtools.html) | Read mapping |


## Data availability

The data utilised in this workshop are publicly available, but are reasonably old (2016), so are not a particularly good demonstration of the quality or volume of data that the current ONT sequencing devices are able to produce.

See the following link for a description of the data (and a rather nostalgic look at the excitement associated with ONT's "new" version 9 chemistry):

[http://lab.loman.net/2016/07/30/nanopore-r9-data-release/](http://lab.loman.net/2016/07/30/nanopore-r9-data-release/)

The specific data used for this workshop can be downloaded here (note that this URL is also provided within the post at link above), although to reduce file sizes, only a subset of reads were used:

[https://s3.climb.ac.uk/nanopore/E_coli_K12_1D_R9.2_SpotON_2.tgz](https://s3.climb.ac.uk/nanopore/E_coli_K12_1D_R9.2_SpotON_2.tgz)

Also worth noting - the original data format was one fast5 file per read, whereas the current fast5 format used by ONT is multiple reads per file. The command `single_to_multi_fast5` from the `ont_fast5_api` package:

[https://github.com/nanoporetech/ont_fast5_api](https://github.com/nanoporetech/ont_fast5_api)

was used here to convert the single-read fast5 data to multi-read fast5.

The fast5 files were then coverted to pod5 format via ONT's `pod5` tool:

[https://github.com/nanoporetech/pod5-file-format](https://github.com/nanoporetech/pod5-file-format)

{% include links.md %}
