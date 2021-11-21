# Split data

For data integration and cell type classification purposes, it is recommended to
split the data into smaller subsets that reflects a single homogeneous experimental 
unit. In the context of cell type classification we define an *experimental unit* as
the group of cells sharing similar biological and technical variance components. 
A batch (e.g pool/well from a single cell experiment) represents the most common
experimental unit.

Therefore, we recommend splitting the data for:

1. *Faster classification/integration*. Splitting the data avoids extensive search
for cell anchors and neighbors in the azimuth approach
2. *Parallelization*. Each experimental unit can be processed and analyzed independently 
using multiple CPU workers
3. *Avoiding intra-batch effects in the query dataset*. Cell differences driven by
batch effects within the query dataset may influence the performance of the integration/alignment.

We recommend splitting the data by experimental units (e.g. batches). If the Seurat 
query object represents a single experimental unit, no splitting is necessary and
this section can be skipped.

To split the data, we run the following code. First, let's create an output directory


```bash
mkdir step1_split
```

Let's assume we have a Seurat RDS object called `query.RDS` with a metadata column name
called `pool` containing the values `1` and `2` corresponding to two independent pools. We can split the data  by pool using the `split.R` module.

Let's examine the parameter information:


```bash
singularity exec -B $PWD cell_classification.sif \ 
  Rscript split.R --help
```


```
Usage: split.R [options]


Options:
        --file=CHARACTER
                RDS object file name

        --batch=CHARACTER
                Batch column to split the data

        --out=CHARACTER
                Output file name [default= split_object]

        --path=CHARACTER
                Output path to store results [default= .]

        -h, --help
                Show this help message and exit

```

We can now split the data as follows


```bash
singularity exec -B $PWD cell_classification.sif \
  Rscript split.R \
  --file query.RDS \
  --batch pool \
  --out query \
  --path step1_split
```


**`split_object.R`** takes two main arguments. `--file` corresponds to the seurat object 
filename in `.RDS` format. `--batch` expects a string corresponding to a column name 
in the `@metadata` slot. A group of RDS files following the *{out}_{batch levels}.RDS* 
filename pattern are created. In this example, two files called `query_1.RDS` and `query_2.RDS` 
are stored in the `step1_split` directory.

> We strongly recommend storing results in different directories to avoid file
> overwriting. 
