# STRfinder
A bioinformatic tool to detect and genotype STRs;

# Requirements

# Installation
STRfinder has been compiled into binary file, you can download and run it directory;

# Config file preparation
Before running STRfinder to type STRs markers, you need to prepare some config files containing certain information of STRs markers you are interested in. 
1) STRs_site.txt
   |Chr|Start|End|Period|Reference allele|Marker|STR sequence structure|Strand|5' Flanking sequence|3' Flanking sequence|STR_type|
   |---|-----|---|------|----------------|------|----------------------|------|--------------------|--------------------|--------|
   |chr1|230769616|230769683|4|17|D1S1656|\[CCTA\]_n \[TCTA\]_n|+|TTAAACACACACACACA|CATCACACAGTTGAC|1|
- Seprator in the file is TAB;
- Chr: chromosome id;(required)
- Start: the start position(1-base coordinate) of STR core repeat region for the related STR marker;(required)
- End: the end position(1-base coordinate) of STR core repeat region for the related STR marker;(required)
- Period: additional stage information in ISFG for the marker, the value is not used in STRfinder ;if the information is not available, you can fill in "0" here；
- Reference allele: repeat number of the STR marker in human reference genome, the value is not used in STRfinder;if the information is not available, you can fill in "0" here；
- Marker：name of STR marker，please only include letter/number/underline in the name, space or any other special characters may raise running error;(required)
- STR sequence structure: repeat motif of the STR marker，the sequence must be from forward strand of reference genome;(required)
- Strand: please enter "+"; 
- 5'Flanking sequence: the 5'flank sequence of STR marker; value in this file in not used in STRfinder, you can also jsut fill in 'NA' here;
- 3'Flanking sequence: the 3'flank sequence of STR marker; value in this file is not used in STRfinder, you can also jsut fill in 'NA' here;
- STR_type: the value indicates the ploidy of STR marker; if the marker is diploidy, please fill in "1" here; if the marker is haploidy, please fill in "2" here;
3) STRs_loci.bed
4) STR_primers.bed
5) flank sequence in fasta format for each STRs marker


Usage

Config file prepareation:



