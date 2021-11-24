# Hierarchical scPred classification

Cell type classification using the **Hierarchical scPred** method can be performed using the 
`map_hierscpred.R` module.. 

Let's create an output directory to store the results.


```bash
mkdir step3_hierscpred
```

And check the input arguments


```bash
singularity exec -B $PWD cell_classification.sif \ 
  Rscript /map_hierscpred.R --help
```

```
Usage: map_hierscpred.R [options]


Options:
        --file=CHARACTER
                RDS object file name

        --batch=CHARACTER
                Batch column. If provided, each group in from the batch columns is mapped to reference independently

        --thr=CHARACTER
                Threshold for rejection. By default no rejection is implemented

        --iter=CHARACTER
                Maximum number or Harmony iterations

        --plan=CHARACTER
                Strategy to resolve future [default= sequential]:
                multisession
                multicore
                cluster
                remote
                transparent

        --workers=NUMERIC
                Number of workers used for parallelization
                [default= 1]

        --mem=NUMERIC
                Maximum allowed total size (in GB) of global variables identified
                [default= Inf]

        --out=CHARACTER
                Output file name [default= hier_scpred]

        --path=CHARACTER
                Output path to store results [default= .]

        -h, --help
                Show this help message and exit
```

Further data splitting and classification can be performed within `map_hierscpred.R` 
via the `future` package, however, we'll classify cells for each of the partitions 
we already created.


Let's assume we have already classified cells using the *azimuth* approach. We can 
use the output Seurat `.RDS` files as as input for *hierchical scPred* as follows:



```bash
for i in $(ls step2_azimuth | grep ".RDS");
do
  out=$(echo $i | awk 'gsub(".RDS", "")')
  Rscript /map_hierscpred.R --file step2_azimuth/${i} --path step3_hierscpred --out ${out}
done
```

Similar to `map_azimuth.R`, `map_hierscpred.R` will return a Seurat object 
including:

-   Cell type classification (`scpred_prediction`) as a column in the metadata


## Parallelize classification: SGE example

The following array job code in SGE (Sun Grid Engine) can be used as a guide to 
classify each pool in individual jobs. This code snippet was used to
classify multiple Seurat objects (75 pools) from the OneK1K dataset. 


```bash
#$ -N clasify_cells
#$ -q short.q
#$ -l mem_requested=50G
#$ -S /bin/bash
#$ -r yes
#$ -cwd 
#$ -o results/2021-11-19_hier_scpred
#$ -e results/2021-11-19_hier_scpred

# mkdir results/2021-11-19_hier_scpred

cd $SGE_O_WORKDIR

# Set environmental variables
input=results/2021-10-28_pools
output=results/2021-11-19_hier_scpred

# Get job info
echo "JOB: $JOB_ID TASK: $SGE_TASK_ID HOSTNAME: $HOSTNAME"

# Get basefile name
files=($(ls ${input} | grep ".RDS"))
i="$(($SGE_TASK_ID-1))"

filename=${files[$i]}
filename=$(echo $filename | sed 's/.RDS//')

echo "Running for: $filename"

# Run main command
singularity exec -B $SGE_O_WORKDIR bin/cell_classification.sif \
  Rscript /map_hierscpred.R \
  --file ${input}/${filename}.RDS \
  --out ${filename}_out
  --path ${output}
```

If we save the previous code into a file (e.g. `run_hierscpred.sh`), we can launch 
an array job iterating for each pool name


```bash
qsub -t 1-75 bin/run_hierscpred.sh
# -t Vector of length equal to the number of pools (.RDS files)
```

