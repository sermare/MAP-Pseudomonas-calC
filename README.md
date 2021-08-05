# CalC Work

***

The different sections contain the code used in R language to generate the list of stastically significant genes, as well to generate the enriched profiles on the experiments listed below on Pseudomonas aeruginosa WT and Pseudomonas aeruginosa ΔcalC mutant (https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4410972/):

5 mM Ca2+ WT vs 0 mM Ca2+ WT

5 mM Ca2+ ΔcalC vs 0 mM Ca2+ ΔcalC

5 mM Ca2+ WT vs 5 mM Ca2+ ΔcalC

0 mM Ca2+ WT vs 0 mM Ca2+ ΔcalC


***


## Load data into R

***

There are multiple ways to input your data into R. In this case, we will be working with the output Fold Change data from iDEP 8.1 Server with the correspondent PAs. Once you have your file in a folder, you will have to write the location of it between the " " on the following code.

```{r}

VolData<- read.csv("~/File)

```

***

## Installing packages in R

Thoughout these codes, there are external packages to be imported into use in the functions

to install the package:

```{r}
install.packages("package")
```

then import it

```{r}
library(package)

```

***

## Generation of Expression profile

The RNA seq analysis of the samples was performed in EdgeR and iDEP 8.1. Reads counts data were uploaded to IDEP.93. In the pre-processing of the read counts, setting were left predetermined. The transform counts data for clustering & PCA was EdgeR: log2(CPM+c), with a pseudo count c: 4. No missing values were observed in the reads counts. In total - genes in - samples. - genes were kept in the data using original IDs and - were converted to Ensemble genes ID from the iDEP database. The processed data was uploaded to Studio. 

### Plot of differential expressed genes 

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

From the iDEP generated file, the 4 comparisons were listed in different columns. This function extracts two columns, log2FoldChange and P-adjusted value. Only values p <= 0.05 were obtained, and were sorted as listed. 

    
}

```

***

## Vector of Statistically Significant genes 

### A vector of  statistically significant (p-adj <= 0.05) genes was generated using the code listed below:

```{r, Sig-funct}

 create_spreadsheet<- function(x,y){
  p <- VolData[,x:y]

  p <- p %>% 
    filter(p[,2] <= 0.05)
  
  p <- p %>% 
    filter(p[,1] >= 1.00 | p[,1] <= -1.00)
  
  p$diffexpressed <- "NO"
  p$diffexpressed[p[,1] >= 1.00 & p[,2] <= 0.05] <- "Up"
  p$diffexpressed[p[,1] <= (-1.00) & p[,2] <= 0.05] <- "Down"
  
  p %>% arrange(desc(p[,1]))
  
  p
  
}

genes_sheet <- function(x,y){
  w <- create_spreadsheet(x,y)
  w <- left_join(w, VolData, by = colnames(w[1]))
  w <- w[,1:4]
  w <- unique(w)
}


Description of code: 
This function utilizes the same sheet generated by iDEP8.1. It first extracts all genes (p-adj <=0.05) whose log2FC is over 1.00 and below -1.00. Then, it generates a descriptive column with the change being “Up”, “Down” or “NO” for no change. No values with NO should be listed in the vector. 

The second function of the code integrates the first function, and the result is a list of genes. The list gets the corresponding p-adj and log2FC from the original sheet. 
```
***

## Enrichment pathways generation 

###Generation of KEGG Pathways

In R, we utilized the ‘goseq’, ‘clusterProfiler’, and ‘’ packages. The Pseudomonas aeruginosa utilized was extracted from the KEGG organism, listed in hotel://www.genomes.jp/keggg/catalog/org_list.html. The other R package, cluster profiler package, utilizes statistical analysis and visualization of functional profiles for genes and gene clusters. The package is designed to perform Over Representation Analysis (ORA) to compare functional profiles of various conditions on one level. An ORA (Boyle et. Al 2004) determines whenever known biological functions or processes are over-represented (=enrched) in an experimentally-derived gene list (more detail http://yulab-smu.top/clusterProfiler-book/chapter2.html)

After a list of differentially expressed genes was generated, these were included in the KEGG Enrichment analysis. Given a vector of genes, this function returns the enrichment KEGG categories with FDR control. 

An example of a result is shown below: 

Files can be downloaded here:

### Generation of GO Pathways

 

The vector of statistically generated genes is uploaded to http://geneontology.org/.  
The PANTHER classification system annotates the genes and generates a list of pathways with gene count, a p-adj, and GO reference IDs.

Files can be downloaded here:


***

## Diagrams of genes in the role of Ca2+ regulation

In R, the packages ‘GOplot’ will be needed. 

```{r}

down_or_not <- function(x,y){
  p <- VolData[,x:y]

  p <- p %>% 
    filter(p[,2] <= 0.05)
  
  p <- p %>% 
    filter(p[,1] < 1.00)
  
  p$diffexpressed <- "NO"
  p$diffexpressed[p[,1] <= (-1.00) & p[,2] <= 0.05] <- "Down"
  
  p %>% arrange(desc(p[,1]))
  
  p
  
}

vector_generates <-function(x,y){
  w <- down_or_not(x,y)
  w <- left_join(w,VolData, by = colnames(w[1]))
  w <- w[,1:4]
  w <- unique(w)
  
}


up_WT <- function(x,y){
  p <- VolData[,x:y]

  p <- p %>% 
    filter(p[,2] <= 0.05)
  
  p <- p %>% 
    filter(p[,1] >= 1.00)
  
  p$diffexpressed <- "UP"
  
  p %>% arrange(desc(p[,1]))
  
  p
  
}

vector_generates_upWT <-function(x,y){
  w <- up_WT(x,y)
  w <- left_join(w,VolData, by = colnames(w[1]))
  w <- w[,1:4]
  w <- unique(w)
  
}

upregulated_WT <-vector_generates_upWT(8,9)#up regulated genes in 5v0 WT
No_Down_5cv0c <- vector_generates(4,5) # down or not regualted in 5cv0c

V1 <- upregulated_WT[,3:4]
V1 <- V1[,c("Row.names","diffexpressed")]
V2 <- No_Down_5cv0c[,3:4]
V2 <- V2[,c("Row.names","diffexpressed")]

colnames(V2) <- c("Genes","Expressed")  
colnames(V1) <- c("Genes","Expressed")  

write.csv(V1,"~/Desktop/2021/comparison1.csv")
write.csv(V2,"~/Desktop/2021/comparison2.csv")

GOVenn(V2,V1, label= c("Down or Not regulated genes in calC","Up-regualted genes in WT"),plot = F)
```

The following functions will be needed to generate the lists used for the Venn diagrams.


The R function will return three tables containing unique values found, and then a third table with the common genes found in both lists. 

The numbers can be then used in the generation of a Venn diagram. 

Diagrams were generated with: http://bioinformatics.psb.ugent.be/webtools/Venn/

### Up-Regulated Genes in WT vs Down/No in calC
 


The Venn diagram above shows the unique genes for each experiment 2402 genes for 5 mM v 0 mM calC and 349 genes for 5 mM v 0 mM WT. The lists have 324 genes present in both experiments.

File with numbers:  

### Down-Regulated Genes in WT vs Up/No in calC


The Venn diagram above shows the unique genes for 2171 genes for 5v0 calC and 299 genes for 5v0 WT. The lists have 392 genes present in both experiments. 


File with numbers:  






