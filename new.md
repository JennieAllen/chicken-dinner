---
title: "Detecting Missing Genomes from MLST BlastN"
author: "Jennie Allen"
date: "August 30, 2016"
output: html_document
---
# Blastn search of MLST Housekeeping Genes
##  Genes used as query:
abcZ, adk, aroE, fumC, gdh, pdhC, pgm

## Genomes used in database
81 complete RefSeq Neisseria genomes present in NCBI in August 2016

##  Blastn command using parameters set from BIGSdb platform
blastn -db Neisseria\_august\_2016.fna -word\_size 15 -evalue 0.001 -perc\_identity 70 -query FA1090\_MLST\_gene\_fragment.fa

Housekeeping Gene  | Hits from each blastn search against database 
------------- | ------------- 
abcZ    | 172        
adk        | 81
aroE    | 80        
fumC        | 84
gdh    | 81        
pdhC        | 81
pgm| 81        


    
##  Issue Concerning Genomes Not Captured

I used 81 genomes in database, 2 of which had plasmids, MS11 and NCCP11945
I then inputted the blastn results for each housekeeping gene into an excel spreadsheet, then read that into Rstudio.
I wrote the following R script to search each spreadsheet of blastn results for each genome in the database, then outputted
a dataframe with each housekeeping gene and it's corresponding missing genome.
    


```
##This script should compare to make sure my housekeeping genes are present in every Neisseria genome
##  Prior run Neisseria_list <- read.xlsx("Neisseria_genomes.xlsx",sheetIndex = 1,header = FALSE,stringsAsFactors=FALSE)
## abcZ_blastn <- read.xlsx("abcZ_august_2016.xlsx",sheetIndex = 1,header = TRUE,stringsAsFactors=FALSE)

## Finally write results with:
##             August_2016_missing_MLST_Neisseria<- rbind(missing_abcZ,missing_adk,missing_aroE,missing_fumC,missing_gdh,missing_pdhC,missing_pgm)
##             write.xlsx(August_2016_missing_MLST_Neisseria,"BlastN_Missing_Genomes_MLST_Neisseria_8_2016.xlsx",sheetName = "Sheet1",col.names = TRUE,row.names = FALSE)


## this function detects missing genes, prints to screen, then writes to a dataframe
housekeeping_gene_missing_genome <-function(genome_data,blastn_data){
     housekeeping_gene <- colnames(blastn_data[1])
     Missing_blastN_genes <- data.frame(housekeeping_gene=character(),missing_gene=character(),stringsAsFactors = FALSE)
     missing <- data.frame(housekeeping_gene=character(),missing_gene=character(),stringsAsFactors = FALSE)
     for(i in 1:nrow(genome_data)){
          check<-0
          query <- genome_data[i,2]
          for(j in 1:nrow(blastn_data)){
               if(query == blastn_data[j,2]){
                    check<-1
               }
               if(j == nrow(blastn_data) && check == 0){
                    if(query=="NC_011034.1"){
                         query <- paste("NC_011034.1","which is a","plasmid",sep = " ")
                    }
                    else if(query=="NC_022243.1"){
                         query <- paste("NC_022243.1","which is a","plasmid",sep =" " )
                    }
               missing[1,1]<- housekeeping_gene
               missing[1,2] <- query
               Missing_blastN_genes<-rbind(Missing_blastN_genes,missing)
               }
          }
     }
```

##  Results

   
   
   Housekeeping Gene  | Missing Genome 
------------- | ------------- 
aroE      | LZ\_LT571436.1        
gdh        | NZ\_CP006869.1                

