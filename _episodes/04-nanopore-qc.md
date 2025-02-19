---
# Please do not edit this file directly; it is auto generated.
# Instead, please edit 04-nanopore-qc.md in _episodes_rmd/
title: "Nanopore: quality assessment"
teaching: 25
exercises: 20
questions:
- "How can I perform quality assessment of ONT data?"
objectives:
- "Assess the quality of reads."
- "Generate summary metrics."
keypoints:
- "FastQC and NanoPlot provide pre-formatted reports on run characteristics and read quality."
- "Bioawk can be used to generate summary metrics and ask specific questions about the read data."
source: Rmd
---



## Software for quality assessment

In this part of the module, we'll look at software applications that can be used to assess the quality of data from ONT sequencing runs. We'll be focussing on two pieces of software: FastQC and NanoPlot.

- FastQC (you've already seen this): generic tool for assessing the quality of next generation sequencing data.
- NanoPlot: quality assessment software specifically built for ONT data.

## NanoPlot

Let's use the NanoPlot software to assess the quality of our unaligned `.bam` data.  We can run NanoPlot interactively (i.e., we don't need to submit it as a slurm job), and it wil produce an HTML-based report that can be viewed in a browser.

Load the NanoPlot module:

~~~
module load NanoPlot
~~~
{: .bash}

Run NanoPlot on `.bam` file:

~~~
NanoPlot -o nanoplot_fastmodel \
         -p ecoli_fastmodel_ \
         --ubam bam-unaligned/ecoli-pod5-pass-basecalls.bam
~~~
{: .bash}

Command syntax:

- `NanoPlot`: run the NanoPlot command (note that the capital N and P are required).
- `-o nanoplot_fastmodel`: folder for output (`-o`) to be written.
- `-p ecoli_fastmodel_`: prefix (`-p`) to be appended to start of each filename in the output folder.
- `--ubam bam-unaligned/ecoli-pod5-pass-basecalls.bam`: the unaligned bam (`ubam`) file to process with NanoPlot.

Note that (for reasons we won't get into) NanoPlot will probably give you the following warning when it runs (but it will still work):

~~~
E::idx_find_and_load] Could not retrieve index file for 'ecoli-pod5-pass-basecalls.bam'
~~~
{: .output}

Let's check what output was generated:

~~~
ls -1 nanoplot_fastmodel/
~~~
{: .bash}

~~~
ecoli_fastmodel_LengthvsQualityScatterPlot_dot.html
ecoli_fastmodel_LengthvsQualityScatterPlot_dot.png
ecoli_fastmodel_LengthvsQualityScatterPlot_kde.html
ecoli_fastmodel_LengthvsQualityScatterPlot_kde.png
ecoli_fastmodel_NanoPlot_20231126_0241.log
ecoli_fastmodel_NanoPlot-report.html
ecoli_fastmodel_NanoStats.txt
ecoli_fastmodel_Non_weightedHistogramReadlength.html
ecoli_fastmodel_Non_weightedHistogramReadlength.png
ecoli_fastmodel_Non_weightedLogTransformed_HistogramReadlength.html
ecoli_fastmodel_Non_weightedLogTransformed_HistogramReadlength.png
ecoli_fastmodel_WeightedHistogramReadlength.html
ecoli_fastmodel_WeightedHistogramReadlength.png
ecoli_fastmodel_WeightedLogTransformed_HistogramReadlength.html
ecoli_fastmodel_WeightedLogTransformed_HistogramReadlength.png
ecoli_fastmodel_Yield_By_Length.html
ecoli_fastmodel_Yield_By_Length.png
~~~
{: .output}

The file that we are interested in is the HTML report: `ecoli_fastmodel_NanoPlot-report.html`

To view this in your browser:

1. Use the file browser (left panel of Jupyter window) to navigate to the `nanoplot_fastmodel` folder.
2. Control-click (Mac) or right-click (Windows/Linux) on the "ecoli_fastmodel_NanoPlot-report.html" file and choose "Open in New Browser Tab".
3. The report should now be displayed in a new tab in your browser.

## FastQC

Load the FastQC module:

~~~
module load FastQC
~~~
{: .bash}

Make a folder for the FastQC output to written to:

~~~
mkdir fastqc_fastmodel
~~~
{: .bash}

Run FastQC on our data:

~~~
fastqc -t 4 -o fastqc_fastmodel bam-unaligned/ecoli-pod5-pass-basecalls.bam
~~~
{: .bash}

Command syntax:

- `fastqc`: run the `fastqc` command
- `-t 4`: use four cpus to make it go faster (the `-t` is for "threads").
- `-o fastqc_fastmodel`: specify output folder.
- `bam-unaligned/ecoli-pod5-pass-basecalls.bam`: data file to analyse.

Check the output:

~~~
ls -1 fastqc_fastmodel
~~~
{: .bash}

~~~
ecoli-pod5-pass-basecalls_fastqc.html
ecoli-pod5-pass-basecalls_fastqc.zip
~~~
{: .output}

As we did with the NanoPlot output, we can view the HTML report in the browser:

1. Use the file browser (left panel of Jupyter window) to navigate to the `fastqc_fastmodel` folder.
2. Control-click (Mac) or right-click (Windows/Linux) on the "ecoli-pass_fastqc.html" file and choose "Open in New Browser Tab".
3. The report should now be displayed in a new tab in your browser.

## Bioawk (we might skip this one)

FastQC and NanoPlot are great for producing pre-formatted reports that summarise read/run quality.  

But what if you want to ask some more specific questions about your data?

Bioawk is an extension to the Unix `awk` command which allows use to query specific biological data types - here we will use it to ask some specific questions about our FASTQ data.

Load the bioawk module.

~~~
module load bioawk
~~~
{: .bash}

To run `bioawk` (no inputs, so won't do anything useful):

~~~
bioawk 
~~~
{: .bash}

~~~
usage: bioawk [-F fs] [-v var=value] [-c fmt] [-tH] [-f progfile | 'prog'] [file ...]
~~~
{: .output}

View help info on "format" (`-c`):

~~~
bioawk -c help
~~~
{: .bash}

~~~
bed:
        1:chrom 2:start 3:end 4:name 5:score 6:strand 7:thickstart 8:thickend 9:rgb 10:blockcount 11:blocksizes 12:blockstarts 
sam:
        1:qname 2:flag 3:rname 4:pos 5:mapq 6:cigar 7:rnext 8:pnext 9:tlen 10:seq 11:qual 
vcf:
        1:chrom 2:pos 3:id 4:ref 5:alt 6:qual 7:filter 8:info 
gff:
        1:seqname 2:source 3:feature 4:start 5:end 6:score 7:filter 8:strand 9:group 10:attribute 
fastx:
        1:name 2:seq 3:qual 4:comment  
~~~
{: .output}

Syntax is different depending on what type of data file you have.  Here we have an unaligned BAM file.

Unfortunately bioawk doesn't work with BAM formatted data (which is a compressed vesion of SAM) - we need to convert our BAM to SAM...

This can be done using SAMtools.

Load SAMtools module:

~~~
module load SAMtools
~~~
{: .bash}

The `samtools view` command can be used to do the conversion. To output to the console:

~~~
samtools view bam-unaligned/ecoli-pod5-pass-basecalls.bam | more
~~~
{: .bash}

We can redirect the outoput to create a SAM file:

~~~
samtools view bam-unaligned/ecoli-pod5-pass-basecalls.bam > ecoli-pod5-pass-basecalls.sam
~~~
{: .bash}

For a SAM file (using the information from `bioawk --help`), if we're interested in each read's 
name and length, we would query `qname` and `seq`.

Extract read name and length using `bioawk`:

~~~
bioawk -c sam '{print $qname,length($seq)}' ecoli-pod5-pass-basecalls.sam | head
~~~
{: .bash}

~~~
01b81395-0397-45c3-a62f-3184fafb8f4a    11129
0202d7fc-f4f9-4242-8dac-5dcda36dcebc    4104
028f0c75-29b5-4877-8f2f-c070a4563e70    1316
0249f90e-2f03-4dc7-860b-5aba518ba5ec    4999
02dba632-4671-43a0-840d-a7798f70cb79    8627
035e2d3f-b6b8-45b7-9eb7-2973c02b6666    3282
01207b3d-ded6-439a-97f6-2b3ccda77290    19269
03186e67-e391-4898-9554-57f8cfb8a3d5    9462
02944994-7ed1-47ce-ad53-59dba640995a    8858
0491758b-dde6-4693-9304-fb3eacdb5f02    6586
~~~
{: .output}

Note that is we had a `.fastq` (or `.fastq.gz`) file, then we could extract the same information via 
(note the slightly different syntax - file format and parameter names):

~~~
# DON'T RUN THIS CODE - IT IS JUST AN EXAMPLE FOR PROCESSING FASTQ/FASTQ.GZ DATA
bioawk -c fastx '{print $name,length($seq)}' ecoli-ecoli-pod5-pass-basecalls.fastq.gz | head
~~~
{: .bash} 

To count the number of reads, we can pipe the output to the `wc` command:

~~~
bioawk -c sam '{print $qname,length($seq)}' ecoli-pod5-pass-basecalls.sam | wc -l
~~~
{: .bash}

~~~
13220
~~~
{: .output}

Total number of bases in first ten reads (I've stepped through the process to illustrate each component of the command):

~~~
bioawk -c sam '{print length($seq)}' ecoli-pod5-pass-basecalls.sam | head
~~~
{: .bash}

~~~
11129
4104
1316
4999
8627
3282
19269
9462
8858
6586
~~~
{: .output}

~~~
bioawk -c sam '{print length($seq)}' ecoli-pod5-pass-basecalls.sam | head | paste -sd+ 
~~~
{: .bash}

~~~
11129+4104+1316+4999+8627+3282+19269+9462+8858+6586
~~~
{: .output}

~~~
bioawk -c sam '{print length($seq)}' ecoli-pod5-pass-basecalls.sam | head | paste -sd+ | bc
~~~
{: .bash}

~~~
77632
~~~
{: .output}

So the first 10 reads comprise 77,632 bases.

Total number of bases in all reads (remove the `head` command):

~~~
bioawk -c sam '{print length($seq)}' ecoli-pod5-pass-basecalls.sam | paste -sd+ | bc
~~~
{: .bash}

~~~
129866671
~~~
{: .output}

In total, the 13,220 reads in our data set comprise 129,866,671 bases (130Mbp).


Number of reads longer than 10000 bases

First create "yes/no" (1/0) information about whether each read has a length greater than 10,000 bases:

~~~
bioawk -c sam '{print (length($seq) > 10000)}' ecoli-pod5-pass-basecalls.sam | head
~~~
{: .bash}

~~~
1
0
0
0
0
0
1
0
0
0
~~~
{: .output}

Now sum this up to find the total number of reads satisfying this condition:

~~~
bioawk -c sam '{print (length($seq) > 10000)}' ecoli-pod5-pass-basecalls.sam | paste -sd+ | bc
~~~
{: .bash}

~~~
4527
~~~
{: .output}

So, of our 13,220 reads, 4527 of them are longer than 10,000 bases.

