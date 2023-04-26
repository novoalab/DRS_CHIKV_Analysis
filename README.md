# Analysis of DRS data from chikungunya virus
Direct RNA sequencing (DRS) analysis included in article: N6-methyladenosine modification is not a general trait of viral RNA genomes

### Basecalling and demultiplexing:
The module [mop_preprocess](https://biocorecrg.github.io/master_of_pores/nanopreprocess.html) module from [Master of Pores2](https://github.com/biocorecrg/MOP2) pipeline was used to perform basecalling, with guppy (version 3.1.5, model rna_r9.4.1_70bps_hac), and demultiplexing - when needed - with Deeplexicon (version 1.2.0). Then, it also mapped the basecalled reads to the suitable viral reference (for chikungunya: `ref/Escherichia_coli.rRNA.fa` and for adenovirus: `ref/Saccharomyces_cerevisiae.rRNA.fa`) with miniamap2 (version 2.17) with unspliced parameters (-uf -k14 -ax map-ont). Moreover, same parameters were used to align reads to the to the human transcriptome from Ensembl, based on assembly GRCh38 (ref/XXX). To execute this module, please add these parameters into both `params.config` and `tools_opt.tsv` files and run the command below: 

```
nextflow run mop_preprocess.nf -with-singularity -bg > log.txt
```

### Extracting full-length reads:
For each sample and transcript, only full-length reads were included in the different analysis done in this study:
* Generate bed file from bam file to know the alignment start and end for each read:
```
bedtools bamtobed -i Ecoli.sorted.bam > Ecoli.sorted.bam.bed
```
* Extracting reads' ID from full length reads:
```
#Usage: 
bash ./scripts/Full_Length/Extract_FullLength_IDs.sh /path/to/bed chr start_position end_position output_name

#Examples:
bash ./scripts/Full_Length/Extract_FullLength_IDs.sh Ecoli.sorted.bam.bed 16S 50 1525 Ecoli
bash ./scripts/Full_Length/Extract_FullLength_IDs.sh Ecoli.sorted.bam.bed 23S 50 2894 Ecoli
```

* Extracting basecalled fast5, fastq and alignments from full-length reads:
  
To extract basecalled fast5, fastq and alignments from full-length reads from specific samples, please use the script and the code below: 
```
#Usage: 
bash ./scripts/Full_Length/FullLength_Basecalled_Fastq_Bam.sh /path/input/sample output_folder full-length_readsIDs.txt

#Examples:
bash ./scripts/Full_Length/FullLength_Basecalled_Fastq_Bam.sh Ecoli_Untreated_1h Ecoli_Untreated_1h_FullLength Ecoli_Untreated_1h.16S.read_IDs.txt 
```


### Running RNA modification detection softwares: 

EpiNano, Nanopolish, Tombo and Nanocompore were run by the module [mop_mod](https://biocorecrg.github.io/master_of_pores/nanomod.html) module from [Master of Pores2](https://github.com/biocorecrg/MOP2) with the default options for both EpiNano and Nanopolish whereas Tombo was run with additional options `--percent-to-filter 80` and `--multiprocess-region-size 50`. Finally, Nanocompore was run with `--downsample_high_coverage 5000`. To execute this module, please add these parameters into `tools_opt.tsv` file and run the command below: 

```
nextflow run mop_mod.nf -with-singularity -bg > log.txt
```

### Running NanoConsensus:

NanoConsensus was run by the module mop_consensus from [Master of Pores2](https://github.com/biocorecrg/MOP2) with both default parameters and relaxed ones (`--MZS 3.75` and `--NC 4`).

```
nextflow run mop_consensus.nf -with-singularity -bg > log.txt
```
