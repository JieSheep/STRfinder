# STRfinder
A bioinformatics tool to detect and genotype STRs;

# Installation
STRfinder has been compiled into binary file, you can download and run it directory;

# Config file preparation
Before running STRfinder to type STRs markers, you need to prepare some config files containing certain information of STRs markers you are interested in. These config files don't need to be changed until you change your STR panel.  

**1. STRs_site.txt**
<a name="anchor1"></a>
|Chr|Start|End|Period|Reference allele|Marker|STR sequence structure|Strand|5' Flanking sequence|3' Flanking sequence|STR_type|
|---|-----|---|------|----------------|------|----------------------|------|--------------------|--------------------|--------|
|chr1|230769616|230769683|4|17|D1S1656|\[CCTA\]_n \[TCTA\]_n|+|TTAAACACACACACACA|CATCACACAGTTGAC|1|
- Seprator in the file is TAB;
- Chr: chromosome id;
- Start: the start position(1-base coordinate) of STR core repeat region for the related STR marker;
- End: the end position(1-base coordinate) of STR core repeat region for the related STR marker;
- Period: additional stage information in ISFG for the marker, the value is not used in STRfinder ;if the information is not available, you can fill in "0" here；
- Reference allele: repeat number of the STR marker in human reference genome, the value is not used in STRfinder;if the information is not available, you can fill in "0" here；
- Marker：name of STR marker，please only include letter/number/underline in the name, space or any other special characters may raise running error;
- STR sequence structure: repeat motif of the STR marker，the sequence must be from forward strand of reference genome;
- Strand: please enter "+"; 
- 5'Flanking sequence: the 5'flank sequence of STR marker; value in this file in not used in STRfinder, you can also jsut fill in 'NA' here;
- 3'Flanking sequence: the 3'flank sequence of STR marker; value in this file is not used in STRfinder, you can also jsut fill in 'NA' here;
- STR_type: the value indicates the ploidy of STR marker; if the marker is diploidy, please fill in "1" here; if the marker is haploidy, please fill in "2" here;

**2. STRs_loci.bed**  
<a name="anchor2"></a>
A bed file contains the chr/star/end information of core repeat region for each STR markers in your panel. This file is used to evaluate how many reads and bases covers you STR core repeat region. If you don't pay attention to this information, you can provide any existed BED file.  

**3.  STR_primers.bed**  
<a name="anchor3"></a>
A bed file contains the chr/start/end information of your amplified/probe_covered region for each STR markers in your panel. This file is used to evaluate how many reads and bases covers your amplified STRs regions. If you don't pay attention to this information, you can provide any existed BED file.  

**4.  flank sequence in fasta format for each STRs marker**  
<a name="anchor4"></a>
For each STR marker, you must prepare a fasta file contains flank sequences. The fasta file must be named as marker.fa with the character of marker must be consistent with marker name in the file STRs_site.txt. Content of the fasta file goes as follow,


> \>DYS460_p5  
> GTATTTGTCTATTAT  
> \>DYS460_p7  
> GTCAGAGGTGTCAGA  

5'flank sequence of a STR marker must be named as marker_p5, 3'flank sequence of a STR marker must be named as marker_p7, marker is the name of the marker which is consistent with the marker.fa file. The length of flanking sequence is up to you, the recommended value is above 15bp, while the base sequence must be concordant with the forward strand of reference genome. Once the fasta files for each STR marker  are prepared, all the marker.fa file need to be placed in the same folder.  

**5. ID correspondence file**  
<a name="anchor_id"></a>
Sometimes STRs markers' name typed in the previous config file may not satisfy the practical application, in order to generate customer satisfied genotype STRs marker names in the final result, a file indicates the ID correspondence is necessary. The ID correspondence file involves two columns separated by TAB, the first column is the STRs markers name used in the previous config file, and the second column is your expected output STRs markers name.
|#Used ID|Final ID|
|--------|--------|
|DYS460|DYS460|
|PentaE|Penta E|
|DYS459|DYS459 a/b|
 
# Tools Requirements
<a name="anchor_tool"></a>
STRfinder has applied several tools to conduct QC/Alignment/Sampling/Consensus. These binary executable tools(bamdst/minimap2/samtools/seqkit/spoa) must be prepared ready and placed in the same directory before running STRfinder. For example, you have built a directory named STR_Tools, the directory will contains files including bamdst/minimap2/samtools/seqkit/spoa binary executable file.
- [bamdst](https://github.com/shiquan/bamdst)
- [minimap2](https://github.com/lh3/minimap2)
- [samtools](https://github.com/samtools/samtools)
- [seqkit](https://github.com/shenwei356/seqkit)
- [spoa](https://github.com/rvaser/spoa)
  
# Usage
```
STRfinder --working_path <your OUT Dir> \
       --fastq <your input fq> \
       --sample <your sample name> \
       --STR_ref_bed <STR_site_file> \
       --STR_flank_ref_dir <STR_flank_dir> \
       --ref <genome REF fa or index> \
       --reads_threshold 100 \
       --num_threads 20 \
       --num_process 10 \
       --primer_bed <STR_primer_bed> \
       --target_bed <STR_target_bed> \
       --tools_dir <Tools_Dir> \
```
- working_path: your output diretory(required);
- fastq: your input fastq file path(required);
- sample: your sample name(required);
- STR_ref_bed: equals to the [STR_site.txt](#anchor1) file in Config file Preparation section(required)；
- STR_flank_ref_dir: equals to the dir([STR_flank_fasta](#anchor4)) geneated in the part5 section of the former Config file Preparation part(required);
- ref: genome reference fasta file or minimap2 index file(required);
- reads_threashold: the lowest original depth of each STRs markers; Markers with orginal depth lower than this value won't be genotyped in the pipeline, the final typing result of relative markers will be "-";default is 100;
- num_threads：threads number used in minimap2 alignment;default is 20;
- num_process: process number used in the STRs markers genotyping by STRfinder; default is 10;
- primer_bed: equals to the [STR_primers.bed](#anchor2) in the Config file Preparation section(required);
- target_bed: equals to the [STR_loci.bed](#anchor3) in the Config file Preparation section(required);
- tools_dir: equals to the [STR_Tools](#anchor_tool) directory path in the Tools Requirement section(required);
- id_trans: equals to the [ID correspondence file](#anchor_id) path in the Config file Preparation section(required);

# Output
The most important output file is sample_genotypes.csv which contains the genotyping results for STRs markers. Its format goes as,
|STR_marker|STR_position|STR_motif|STR_genotype_structure|STR_genotype|STR_core_seq|Allele_coverage|Alleles_ratio|Reads_Distrubution(consensused)|STR_depth|Full_seq|
|----------|------------|---------|----------------------|------------|-----------------|---------------|-------------|-------------------------------|---------|--------|
|D7S820|chr7:84160226-84160277|[TATC]n|[TATC]8,[TATC]11|8,11|TATCTATCTATCTATCTATCTATCTATCTATC,TATCTATCTATCTATCTATCTATCTATCTATCTATCTATCTATC|82.4%|99.8%|7:0,7.1:0,7.2:0,7.3:0,8:150,8.1:0,8.2:0,8.3:0,9:0,9.1:0,9.2:0,9.3:0,10:0,10.1:0,10.2:0,10.3:0,11:200,11.1:0,11.2:0,11.3:0,12:0|3434|5'aaaaaaactatcaatctgtc-TATCTATCTATCTATCTATCTATCTATCTATC-gttagttcgttctaaactat3',5'aaaaaaactatcaatctgtc-TATCTATCTATCTATCTATCTATCTATCTATCTATCTATCTATC-gttagttcgttctaaactat3'|
- STR_marker: Name of STRs marker;
- STR_position: Position of STRs marker, formatted in "chr:start-end", the coordinate information is the same with ones in STR_site.txt config file;
- STR_motif: Motif structure of STRs marker, it's the same with the value in STR_site.txt config file;
- STR_genotype_structure: STR core region sequence formatted in the motif structure obtained from STR finder for the sample's STR marker;
- STR_genotypes: STR genotype result (repeat numaber) of marker; if the value is "-", it means the marker cannot be genotyped for low depth or genotype error risk;
- STR_core_seq: STR core region sequence obtained from STRfinder for the sample's STR marker;
- Allele_coverage: Ratio of reads contributes to the alleles to total reads covered the STR marker;
- Alleles_ratio: Ratio of reads contributes to two alleles, allele1/allele2;for haploidy markers, this value will be "-";
- Reads Distribution(consensused): The consensused reads distribution for final genotyped alleles;
- Full_seq: Sequence is composed of 5'flanking -STR repeat core region-3'flanking. The flanking sequence is in lowercase, while the repeat core region is in uppercase; 
