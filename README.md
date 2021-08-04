
## Load data into R

***

There are multiple ways to input your data into R. In this case, we will be working with the output Fold Change data from iDEP 8.1 Server with the correspondent PAs. Once you have your file in a folder, you will have to write the location of it between the " " on the following code.

***

## Generation of Expression profile

The RNA seq analysis of the samples was performed in EdgeR and iDEP 8.1. Reads counts data were uploaded to IDEP.93. In the pre-processing of the read counts, setting were left predetermined. The transform counts data for clustering & PCA was EdgeR: log2(CPM+c), with a pseudo count c: 4. No missing values were observed in the reads counts. In total - genes in - samples. - genes were kept in the data using original IDs and - were converted to Ensemble genes ID from the iDEP database. The processed data was uploaded to Studio. 

Plot of differentially expressed genes 

In RStudio packages used were ‘dplyr’. 

A function was created as follows: 

``` {r, GEP, echo = FALSE}

create_table <- function(x,y){
    p <- w[,x:y]
    
    p1 <- sum(p[,2] <= 0.05 & p[,1] >= 1.00 & p[,1] <= 2.00)
    p2 <-sum(p[,2]<= 0.05 & p[,1] > 2.00  & p[,1] <= 3.00)
    p3 <-sum(p[,2] <= 0.05 & p[,1] > 3.00  & p[,1] <= 4.00)
    p4 <-sum(p[,2] <= 0.05 & p[,1] > 4.00  & p[,1] <= 5.00)
    p5 <-sum(p[,2] <= 0.05 & p[,1] > 5.00)
    p11 <- p1+p2+p3+p4+p5
    
    p6 <- sum(p[,2] <= 0.05 & p[,1] <= (-1) & p[,1] >= (-2))
    p7 <-sum(p[,2]<= 0.05 & p[,1] < (-2) & p[,1] >= (-3))
    p8 <-sum(p[,2] <= 0.05 & p[,1] < (-3) & p[,1] >= (-4))
    p9 <-sum(p[,2] <= 0.05 & p[,1] < (-4) & p[,1] >= (-5))
    p10 <-sum(p[,2] <= 0.05 & p[,1] < (-5))
    p12 <- p6+p7+p8+p9+p10
    
    c <- list(c(p1,p2,p3,p4,p5,p11,p6,p7,p8,p9,p10,p12))
    
}

#From the iDEP generated file, the 4 comparisons were listed in different columns. This function extracts two columns, log2FoldChange and P-adjusted value. Only values p <= 0.05 were obtained, and were sorted as listed. 

```





