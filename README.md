## About ComBat-Seq

ComBat-Seq is a batch effect adjustment (BEA) tool for RNA-seq count data. It is an improved model based on the popular ComBat[1], to address its limitations through novel methods designed specifically for RNA-Seq studies. ComBat-Seq takes **untransformed, raw count matrix** as input. We use a negative binomial regression to model and address batch effects. Then it provides adjusted data by mapping the original data to an expected distribution if there were no batch effects. The adjusted data preserve the integer nature of count matrix. Like ComBat, it requires known a batch variable.

This approach better captures the properties of RNA-Seq count data compared to the Gaussian distribution assumed by ComBat. ComBat-Seq specifies different dispersion parameters across batches, allowing for flexible modeling of the variance of gene expression. In addition, ComBat-Seq provides adjusted data which preserves the integer nature of counts, so that the adjusted data are compatible with the assumptions of state-of-the-art differential expression software (e.g. edgeR, DESeq2, which specifically request untransformed count data). 

## Installation

This repository is **not a package** for ComBat-Seq. It stores files to reproduce the results in our manuscript.

We are working to make ComBat-Seq easily and publically available. Currently it is included in an improved version of sva[2,3] [which I work on](https://github.com/zhangyuqing/sva-devel). To use ComBat-Seq, download the sva package from my Github:

```r
# install.packages("devtools")
devtools::install_github("zhangyuqing/sva-devel")
```

## Usage

Basic usage (users need to input at least two parameters - a raw count matrix from RNA-Seq studies, without any normalization or transformation, and a vector for batch separation):

```r
count_matrix <- matrix(rnbinom(400, size=10, prob=0.1), nrow=50, ncol=8)
batch <- c(rep(1, 4), rep(2, 4))

adjusted <- ComBat_seq(count_matrix, batch=batch, group=NULL)
```
  
In ComBat-Seq, user may specify biological covariates, whose signals will be preserved in the adjusted data. If the user would like to specify one biological variable, they may use the `group` parameter:

```r
group <- rep(c(0,1), 4)
adjusted_counts <- ComBat_seq(count_matrix, batch=batch, group=group)
```
  
If users wish to specify multiple biological variables, they may pass them as a matrix or data frame to the `covar_mod` parameter:

```r
cov1 <- rep(c(0,1), 4)
cov2 <- c(0,0,1,1,0,0,1,1)
covar_mat <- cbind(cov1, cov2)
adjusted_counts <- ComBat_seq(count_matrix, batch=batch, group=NULL, covar_mod=covar_mat)
```

## Citation
Please cite:

> Zhang, Y., Parmigiani, G., & Johnson, W. E. (2020). ComBat-Seq: batch effect adjustment for RNA-Seq count data. *bioRxiv*, 904730.


## File descriptions

### Simulation

+ **sim_DEpipe.R, sim_DEpipe_helpers.R**
    + Pipeline and helper functions for simulations. Run sim_DEpipe.R to produce the simulation results. 
    + Usage: ``Rscript sim_DEpipe.R <mean batch effect> <dispersion batch effect> <total number of samples>``
    + True and false positive rates will be stored in CSV files. 
    + Modify the parameters to change the study design, level of biological signal, sequencing depth, etc.
+ **qsub_simDEpipe.py**
    + Script to run (qsub to cluster) multiple experiments
+ **visualize.R, visualize_helpers.R**
    + Script and helper functions to visualize the simulation results. Run visualize.R to generate the plot based on the CSV result files. 
    + Change the paths to files if necessary.

### Real data application

+ **gfrn_application.R, gfrn_DE.R, gfrn_helpers.R**
    + Script and helper functions for application example on the GFRN signature dataset. Run gfrn_application.R for the PCA analysis. Run gfrn_DE.R for differential expression analysis.
    + Change the paths to files at the top of the script, if necessary.
+ **signature_data.rds**
    + RDS object for the cleaned signature dataset, published[4] and used in our previous work[5,6].
+ **ras-pathway-gene-names.csv**
    + genes in RAS signaling pathway, obtained from [NCI website](https://www.cancer.gov/research/key-initiatives/ras/ras-central/blog/2015/ras-pathway-v2).

## References
1. Johnson, W. E., Li, C., & Rabinovic, A. (2007). Adjusting batch effects in microarray expression data using empirical Bayes methods. *Biostatistics*, 8(1), 118-127.
2. Leek, J. T., Johnson, W. E., Parker, H. S., Jaffe, A. E., & Storey, J. D. (2012). The sva package for removing batch effects and other unwanted variation in high-throughput experiments. *Bioinformatics*, 28(6), 882-883.
3. Leek JT, Johnson WE, Parker HS, Fertig EJ, Jaffe AE, Storey JD, Zhang Y, Torres LC (2019). *sva: Surrogate Variable Analysis*. R package version 3.34.0.
4. Rahman, M., MacNeil, S. M., Jenkins, D. F., Shrestha, G., Wyatt, S. R., McQuerry, J. A., ... & Bild, A. H. (2017). Activity of distinct growth factor receptor network components in breast tumors uncovers two biologically relevant subtypes. *Genome medicine*, 9(1), 40.
5. McQuerry, J. A., Jenkins, D. F., Yost, S. E., Zhang, Y., Schmolze, D., Johnson, W. E., ... & Bild, A. H. (2019). Pathway activity profiling of growth factor receptor network and stemness pathways differentiates metaplastic breast cancer histological subtypes. *BMC cancer*, 19(1), 881.
6. Zhang, Y., Jenkins, D. F., Manimaran, S., & Johnson, W. E. (2018). Alternative empirical Bayes models for adjusting for batch effects in genomic studies. *BMC bioinformatics*, 19(1), 262.
