# anellovirus_reference
NCBI virus Human Anellovirus References including index and script to kallisto index using kallisto.


# TODO 
1) Add scripts that download the references, cluster, hardmask plus the gtf


# Workflow 
* NCBI virus extract Anellovirus, host == human, complete genome 
n=5.5k 

* Clustering into representative sequences with vclust CD-HIT
n=2.2k 

* hardmask representative sequences with dustmask 30/64 

# Quantification

Hardmasked reference genome is a fasta compatible with any number of detection strategies. 
Due both the sequence diversity and yet strong similaritiy across anellovirus genomes I have shown that pseudoalignment with kallisto offers idealized quantification of these similar contigs without custom scripts to handle multimapping bams. 

I have used kallisto V0.48.0 which allows psuedobam generation for genome coverage quantification. The kallisto you use to create the index but be exactly the same as the version you use for quantification. 

Note: Kallisto accepts paired or single ended reads, however fragment size and std dev are required parameters for the tool which are *required in single end mode* but inferred from the data in paired end. 

```
module load kallisto/0.48.0
use_cores=2 # n cores for quant
idx="/path/to/ref/hardmasked_cdhit_rep_anelloviridae_061725_genomic.idx" ## hardmasked anello reference
FQ_out="/path/to/sampleoutput" #
F1="/path/to/fastq/R1"
F2="/path/to/fastq/R2"

kallisto quant -t $use_cores -i $idx -o $FQ_out $F1 $F2


FQ="/path/to/fastq/singleend"
# estimating parameters are 300 fragment w/ 20bp std dev, ideally bioanalyzer trace to infer but can make educated guess based on library prep protocol
kallisto quant -t $use_cores -i $idx -o $FQ_out --single -l 300 -s 20 $FQ
```

# Genome Coverage 

If you are interested in the genome coverage you can run a now defunt psuedobam mode of kallisto to extract the coordinates on target reads are compatible with. 
NOTE: this mode is VERY SLOW!!! Likely because it is looping over and sorting the entire input instead of extracting on target reads. Later versions of kallisto do not support this feature and it is no longer supported. 

This code chunk uses the fusion function which calls 'gene' fusion events across different anellovirus contigs if present. Mostly returns an empty file but rare samples do have a recombined anellovirus which it can flag.

```
gtf="/path/to/ref/anello_for_kallisto.gtf" ## manually created gtf file 
chrs="/path/to/ref/anello_sizes.txt" ## contig sizes 
kallisto quant -t $use_cores -i $idx --genomebam --gtf $gtf --chromosomes $chrs -o $FQ_out --fusion $FQ1 $FQ2
```

