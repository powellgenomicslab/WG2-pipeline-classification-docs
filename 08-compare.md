# Compare classifications

If two methods are used to classify cells (e.g. *azimuth* and *hierarchical scPred*), 
we can plot a contingeny table showing the concordance by cell type between the two methods.

Let's create an output directory


```bash
mkdir step5_compare
```

And check the input arguments


```bash
singularity exec -B $PWD cell_classification.sif \  
  Rscript /compare.R --help
```

```
Usage: compare.R [options]


Options:
        --file=CHARACTER
                RDS object file name

        --xaxis=CHARACTER
                Column in metadata

        --yaxis=CHARACTER
                Column in metadata

        --sort=CHARACTER
                Sort labels in both axes to match cell type hierarchy?

        --out=CHARACTER
                Output file name [default= comp]

        --path=CHARACTER
                Output path to store results [default= .]

        -h, --help
                Show this help message and exit
```



Let's assume we have a directory called `step4_reduce` a single `.RDS` file 
with cells classified with azimuth and hierarchical scPred. We can generate
a heatmap showing the counts and proportion of cell type concordance between the
two methods as well as a text file with the numeric results as follows:


```bash
singularity exec -B $PWD cell_classification.sif \ 
  Rscript /compare.R \
  --file step4_reduce/query.RDS \
  --out comparison \
  --path step5_compare
```

`compare.R` will output two heatmap plots corresponding to counts and proportion 
of cell type concordance between the two methods stored in the `step5_compare` 
directory.


