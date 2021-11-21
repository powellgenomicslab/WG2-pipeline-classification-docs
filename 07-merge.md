# Merge data

If cell type classification is performed by experimental units (e.g. batches), 
the data can be merged into a single Seurat object using the `reduce.R` script.

Let's create an output directory to store the results.


```bash
mkdir step4_reduce
```

And check the input arguments


```bash
singularity exec -B $PWD cell_classification.sif \  
  Rscript reduce.R --help
```


Let's assume we have a directory called `step3_hierscpred` containing 
multiple `.RDS` files corresponding to different batches. We can 
merge all Seurat objects follows:


```bash
singularity exec -B $PWD cell_classification.sif \ 
            Rscript reduce.R \
            --file step3_hierscpred \
            --out reduced_data \
            --path step4_reduce
```

`reduce.R` will output a single Seurat object called `reduced_data.RDS` within the 
`step4_reduce` directory

