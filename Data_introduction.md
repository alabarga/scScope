Here, we give R code to generate the simulated data used in our readme demonstration.

## Dataset 1: 2k cells with 5 clusters
Dataset was generated by Splatter (https://github.com/Oshlack/splatter-paper) R packages by setting

- cell number = 2,000
- gene number = 500
- cell type   = 5
- drop out    = 4 (sparsity level > 60%)

Output files:

- counts_1.csv: gene expressions
- cellinfo_1.csv: identities information for cells

## Dataset 2: 5k cells with 10 clusters
Dataset was generated by Splatter R packages by setting

- cell number = 5,000
- gene number = 500
- cell type   = 10
- drop out    = 4 (sparsity level > 60%)

Output files:

- counts_2.csv: gene expressions
- cellinfo_2.csv: identities information for cells


## Code to generate simulated datasets
We made use of below code for simulating scRNA-seq data in R. The code was modified from https://github.com/theislab/dca/

```R
library(splatter)

simulate <- function(nGroups=1, nGenes=1000, batchCells=2000, dropout=3)
{
  if (nGroups > 1) method <- 'groups'
  else             method <- 'single'
  
  group.prob <- rep(1, nGroups) / nGroups
  
  # new splatter requires dropout.type
  if ('dropout.type' %in% slotNames(newSplatParams())) {
    if (dropout)
      dropout.type <- 'experiment'
    else
      dropout.type <- 'none'
    
    sim <- splatSimulate(group.prob=group.prob, nGenes=nGenes, batchCells=batchCells,
                         dropout.type=dropout.type, method=method,
                         seed=0, dropout.shape=-1, dropout.mid=dropout)
    
  } else {
    sim <- splatSimulate(group.prob=group.prob, nGenes=nGenes, batchCells=batchCells,
                         dropout.present=!dropout, method=method,
                         seed=0, dropout.shape=-1, dropout.mid=dropout)        
  }
  
  counts     <- as.data.frame(t(counts(sim)))
  truecounts <- as.data.frame(t(assays(sim)$TrueCounts))
  
  dropout    <- assays(sim)$Dropout
  mode(dropout) <- 'integer'
  
  cellinfo   <- as.data.frame(colData(sim))
  geneinfo   <- as.data.frame(rowData(sim))
  
  list(counts=counts,
       cellinfo=cellinfo,
       geneinfo=geneinfo,
       truecounts=truecounts)
}

cell_num<-2000
drop_out<-4
group<-5
sim <- simulate(nGroups=group,batchCells=cell_num,dropout = drop_out)

counts <- sim$counts
geneinfo <- sim$geneinfo
cellinfo <- sim$cellinfo
truecounts <- sim$truecounts

write.csv(counts, file =paste("./counts.csv",sep="") )
write.csv(cellinfo, file =paste( "./cellinfo.csv",sep=""))
write.csv(geneinfo, file = paste("./geneinf.csv",sep=""))
```
