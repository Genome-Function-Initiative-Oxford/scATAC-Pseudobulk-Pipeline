# scATAC Pseudobulk Pipeline
## About
This Snakemake workflow is designed to split one or more scATAC-seq BAM files into pseudobulk replicates, either of equal cell number by sub-sampling to $n$ cell barcodes, or using a supplied set of cell barcodes.

## Pipeline Steps
1. If no cell barcodes given, then count the number of unique occurrences of each cell barcode per BAM file and assign cell barcodes a label corresponding to a pseudobulk replicate to create
2. Split the BAM file into pseudobulk BAM files using [Sinto](https://timoast.github.io/sinto/) to separate the cell barcodes
3. Generate indexes for the pseudobulk BAM files
4. Generate bigWigs for the pseudobulks
5. Call peaks for the pseudobulks
6. Create a metadata file summarising the number of pseudobulks created from each input BAM file

## Running the Pipeline
1. Set up the pseudobulk conda enviroment using [workflow/envs/pseudobulk_env.yaml](https://github.com/Genome-Function-Initiative-Oxford/scATAC_Pseudobulk_Pipeline/blob/main/workflow/envs/pseudobulk_env.yaml)

   `conda env create --name pseudobulk --file=pseudobulk_env.yml`
   
3. Edit config/config.yaml to set the data and parameters
4. Then the pipeline can either be run manually via command line or scheduled to run on a cluster via Slurm.

### Command Line
1. Open a terminal and navigate to the folder containing the workflow, e.g.
   
   `cd user/path_to_projects/scATAC_Pseudobulk_Pipeline`

2. Activate conda environment with dependencies
3. Run the workflow via the command below, setting `n` as the number of processors to use

`snakemake --cores n`

### Running on a Cluster via Slurm
The pipeline can be scheduled to run on a cluster using the file `submit.sh`

### Custom Barcodes
If separating BAMs with a custom barcode_split file, barcodes must match those within the BAM. A way to check the format of cell barcodes within a BAM is to use samtools, e.g.
```
samtools view yourfile.bam | awk '{for(i=12;i<=NF;i++){if($i ~ /^CB:Z:/){print $i; exit}}}'
```

This will print the first cell barcode, e.g. `CB:Z:GCGCTTGCAACAGCCT-1`. If this does not print anything, the BAM may not contain cell barcodes. 
