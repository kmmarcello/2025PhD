---
title: project outline

---

# project outline

[pangenome](https://hackmd.io/UMFoektHTomRf-CHoF9oVQ?both#Snakemake-TEST-)

**note:** change it into separating by language type: R, Bash, Python, etc.
:::info
**local**
1. GTDB found cyanobacteria with this criteria: ![Screenshot 2024-04-04 at 10.09.13 AM](https://hackmd.io/_uploads/SyapXD2kC.png)
 -> 1185 genomes total
2. downloaded the genomes and .csv file with the criteria to local
3. Opened the .sh folder provided into BBEdit (contains all lines of code to use NCBI datasets to download the genomes) `CTL + a` `CTL + c` 
:::

:::success
**FARM** \
4. ~~`CTL + v` into FARM cyanobacteria/ncbi_datasets `ENTER`~~ \
  `CTL + v` into FARM cyanobacteria/April_04_2024_ncbi_datasets `ENTER` \
5. this is where the info is coming [here](https://hackmd.io/SKhz08m-SBOzyE4ksgw1Bw?both#May-15-2023-Monday) \
6. pull out actual genomes with for loop
```
#!/bin/bash

for i in *.zip; do
    mkdir "$i-dir"
    cd "$i-dir"
    unzip "../$i"
    for j in *; do
        mv "$j" "$i.${j##*.}"
    done
    cd ..
done
```
7. `mkdir zipped` 
8. `mkdir unzipped`
9. move files to **zipped** `mv *.zip-dir unzipped`
10. `mkdir cyano_fna`
11. `mv cyano_fna unzipped`
12. `find . -maxdepth 5 -name '*.fna' -exec mv {} ./cyano_fna/ \;` 614 genomes moved to **cyano_fna**
13. `mv unzipped/cyano_fna/ .` moved to ~/cyanobacteria/ncbi_datasets
14. move Snakefile, hmms, and create file_names.txt to directory
15. edit Snakefile to match directories
16. `snakemake -n` check
17. `snakemake`
18. `srun -p bmm -J anvio -t 5:00:00 --mem=32G --pty bash`
19. `conda activate anvio-7`
20. deleted these bc they were not good ssequences 

| Column 1 | 
| -------- | 
| **file_names.txt:** GCA_018266275.1_ASM1826627v1_genomic \
 **.csv:** GCA_018266275.1	Cyanobacteria bacterium SZAS LIN-5	d__Bacteria;p__Cyanobacteria;c__;o__;f__;g__;s__	d__Bacteria;p__Cyanobacteriota;c__Vampirovibrionia;o__Obscuribacterales;f__Obscuribacteraceae;g__PALSA-1081;s__PALSA-1081 sp003963305	not type material	97.25	0.85	50.25812208	6381864	GCA_018266275.1	activated sludge	0	China: Shenzhen	temperate	single	waste water	Cyanobacteria_bacterium_SZAS_LIN_5     | 

21. after deleting each one `touch /home/kmrcello/cyanobacteria/ncbi_datasets/data/*` the data and rerun `snakemake`: snakemake file will get cold tolerance genes. need to get Bacteria 71, photo genes, and TCS genes.
22. run photo_hmms and membrane_hmms

**photo_hmms**
`scp -r ./photo_hmms kmrcello@farm.cse.ucdavis.edu:/home/kmrcello/cyanobacteria/ncbi_datasets`
`srun -p bmm -J memg -t 40:00:00 --mem=32G --pty bash`
`conda activate anvio-7`
```
#!/bin/bash

for i in `ls *db | awk 'BEGIN{FS=".db"}{print $1}'`
do
    anvi-run-hmms -c $i.db -H /home/kmrcello/cyanobacteria/ncbi_datasets/photo_hmms
done
```

**new stringency for noise cut off**
```
#!/bin/bash

for i in `ls *db | awk 'BEGIN{FS=".db"}{print $1}'`
do
    anvi-run-hmms -c $i.db -H /home/kmrcello/cyanobacteria/April_04_2024_ncbi_datasets/ctg_hmms
done
```
**membrane_hmms**
```
#!/bin/bash

for i in `ls *db | awk 'BEGIN{FS=".db"}{print $1}'`
do
    anvi-run-hmms -c $i.db -H /home/kmrcello/cyanobacteria/ncbi_datasets/membrane_hmms
done
```

**bacteria_71**
```
#!/bin/bash

for i in `ls *db | awk 'BEGIN{FS=".db"}{print $1}'`
do
    anvi-run-hmms -c $i.db -I Bacteria_71
done
```
23. make directores for gene categories

```
for object in "${object_list[@]}"; do
    mkdir gene_fastas/aa_cold/${object}-fastas
done

for object in "${photo_genes[@]}"; do
    mkdir gene_fastas/aa_photo/${object}-fastas
done

for object in "${membrane_genes[@]}"; do
    mkdir gene_fastas/aa_membrane/${object}-fastas
done
```

24. Get sequences for each set of genes:
```
#!/bin/bash


```
**example**

```
# Loop through each object
for object in "${object_list[@]}"; do
    echo "Processing object: $object"
    
    # Loop through each .db file
    for file in *.db; do
        echo "Processing file: $file"
        
        # Perform some action for each object and file combination
        # For example, here's a placeholder command to show how you might use 'object' and 'file':
        # echo "Processing object $object with file $file"
        # You can replace this echo command with your specific processing command
        # For example, run a command using 'object' and 'file':
        # some_command --object="$object" --file="$file" > "${object}_${file}_output.txt"
    done
done
```

**real deal**
```
# List of objects to process
# this list of objects is actually the cold tolerance genes
object_list=("COG2609.faa.final_tree.fa" "COG0508.faa.final_tree.fa" "COG1278.faa.final_tree.fa" "COG0513.faa.final_tree.fa" "COG3239.faa.final_tree.fa" "COG0593.faa.final_tree.fa" "COG0484.faa.final_tree.fa" "COG0443.faa.final_tree.fa" "COG0188.faa.final_tree.fa" "COG0776.faa.final_tree.fa" "COG0361.faa.final_tree.fa" "COG0532.faa.final_tree.fa" "COG0290.faa.final_tree.fa" "COG0195.faa.final_tree.fa" "COG0380.faa.final_tree.fa" "COG1185.faa.final_tree.fa" "COG0858.faa.final_tree.fa" "COG0468.faa.final_tree.fa" "COG0557.faa.final_tree.fa" "COG0544.faa.final_tree.fa" "COG1115.faa.final_tree.fa")
# Loop through each object
for object in "${object_list[@]}"; do
    echo "Processing object: $object"
    
    # Loop through each .db file
    for file in *.db; do
        anvi-get-sequences-for-hmm-hits -c $file \
                                --hmm-source ctg_hmms \
                                --gene-names $object \
                                --get-aa-sequences \
                                -o ../outputs/aa_hit_reports/${object}-${file}.fa;
    done
done

photo_genes=("COG1143.faa.final_tree.fa" "1G6I8.faa.final_tree.fa" "1G1ET.faa.final_tree.fa" "1FZXJ.faa.final_tree.fa" "2ZBN6.faa.final_tree.fa" "3313D.faa.final_tree.fa" "2Z87P.faa.final_tree.fa" "1G08A.faa.final_tree.fa" "2Z8JK.faa.final_tree.fa" "2Z7TN.faa.final_tree.fa" "2Z7VA.faa.final_tree.fa" "2Z7ZP.faa.final_tree.fa")
# Loop through each object
for object in "${photo_genes[@]}"; do
    echo "Processing object: $object"
    
    # Loop through each .db file
    for file in *.db; do
        anvi-get-sequences-for-hmm-hits -c $file \
                                --hmm-source photo_hmms \
                                --gene-names $object \
                                --get-aa-sequences \
                                -o ../outputs/aa_hit_reports/photo/${object}-${file}.fa;
    done
done

membrane_genes=("1TVTF.faa.final_tree.fa" "COG4585.faa.final_tree.fa" "COG3239.faa.final_tree.fa" "1G096.faa.final_tree.fa" "1FZVK.faa.final_tree.fa" "COG1398.faa.final_tree.fa" "1G100.faa.final_tree.fa" "1G2GY.faa.final_tree.fa" "COG5002.faa.final_tree.fa" "1FZWA.faa.final_tree.fa" "1G0YA.faa.final_tree.fa")
# Loop through each object
for object in "${membrane_genes[@]}"; do
    echo "Processing object: $object"
    
    # Loop through each .db file
    for file in *.db; do
        anvi-get-sequences-for-hmm-hits -c $file \
                                --hmm-source membrane_hmms \
                                --gene-names $object \
                                --get-aa-sequences \
                                -o ../outputs/aa_hit_reports/mem/${object}-${file}.fa;
    done
done
```



* note on how to look at these lists again:
```
# Access individual elements by index
echo "${cold_genes[0]}"  # Outputs the first element
echo "${cold_genes[1]}"  # Outputs the second element

# Print all elements using a loop
for gene in "${cold_genes[@]}"; do
    echo "$gene"
done

# Verify the entire array content
echo "${cold_genes[@]}"
```

25. change names (this is hard bc the names are different than they were the last time I did this)
```
# Change the name of each .fa file to remove the .db in the middle. 
for i in *.fa; do 
	    [ -f "$i" ] || continue
	    mv "$i" "${i//.db/}"
	done
```
vvv April 8, 2024 vvv this was specifically bc I didnt realixe that these were nucleotide sequences that I had been generating the whole time000
```
for i in *.txt; do 
    [ -f "$i" ] || continue
    new_name="${i//.photo.sequence.txt/}.fa"
    mv "$i" "$new_name"
done
```
```
# ChatGPT version based on version above for each individual directory 
for object_dir in *-fastas; do
    if [ -d "$object_dir" ]; then  # Check if it's a directory
        cd "$object_dir" || continue  # Move into the directory

        for file in *.fa; do
            [ -f "$file" ] || continue  # Check if it's a file
            mv "$file" "${file//.db}"    # Rename the file, removing '.db' extension
        done

        cd ..  # Move back to the parent directory
    fi
done
```

26. change the heading for each fasta in each .fa file
```
# original 
for f in *.fa; do 
    sed -i "s/^>/>${f}_/" "$f"; 
done
```
```
# loop based on original that changes the heading in each fasta to the file name
for object_dir in *-fastas; do
    if [ -d "$object_dir" ]; then  # Check if it's a directory
        cd "$object_dir" || continue  # Move into the directory

		for file in *.fa; do
		    filename=$(basename "$file")
		    sed -i "s/^>.*/>${filename} /" "$file"
		done
        
        cd ..  # Move back to the parent directory
    fi
done
    
```


27. concatenate the fastas from each genome into gene.fa
```
cat *.fa > PsbA-nuc.fa
```
```
for directory in *-fastas; do
    if [ -d "$directory" ]; then
        cd "$directory" || continue
        
        # Get the base name without the '-fastas' part
        base_name=${directory%.fa-fastas}

        # Concatenate the desired files into a new combined file
        cat "$base_name"*.fa > "../$base_name.fa"

        cd ..
    fi
done
```

28. scp them to local
```
scp kmrcello@farm.cse.ucdavis.edu:/home/kmrcello/cyanobacteria/ncbi_datasets/outputs/db/gene_fastas/aa_membrane/{1FZVK.faa.final_tree.fa,1G100.faa.final_tree.fa,COG3239.faa.final_tree.fa,1FZWA.faa.final_tree.fa,1G2GY.faa.final_tree.fa,COG4585.faa.final_tree.fa,1G096.faa.final_tree.fa,1TVTF.faa.final_tree.fa,COG5002.faa.final_tree.fa,1G0YA.faa.final_tree.fa,COG1398.faa.final_tree.fa} .
```
```
scp kmrcello@farm.cse.ucdavis.edu:/home/kmrcello/cyanobacteria/ncbi_datasets/outputs/db/gene_fastas/aa_cold/COG1115.faa.final_tree.fa .
:::

29. switch over to python scripts to get all the gene characteristic data into csv files.
:::warning
## Jupyter Notebook: Python
### Importing Python Modules
```
import csv
from Bio import SeqIO
import os
from Bio.SeqUtils.ProtParam import ProteinAnalysis
from Bio.Seq import Seq
import pandas as pd
import matplotlib.pyplot as plt
```
### Defining Paths and Variables
```
km_path = "/Users/kaylahmarcello/Desktop/projects/ncbi_datasets121023/data/"
aliphatic_index = ["A", "V", "I", "L"]
acidic = ["D", "E"]

cold_genes=["COG2609.faa.final_tree.fa", "COG0508.faa.final_tree.fa", "COG1278.faa.final_tree.fa", "COG0513.faa.final_tree.fa", "COG3239.faa.final_tree.fa", "COG0593.faa.final_tree.fa", "COG0484.faa.final_tree.fa", "COG0443.faa.final_tree.fa", "COG0188.faa.final_tree.fa", "COG0776.faa.final_tree.fa", "COG0361.faa.final_tree.fa", "COG0532.faa.final_tree.fa", "COG0290.faa.final_tree.fa", "COG0195.faa.final_tree.fa", "COG0380.faa.final_tree.fa", "COG1185.faa.final_tree.fa", "COG0858.faa.final_tree.fa", "COG0468.faa.final_tree.fa", "COG0557.faa.final_tree.fa", "COG0544.faa.final_tree.fa", "COG1115.faa.final_tree.fa"]

membrane_genes = ["1TVTF.faa.final_tree.fa", "COG4585.faa.final_tree.fa", "COG3239.faa.final_tree.fa", "1G096.faa.final_tree.fa", "1FZVK.faa.final_tree.fa", "COG1398.faa.final_tree.fa", "1G100.faa.final_tree.fa", "1G2GY.faa.final_tree.fa", "COG5002.faa.final_tree.fa", "1FZWA.faa.final_tree.fa", "1G0YA.faa.final_tree.fa"]
    
photo_genes =["COG1143.faa.final_tree.fa", "1G6I8.faa.final_tree.fa", "1G1ET.faa.final_tree.fa", "1FZXJ.faa.final_tree.fa", "2ZBN6.faa.final_tree.fa", "3313D.faa.final_tree.fa", "2Z87P.faa.final_tree.fa", "1G08A.faa.final_tree.fa", "2Z8JK.faa.final_tree.fa", "2Z7TN.faa.final_tree.fa", "2Z7VA.faa.final_tree.fa", "2Z7ZP.faa.final_tree.fa"]
```
### Calculations
```

header = ['name', 'aa_proline_count', 'aa_glycine_count', 'aa_serine_count', 'aa_arginine_count', 'aa_lysine_count', 'aa_count', 
         'calc_aliphatic_percent_sum', 'calc_acidic_percentage_sum', 'calc_aromaticity', 'calc_flexibility', 'calc_flexibility_sum', 'calc_flexibility_avg', 
          'calc_gravy', 'sequence']

for file in photo_genes:
    output_filename = file.replace(".fa", ".csv")  # Replace .fa with .csv
    output_pathway = os.path.join(output_path, output_filename)  # Include the 'data' directory in the output path

    with open(os.path.join(km_path, file)) as handle, open(output_pathway, 'a', newline='') as output_file:
        writer = csv.writer(output_file)

        # Write header only if the file is empty (first time)
        if os.path.getsize(output_path) == 0:
            writer.writerow(header)

        for record in SeqIO.parse(handle, "fasta"):
            sequence = str(record.seq)
            if 'X' in sequence:  # Skip sequences containing 'X'
                continue

            x = ProteinAnalysis(sequence)  # Storing the sequence in a variable

            # Calculate amino acid counts and other parameters
            p_count = x.count_amino_acids()["P"]
            g_count = x.count_amino_acids()["G"]
            s_count = x.count_amino_acids()["S"]
            r_count = x.count_amino_acids()["R"]
            k_count = x.count_amino_acids()["K"]
            aa_count = len(sequence)
            if k_count > 0:
                r_k_ratio = r_count / k_count
            else:
                r_k_ratio = "NA"
            aliphatic_percent_sum = sum(x.get_amino_acids_percent()[aa] for aa in aliphatic_index)
            acidic_percentage_sum = sum(x.get_amino_acids_percent()[aa] for aa in acidic)
            aromaticity = x.aromaticity()
            flexibility = x.flexibility()
            flexibility_sum = sum(flexibility)
            flexibility_avg = flexibility_sum / len(flexibility)
            gravy = x.gravy()


            # Write the data row to the CSV file
            data = [record.id, p_count, g_count, s_count, r_count, k_count, aa_count, 
         aliphatic_percent_sum, acidic_percentage_sum, aromaticity, flexibility, flexibility_sum, flexibility_avg, 
          gravy, sequence]
            writer.writerow(data)

            
```
### Concatonate .csv's
```


# Directory where your CSV files are located
directory = output_path 

# Output file to store the concatenated data
output_file = "/Users/kaylahmarcello/Desktop/projects/could_this_be_the_last_one/combined_genomes/aa_photo/photo_combined_gene_data/concat_photo_gene_data.csv"

# Check if the output file already exists; if so, delete it
if os.path.exists(output_file):
    os.remove(output_file)

# List all CSV files in the directory
csv_files = [file for file in os.listdir(directory) if file.endswith('.csv')]

# Ensure the output CSV file has a header by writing the header row first
write_header = True

# Concatenate data from all CSV files
for file in csv_files:
    with open(os.path.join(directory, file), 'r') as csv_file:
        reader = csv.reader(csv_file)
        with open(output_file, 'a', newline='') as output:
            writer = csv.writer(output)
            # Write header if this is the first file
            if write_header:
                for row in reader:
                    writer.writerow(row)
                write_header = False
            else:
                # Skip header for subsequent files
                next(reader)
                for row in reader:
                    writer.writerow(row)
```
:::
**30. switch over to RStudio**

title: "NCBI_datasets_photo_121023"\
author: "Kaylah Marcello"\
date: '2023-02-28'\
output: \
  html_document: \
    keep_md: true

#### ChatGPT citation for all questions about my PhD https://chat.openai.com/share/d30a0aa9-8e2b-4370-8f5b-ad6f6a267bc8
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

# Figures of cyanobacteria genes from complete genomes and MAGs
## Load the Libraries
```{r message=FALSE}
library(tidyverse)
library(RColorBrewer)
library(paletteer)
library(janitor)
library(here)
library(skimr)
#library(viridis)
#library(shiny)
#library(shinydashboard)
library(devtools)
library(svglite)
#library(cluster)
#library(factoextra)
library(ggdendro)
library(grid)
library(gplots)
library(ggplot2)
library(graphics)
library(plotly)
library(ggfortify)
#install.packages("ggVennDiagram")
library(ggVennDiagram)
#install.packages("reshape2")
library(reshape2)
#install.packages("stringr")
library(stringr)
```

# Load desA amino acid substitution data
```{r}
df_photo <- read.csv("data/aa_photo/gene_data/photo_concatenated.csv")
df_cold <- read.csv("data/aa_cold/gene_data/cold_concatenated.csv")
df_membrane <- read.csv("data/aa_membrane/gene_data/membrane_concatenated.csv")
```

# overview of the data
```{r}
glimpse(df_cold)
```
```{r}
vec <- rep(c("photo"), 19615)
df_photo <- df_photo%>% 
  mutate(gene_type = vec)
df_photo
```
```{r}
vec <- rep(c("membrane"), 158995)
df_membrane <- df_membrane%>% 
  mutate(gene_type = vec)
df_membrane
```
```{r}
vec <- rep(c("cold"), 32716)
df_cold <- df_cold%>% 
  mutate(gene_type = vec)
df_cold
```

# clean calomn names
```{r}
df_photo <- clean_names(df_photo)
df_photo
df_cold <- clean_names(df_cold)
df_cold
df_membrane <- clean_names(df_membrane)
df_membrane
```

# Join/merge these df's
```{r}
df_bind <- bind_rows(df_cold, df_photo, df_membrane)
df_bind

```


```{r}
df_bind <- df_bind %>% 
  separate(name, into = c("gene_name", "accession"), sep = "\\-")
df_bind
```

```{r}
df_bind <- df_bind %>% 
  separate(accession, into = c("accession"), sep = "\\_ASM")
df_bind
```

```{r}
df_bind <- df_bind %>% 
  separate(accession, into = c("accession"), sep = "\\_JGI") %>% 
  rename(gene_id=gene_name)
df_bind
```

```{r}
gene_id <- c("COG2609.faa.final_tree.fa", "COG0508.faa.final_tree.fa", "COG1278.faa.final_tree.fa", "COG0513.faa.final_tree.fa", "COG3239.faa.final_tree.fa", "COG0593.faa.final_tree.fa", "COG0484.faa.final_tree.fa", "COG0443.faa.final_tree.fa", "COG0188.faa.final_tree.fa", "COG0776.faa.final_tree.fa", "COG0361.faa.final_tree.fa", "COG0532.faa.final_tree.fa", "COG0290.faa.final_tree.fa", "COG0195.faa.final_tree.fa", "COG0380.faa.final_tree.fa", "COG1185.faa.final_tree.fa", "COG0858.faa.final_tree.fa", "COG0468.faa.final_tree.fa", "COG0557.faa.final_tree.fa", "COG0544.faa.final_tree.fa", "COG1115.faa.final_tree.fa","COG1143.faa.final_tree.fa", 
             "1G6I8.faa.final_tree.fa", "1G1ET.faa.final_tree.fa", "1FZXJ.faa.final_tree.fa", "2ZBN6.faa.final_tree.fa", "3313D.faa.final_tree.fa", "2Z87P.faa.final_tree.fa", "1G08A.faa.final_tree.fa", "2Z8JK.faa.final_tree.fa", "2Z7TN.faa.final_tree.fa", "2Z7VA.faa.final_tree.fa", "2Z7ZP.faa.final_tree.fa",
             "1TVTF.faa.final_tree.fa", "COG4585.faa.final_tree.fa", "COG3239.faa.final_tree.fa", "1G096.faa.final_tree.fa", "1FZVK.faa.final_tree.fa", "COG1398.faa.final_tree.fa", "1G100.faa.final_tree.fa", "1G2GY.faa.final_tree.fa", "COG5002.faa.final_tree.fa", "1FZWA.faa.final_tree.fa", "1G0YA.faa.final_tree.fa")

gene_name <- c("aceE", "aceF", "csp", "deaD", "desA", "dnaA", "dnaJ", "dnaK", "gyrA", "hupB", "infA", "infB", "infC", "nusA", "otsA", "pnp", "rbfA", "recA", "rnr", "tig", "yifA",
                "bac_PsaC", "cya_PsaC", "PsaA", "PsaB", "PsaD", "PsaE", "bac_PsbA", "cya_PsbA", "D2_PsbD", "PsbB", "PsbC", "PsbO",
                "desR", "desK", "desABCD", "desA_cyano", "desB_cyano", "desC", "desC_cyano", "desD_cyano", "hik33", "hik33_cyano", "rpaB")

dumb_df <- data.frame(gene_id = gene_id, gene_name = gene_name)
dumb_df
```

```{r}

df <- full_join(dumb_df, df_bind, by = "gene_id") 
  
df
```

```{r}
#table(df$gene_id)
```


```{r}
df_meta <- read.csv("data/gtdb-adv-search.csv")
```

```{r}
df_meta <- clean_names(df_meta)
df_meta
```

```{r}
df_complete <- full_join(df_meta, df, by = "accession")
df_complete
```

```{r}
df_complete <- df_complete %>%
  mutate(organism_tidy_name_copy = organism_tidy_name) %>%
  separate(organism_tidy_name, into = c("genus", "species", "strain", "strain1", "strain2"), sep = "_", convert = TRUE) %>% 
  rename(organism_tidy_name=organism_tidy_name_copy)
df_complete
```
## Looking at all the distinct values in temp_cat
```{r}
#glimpse(df_complete)

df_complete$temp_cat <- ifelse(df_complete$temp_cat == "col-freezing", "cold-freezing", df_complete$temp_cat)

distinct_temp_cat <- unique(df_complete$temp_cat)
distinct_temp_cat
```


## Perform ANOVA and Tukeys test
### This code changes temp_cat to factor instead of character, then takes my huge df and separates it into 44 dfs, one for each gene_name. These can be found in grouped_data. The ANOVA test can only be performed on grouped_data that has 2+ categories in temp_cat so there can actually be a comparison. 
```{r include=FALSE}
# List unique values before conversion
unique(df_complete$temp_cat)

# Convert temp_cat to factor with specified levels
df_complete$temp_cat <- factor(
  df_complete$temp_cat,
  levels = unique(df_complete$temp_cat)  # Use unique values as levels
)

# Check the updated temp_cat column
df_complete

```

```{r}
write_csv(df_complete, "data/df_complete_011024.csv")
```

```{r}
df_grouped <- df_complete %>%
  group_by(gene_name) %>%
  filter(!is.na(temp_cat)) %>%
  filter(!is.na(cell_type))
df_grouped
```


#### test
```{r}
# Set "temperate" as the reference level
df_grouped$temp_cat <- relevel(df_grouped$temp_cat, "temperate")

anova_result <- aov(gravy ~ temp_cat * cell_type, data = df_grouped)
summary(anova_result)

# Check if the column is significant
anova_table <- anova(anova_result)
anova_table
p_value <- anova_table$"Pr(>F)"
p_value

if (p_value < 0.05) {
  # Extract Tukey HSD results
  tukey_result <- TukeyHSD(anova_result)
  tukey_result
  
  # Get only the pairwise comparisons temp
  pairwise_comparisons_temp_cell <- as.data.frame(tukey_result$'temp_cat:cell_type')
  pairwise_comparisons_temp_cell <- pairwise_comparisons_temp_cell %>% 
    rename(p.adj = "p adj")
  
  # Ensure the result is not empty before further processing
      if (length(pairwise_comparisons) > 0) {
        # Rename columns
        pairwise_comparisons_temp_cell$comparison <- rownames(pairwise_comparisons_temp_cell)
        pairwise_comparisons_temp_cell <- pairwise_comparisons_temp_cell[, c("comparison", "diff", "lwr", "upr", "p.adj")]
  
        # Store the relevant information in a data frame
        result_df <- data.frame(
          comparison = rownames(pairwise_comparisons),
          diff = pairwise_comparisons$diff,
          lwr = pairwise_comparisons$lwr,
          upr = pairwise_comparisons$upr,
          p.adj = pairwise_comparisons$p.adj,
          gene_name = rep(unique(gene_data$gene_name), each = nrow(pairwise_comparisons)),
          column = column
        )
        results_list[[column]] <- result_df
      } else {
        cat("No pairwise comparisons found by Tukey HSD for")
      }
    
______________________________
}
return(results_list)

perform_anova_and_tukey <- function(df_grouped, dependent_variable, factor_variables) {
  # Set "temperate" as the reference level
  df_grouped$temp_cat <- relevel(df_grouped$temp_cat, "temperate")

  anova_result <- aov(as.formula(paste0(dependent_variable, " ~ ", paste(factor_variables, collapse = "*"))), data = df_grouped)
  summary(anova_result)

  # Check if the column is significant
  anova_table <- anova(anova_result)
  p_values <- anova_table$"Pr(>F)"

  # Rest of your code for performing Tukey HSD and storing results

  # Example: return a list containing ANOVA results, p-values, and Tukey HSD results
  results_list <- list(
    anova_result = anova_result,
    p_values = p_values,
    tukey_results = tukey_results  # Make sure to replace with the actual Tukey HSD results
  )

  return(results_list)
}

# Example usage
result <- perform_anova_and_tukey(df_grouped, "gravy", c("temp_cat", "cell_type"))
_____________________________________________


# Plot_cell_temp
plot_celltemp <- ggplot(pairwise_comparisons_temp_cell, aes(x = comparison, y = p.adj)) +
  geom_point(position = position_dodge(width = 0.5)) +
  geom_errorbar(aes(ymin = lwr, ymax = upr), width = 0.2, position = position_dodge(width = 0.5)) +
  theme(axis.text.x = element_text(angle = 90, hjust = 1)) +
  labs(
    title = "Stuff",
    x = "Cell/temp Comparison",
    y = "P value")

plot_celltemp

```

```{r}
aov_results <- aov(proline_count ~ temp_cat, data = df_grouped)
anova_table <- anova(aov_results)
anova_table
#summary(anova_result)

# Extract Tukey HSD results
tukey_result <- TukeyHSD(aov_results)

# Get only the pairwise comparisons
pairwise_comparisons <- as.data.frame(tukey_result$temp_cat)
pairwise_comparisons <- pairwise_comparisons %>% 
  rename(p.adj= "p adj")

# Define the desired comparisons
desired_comparisons <- c("cold-freezing-temperate", "warm-hot-cold-freezing", "warm-hot-temperate")

# Filter the DataFrame to include only the desired comparisons
filtered_comparisons <- pairwise_comparisons[rownames(pairwise_comparisons) %in% desired_comparisons, ]

# Print the filtered comparisons
print(filtered_comparisons)
```



## This one adds in the organism name!!!!! -Don't use organism name bc you dont know if they are lining up correctly
```{r include=FALSE}
perform_aov_and_tukey_1 <- function(gene_data, columns_to_compare) {
  results_list <- list()
  
  for (column in columns_to_compare) {
    # Perform ANOVA test including the specified column as a factor
    aov_results <- aov(as.formula(paste0(column, " ~ temp_cat")), data = gene_data)
  
    # Check if the column is significant
    anova_table <- anova(aov_results)
    p_value <- anova_table$"Pr(>F)"[1]  # Assuming temp_cat is the first factor in the ANOVA table
    
    if (p_value < 0.05) {
      # Column is significant, proceed with Tukey HSD
      
      # Run Tukey HSD test
      tukey_results <- TukeyHSD(aov_results, "temp_cat")
      
      # Get only the pairwise comparisons
      pairwise_comparisons <- as.data.frame(tukey_results$temp_cat)
      pairwise_comparisons <- pairwise_comparisons %>% 
        rename(p.adj = "p adj")
      
      # Define the desired comparisons
      #desired_comparisons <- c("cold-freezing-temperate", "warm-hot-cold-freezing", "warm-hot-temperate")
      
      # Filter the DataFrame to include only the desired comparisons
      #filtered_comparisons <- pairwise_comparisons[rownames(pairwise_comparisons) %in% desired_comparisons, ]
      
      # Store the relevant information in a data frame
      result_df <- data.frame(
        comparison = rownames(pairwise_comparisons),
        diff = pairwise_comparisons$diff,
        lwr = pairwise_comparisons$lwr,
        upr = pairwise_comparisons$upr,
        p.adj = pairwise_comparisons$p.adj,
        gene_name = rep(unique(gene_data$gene_name), nrow(pairwise_comparisons)),
        #gene_name = rep(unique(gene_data$gene_name), nrow(filtered_comparisons)),
        #organism_name = rep(unique(gene_data$organism_name), nrow(filtered_comparisons)),
        #genus = rep(unique(gene_data$genus)[1], nrow(filtered_comparisons)),  # Use [1] to take the first value
        column = column
      )
      
      results_list[[column]] <- result_df
    } else {
      cat("No significant differences found by Tukey HSD for", column, "\n")
    }
  }
  
  return(results_list)
}

# Set "temperate" as the reference level
df_grouped$temp_cat <- relevel(df_grouped$temp_cat, "temperate")

# Use the function with the updated plotting method
columns_to_compare <- c("gravy", "flexibility_avg", "flexibility_sum", "acidic_percentage_sum", "aromaticity", "aliphatic_percent_sum", "r_k_ratio", "lysine_count", "arginine_count", "serine_count", "glycine_count", "proline_count")

# Split the data into groups based on gene_name
grouped_data <- df_grouped %>% group_split()

# Apply the function to each group and store the results
all_results_list <- list()

for (i in seq_along(grouped_data)) {
  current_group <- grouped_data[[i]]
  
  # Display the gene name being processed
  cat("Analyzing gene:", unique(current_group$gene_name), "\n")
  
  # Check the number of unique temp_cat values in the current group
  unique_cats <- unique(current_group$temp_cat)
  
  # Skip if there's only one unique temp_cat value
  if (length(unique_cats) == 1) {
    cat("Skipping group for gene_name:", unique(current_group$gene_name), "\n")
    cat("Only one temp_cat category found:", unique_cats, "\n")
    next  # Skip to the next iteration
  }
  
  # Process the group if it has more than one temp_cat category
  results_list <- perform_aov_and_tukey_1(current_group, columns_to_compare)
  
  all_results_list[[i]] <- results_list
}

# Combine the results into a single list of data frames
final_result_list <- do.call(c, all_results_list)

# Combine the list of data frames into a single data frame
final_result_df <- do.call(rbind, final_result_list)

```

```{r}
df_grouped <- df_complete %>%
  group_by(gene_name) %>%
  filter(!is.na(temp_cat)) %>%
  filter(!is.na(cell_type))
df_grouped

perform_aov_and_tukey_1 <- function(gene_data, columns_to_compare) {
  results_list <- list()
  
  for (column in columns_to_compare) {
    # Perform ANOVA test including the specified column as a factor
    aov_results <- aov(as.formula(paste0(column, " ~ temp_cat * cell_type")), data = gene_data)
  
    # Check if the column is significant
    anova_table <- anova(aov_results)
    p_value <- anova_table$"Pr(>F)"  # Assuming temp_cat is the first factor in the ANOVA table
    
    if (p_value < 0.05) {
      # Column is significant, proceed with Tukey HSD
      
      # Run Tukey HSD test
      tukey_results <- TukeyHSD(aov_results, c("temp_cat", "cell_type"))
      
      # Get only the pairwise comparisons
      pairwise_comparisons <- as.data.frame(tukey_results$critical.range)
      
      # Ensure the result is not empty before further processing
      if (length(pairwise_comparisons) > 0) {
        # Rename columns
        colnames(pairwise_comparisons) <- c("diff", "lwr", "upr", "p.adj")
        
        # Store the relevant information in a data frame
        result_df <- data.frame(
          comparison = rownames(pairwise_comparisons),
          diff = pairwise_comparisons$diff,
          lwr = pairwise_comparisons$lwr,
          upr = pairwise_comparisons$upr,
          p.adj = pairwise_comparisons$p.adj,
          gene_name = rep(unique(gene_data$gene_name), each = nrow(pairwise_comparisons)),
          column = column
        )
        
        results_list[[column]] <- result_df
      } else {
        cat("No pairwise comparisons found by Tukey HSD for")
      }
    } else {
      cat("No significant differences found by Tukey HSD for")
    }
  }
  
  return(results_list)
}

# ... (Rest of the code remains the same)


# Set "temperate" as the reference level
df_grouped$temp_cat <- relevel(df_grouped$temp_cat, "temperate")

# Use the function with the updated plotting method
columns_to_compare <- c("gravy", "flexibility_avg", "flexibility_sum", "acidic_percentage_sum", "aromaticity", "aliphatic_percent_sum", "r_k_ratio", "lysine_count", "arginine_count", "serine_count", "glycine_count", "proline_count")

# Split the data into groups based on gene_name
grouped_data <- df_grouped %>% group_split()

# Apply the function to each group and store the results
all_results_list <- list()

for (i in seq_along(grouped_data)) {
  current_group <- grouped_data[[i]]
  
  # Display the gene name being processed
  cat("Analyzing gene:", unique(current_group$gene_name), "\n")
  
  # Check the number of unique temp_cat values in the current group
  unique_cats <- unique(current_group$temp_cat)
  
  # Skip if there's only one unique temp_cat value
  if (length(unique_cats) == 1) {
    cat("Skipping group for gene_name:", unique(current_group$gene_name), "\n")
    cat("Only one temp_cat category found:", unique_cats, "\n")
    next  # Skip to the next iteration
  }
  
  # Process the group if it has more than one temp_cat category
  results_list <- perform_aov_and_tukey_1(current_group, columns_to_compare)
  
  all_results_list[[i]] <- results_list
}

# Combine the results into a single list of data frames
final_result_list <- do.call(c, all_results_list)

# Combine the list of data frames into a single data frame
final_result_df <- do.call(rbind, final_result_list)


```



```{r}
#df_complete_stats <- final_result_df %>%
#  mutate(organism_name_copy = organism_name) %>%
#  separate(organism_name, into = c("genus", "species", "strain", "strain1"), sep = " ", convert = TRUE) %>% 
#  rename(organism_name=organism_name_copy)
#df_complete_stats
```

```{r}
#df_complete_stats_clean <- final_result_df %>%
#  mutate(
#    strain_complete = ifelse(!is.na(strain1), paste(strain, strain1, sep = " "), strain),
#    across(c("strain", "strain1"), ~ifelse(!is.na(.), ., NA), .names = "clean_{.col}")
#  ) %>%
#  select(-strain, -strain1, -clean_strain, -clean_strain1)
#df_complete_stats_clean
```

```{r}
write_csv(final_result_df, "data/stats_results_complete.csv")
```

```{r}
unique(final_result_df$gene_name)
```

## COLD-TEMPERATE
```{r}
df_cold_temperate <- final_result_df %>% 
  filter(p.adj<0.05, comparison=="cold-freezing-temperate")
df_cold_temperate
```

```{r}
unique(df_cold_temperate$gene_name)
```

```{r}
#df_cold_temperate %>% 
#  group_by(genus) %>% 
#  summarize(mean_pvalue=mean(p.adj)) %>% 
#  ggplot(aes(x=reorder(genus, mean_pvalue), y=mean_pvalue))+
#  geom_point()+
#  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 4),
#        axis.text.y = element_text(hjust = 1, size = 7),
#        panel.background = element_blank(),
#        axis.title = element_text(size = 9),
#        legend.title = element_text(size = 8),
#        legend.text = element_text(size = 6))
```

```{r}
unique(df_cold_temperate$column)
```

```{r}
df_flexibility_avg <- df_cold_temperate %>% 
  filter(column=="flexibility_avg")
#df_flexibility_avg
```

```{r}
unique(df_flexibility_avg$gene_name)
```

```{r}
df_flexibility_sum <- df_cold_temperate %>% 
  filter(column=="flexibility_sum")
#df_flexibility_sum
```

```{r}
unique(df_flexibility_sum$gene_name)
```

```{r}
df_acidic_percentage_sum <- df_cold_temperate %>% 
  filter(column=="acidic_percentage_sum")
#df_acidic_percentage_sum
```

```{r}
unique(df_acidic_percentage_sum$gene_name)
```

```{r}
df_lysine_count <- df_cold_temperate %>% 
  filter(column=="lysine_count")
#df_lysine_count
```

```{r}
unique(df_lysine_count$gene_name)
```

```{r}
df_r_k_ratio <- df_cold_temperate %>% 
  filter(column=="r_k_ratio")
#df_r_k_ratio
```

```{r}
unique(df_r_k_ratio$gene_name)
```

```{r}
df_serine_count <- df_cold_temperate %>% 
  filter(column=="serine_count")
#df_serine_count
```

```{r}
unique(df_serine_count$gene_name)
```

```{r}
df_proline_count <- df_cold_temperate %>% 
  filter(column=="proline_count")
#df_proline_count
```

```{r}
unique(df_proline_count$gene_name)
```

```{r}
df_aromaticity <- df_cold_temperate %>% 
  filter(column=="aromaticity")
#df_aromaticity
```

```{r}
unique(df_aromaticity$gene_name)
```

```{r}
df_gravy <- df_cold_temperate %>% 
  filter(column=="gravy")
#df_gravy
```

```{r}
unique(df_gravy$gene_name)
```

### I want to see the genes that are significantlt different between cold and temperate and which columns have those genes
```{r}
# Create a binary matrix indicating the presence/absence of each characteristic for each gene
association_matrix <- table(df_cold_temperate$gene_name, df_cold_temperate$column) > 0

# Create a heatmap
ggplot(data = melt(association_matrix), aes(x = Var1, y = Var2, fill = value)) +
  geom_tile(color = "white") +
  scale_fill_manual(values = c("FALSE" = "#f4e285", "TRUE" = "#5b8e7d")) +
  labs(x = "Gene Name", y = "Characteristic", fill = "Associated", title = "Statistically significant comparison between cold and temperate",) +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6)) 
```








## COLD-HOT
```{r}
df_cold_hot <- final_result_df %>% 
  filter(p.adj<0.05, comparison=="warm-hot-cold-freezing")
df_cold_hot
```

```{r}
#df_cold_hot %>% 
#  group_by(genus) %>% 
#  summarize(mean_pvalue=mean(p.adj)) %>% 
#  ggplot(aes(x=reorder(genus, mean_pvalue), y=mean_pvalue))+
#  geom_point()+
#  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 4),
#        panel.background = element_blank(),
#        axis.title = element_text(size = 9),
#        legend.title = element_text(size = 8),
#        legend.text = element_text(size = 6))
```


### Genes of interest that were found to be statisitcally significant when comparing a difference between cold-freezing and temperate environments they were found in. 

```{r}
unique(df_cold_temperate$gene_name)
```

```{r}
df_flexibility_avg <- df_cold_hot %>% 
  filter(column=="flexibility_avg")
#df_flexibility_avg
```

```{r}
unique(df_flexibility_avg$gene_name)
```

```{r}
df_flexibility_sum <- df_cold_hot %>% 
  filter(column=="flexibility_sum")
#df_flexibility_sum
```

```{r}
unique(df_flexibility_sum$gene_name)
```

```{r}
df_acidic_percentage_sum <- df_cold_hot %>% 
  filter(column=="acidic_percentage_sum")
#df_acidic_percentage_sum
```

```{r}
unique(df_acidic_percentage_sum$gene_name)
```

```{r}
df_lysine_count <- df_cold_hot %>% 
  filter(column=="lysine_count")
#df_lysine_count
```

```{r}
unique(df_lysine_count$gene_name)
```

```{r}
df_r_k_ratio <- df_cold_hot %>% 
  filter(column=="r_k_ratio")
#df_r_k_ratio
```

```{r}
unique(df_r_k_ratio$gene_name)
```

```{r}
df_serine_count <- df_cold_hot %>% 
  filter(column=="serine_count")
#df_serine_count
```

```{r}
unique(df_serine_count$gene_name)
```

```{r}
df_proline_count <- df_cold_hot %>% 
  filter(column=="proline_count")
#df_proline_count
```

```{r}
unique(df_proline_count$gene_name)
```

```{r}
df_aromaticity <- df_cold_hot %>% 
  filter(column=="aromaticity")
#df_aromaticity
```

```{r}
unique(df_aromaticity$gene_name)
```

```{r}
df_gravy <- df_cold_hot %>% 
  filter(column=="gravy")
#df_gravy
```

```{r}
unique(df_gravy$gene_name)
```

```{r}
# Create a binary matrix indicating the presence/absence of each characteristic for each gene
association_matrix <- table(df_cold_hot$gene_name, df_cold_hot$column) > 0

# Create a heatmap
ggplot(data = melt(association_matrix), aes(x = Var1, y = Var2, fill = value)) +
  geom_tile(color = "white") +
  scale_fill_manual(values = c("FALSE" = "#f5c396", "TRUE" = "#5b8e7d")) +
   labs(x = "Gene Name", y = "Characteristic", fill = "Associated", title = "Statistically significant comparison between cold and hot",) +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))
```




## TEMPERATE-HOT
```{r}
df_temperate_hot <- final_result_df %>% 
  filter(p.adj<0.05, comparison=="warm-hot-temperate")
df_temperate_hot
```

```{r}
#df_temperate_hot %>% 
#  group_by(genus) %>% 
#  summarize(mean_pvalue=mean(p.adj)) %>% 
#  ggplot(aes(x=reorder(genus, mean_pvalue), y=mean_pvalue))+
#  geom_point()+
#  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 4),
#        panel.background = element_blank(),
#        axis.title = element_text(size = 9),
#        legend.title = element_text(size = 8),
#        legend.text = element_text(size = 6))
```



### Genes of interest that were found to be statisitcally significant when comparing a difference between cold-freezing and temperate environments they were found in. 
```{r}
#(df_temperate_hot$gene_name)
```

```{r}
unique(df_cold_temperate$gene_name)
```

```{r}
df_flexibility_avg <- df_temperate_hot %>% 
  filter(column=="flexibility_avg")
#df_flexibility_avg
```

```{r}
unique(df_flexibility_avg$gene_name)
```

```{r}
df_flexibility_sum <- df_temperate_hot %>% 
  filter(column=="flexibility_sum")
#df_flexibility_sum
```

```{r}
unique(df_flexibility_sum$gene_name)
```

```{r}
df_acidic_percentage_sum <- df_temperate_hot %>% 
  filter(column=="acidic_percentage_sum")
#df_acidic_percentage_sum
```

```{r}
unique(df_acidic_percentage_sum$gene_name)
```

```{r}
df_lysine_count <- df_temperate_hot %>% 
  filter(column=="lysine_count")
#df_lysine_count
```

```{r}
unique(df_lysine_count$gene_name)
```

```{r}
df_r_k_ratio <- df_temperate_hot %>% 
  filter(column=="r_k_ratio")
#df_r_k_ratio
```

```{r}
unique(df_r_k_ratio$gene_name)
```

```{r}
df_serine_count <- df_temperate_hot %>% 
  filter(column=="serine_count")
#df_serine_count
```

```{r}
unique(df_serine_count$gene_name)
```

```{r}
df_proline_count <- df_temperate_hot %>% 
  filter(column=="proline_count")
#df_proline_count
```

```{r}
unique(df_proline_count$gene_name)
```

```{r}
df_aromaticity <- df_temperate_hot %>% 
  filter(column=="aromaticity")
#df_aromaticity
```

```{r}
unique(df_aromaticity$gene_name)
```

```{r}
df_gravy <- df_temperate_hot %>% 
  filter(column=="gravy")
#df_gravy
```

```{r}
unique(df_gravy$gene_name)
```

```{r}
# Create a binary matrix indicating the presence/absence of each characteristic for each gene
association_matrix <- table(df_temperate_hot$gene_name, df_temperate_hot$column) > 0

# Create a heatmap
ggplot(data = melt(association_matrix), aes(x = Var1, y = Var2, fill = value)) +
  geom_tile(color = "white") +
  scale_fill_manual(values = c("FALSE" = "#ffb7c3", "TRUE" = "#5b8e7d")) +
   labs(x = "Gene Name", y = "Characteristic", fill = "Associated", title = "Statistically significant comparison between hot and temperate",) +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))
```

```{r}
df_grouped
```

## Look at the average value for each characteristic and see if the difference is what we thought it would be
```{r}
mean_characteristic_cold <- df_grouped %>% 
  filter(temp_cat=="cold-freezing", gene_name!="aceF", gene_name!="dnaA", gene_name!="dnaJ", gene_name!="dnaK", gene_name!="gyrA", gene_name!="infA",gene_name!="infB",gene_name!="infC",gene_name!="rnr", gene_name!="yifA", gene_name!="desR",gene_name!="desK",gene_name!="desC", gene_name!="psaE",gene_name!="psbB", gene_name!="psbO",gene_name!="desABCD") %>%  
  group_by(gene_name) %>% 
  summarize(mean_proline_count=mean(proline_count),
            mean_glycine_count=mean(glycine_count),
            mean_serine_count=mean(serine_count),
            mean_r_k_ratio=mean(r_k_ratio),
            mean_aliphatic_percent_sum=mean(aliphatic_percent_sum),
            mean_aromaticity=mean(aromaticity),
            mean_acidic_percentage_sum=mean(acidic_percentage_sum),
            mean_flexibility_sum=mean(flexibility_sum),
            mean_flexibility_avg=mean(flexibility_avg),
            mean_gravy=mean(gravy),
            sd_proline_count=sd(proline_count),
            sd_glycine_count=sd(glycine_count),
            sd_serine_count=sd(serine_count),
            sd_r_k_ratio=sd(r_k_ratio),
            sd_aliphatic_percent_sum=sd(aliphatic_percent_sum),
            sd_aromaticity=sd(aromaticity),
            sd_acidic_percentage_sum=sd(acidic_percentage_sum),
            sd_flexibility_sum=sd(flexibility_sum),
            sd_flexibility_avg=sd(flexibility_avg),
            sd_gravy=sd(gravy),
            n=n()) %>% 
  arrange(desc(mean_proline_count))

ggplot(mean_characteristic_cold, aes(x = reorder(gene_name, -mean_proline_count), y = mean_proline_count)) +
  geom_col() +
  labs(x = "Gene Name", y = "Proline Count") +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))
  
mean_characteristic_cold
```

```{r}
mean_characteristic_hot <- df_grouped %>% 
  filter(temp_cat=="warm-hot", gene_name!="aceF", gene_name!="dnaA", gene_name!="dnaJ", gene_name!="dnaK", gene_name!="gyrA", gene_name!="infA",gene_name!="infB",gene_name!="infC",gene_name!="rnr", gene_name!="yifA", gene_name!="desR",gene_name!="desK",gene_name!="desC", gene_name!="psaE",gene_name!="psbB", gene_name!="psbO",gene_name!="desABCD") %>%  
  group_by(gene_name) %>% 
  summarize(mean_proline_count=mean(proline_count),
            mean_glycine_count=mean(glycine_count),
            mean_serine_count=mean(serine_count),
            mean_r_k_ratio=mean(r_k_ratio),
            mean_aliphatic_percent_sum=mean(aliphatic_percent_sum),
            mean_aromaticity=mean(aromaticity),
            mean_acidic_percentage_sum=mean(acidic_percentage_sum),
            mean_flexibility_sum=mean(flexibility_sum),
            mean_flexibility_avg=mean(flexibility_avg),
            mean_gravy=mean(gravy),
            sd_proline_count=sd(proline_count),
            sd_glycine_count=sd(glycine_count),
            sd_serine_count=sd(serine_count),
            sd_r_k_ratio=sd(r_k_ratio),
            sd_aliphatic_percent_sum=sd(aliphatic_percent_sum),
            sd_aromaticity=sd(aromaticity),
            sd_acidic_percentage_sum=sd(acidic_percentage_sum),
            sd_flexibility_sum=sd(flexibility_sum),
            sd_flexibility_avg=sd(flexibility_avg),
            sd_gravy=sd(gravy),
            n=n()) %>% 
  arrange(desc(mean_proline_count))

ggplot(mean_characteristic_hot, aes(x = reorder(gene_name, mean_proline_count), y = mean_proline_count)) +
  geom_col() +
  labs(x = "Gene Name", y = "Proline Count") +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))
  
mean_characteristic_hot
```
```{r}
unique(df_grouped$gene_name)
```


```{r}
mean_characteristic_temperate <- df_grouped %>% 
  filter(temp_cat=="temperate", gene_name!="aceF", gene_name!="dnaA", gene_name!="dnaJ", gene_name!="dnaK", gene_name!="gyrA", gene_name!="infA",gene_name!="infB",gene_name!="infC",gene_name!="rnr", gene_name!="yifA", gene_name!="desR",gene_name!="desK",gene_name!="desC", gene_name!="psaE",gene_name!="psbB", gene_name!="psbO",gene_name!="desABCD") %>% 
  group_by(gene_name) %>% 
  summarize(mean_proline_count=mean(proline_count),
            mean_glycine_count=mean(glycine_count),
            mean_serine_count=mean(serine_count),
            mean_r_k_ratio=mean(r_k_ratio),
            mean_aliphatic_percent_sum=mean(aliphatic_percent_sum),
            mean_aromaticity=mean(aromaticity),
            mean_acidic_percentage_sum=mean(acidic_percentage_sum),
            mean_flexibility_sum=mean(flexibility_sum),
            mean_flexibility_avg=mean(flexibility_avg),
            mean_gravy=mean(gravy),
            sd_proline_count=sd(proline_count),
            sd_glycine_count=sd(glycine_count),
            sd_serine_count=sd(serine_count),
            sd_r_k_ratio=sd(r_k_ratio),
            sd_aliphatic_percent_sum=sd(aliphatic_percent_sum),
            sd_aromaticity=sd(aromaticity),
            sd_acidic_percentage_sum=sd(acidic_percentage_sum),
            sd_flexibility_sum=sd(flexibility_sum),
            sd_flexibility_avg=sd(flexibility_avg),
            sd_gravy=sd(gravy),
            n=n()) %>% 
  arrange(desc(mean_proline_count))

ggplot(mean_characteristic_temperate, aes(x = reorder(gene_name, mean_proline_count), y = mean_proline_count)) +
  geom_col() +
  labs(x = "Gene Name", y = "Proline Count") +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))
  
mean_characteristic_temperate
```

```{r}
 mean_characteristic_all<- bind_rows(
  mutate(mean_characteristic_temperate, dataset = "temperate"),
  mutate(mean_characteristic_hot, dataset = "warm-hot"),
  mutate(mean_characteristic_cold, dataset = "cold-freezing")
)

ggplot(mean_characteristic_all, aes(x = reorder(gene_name, mean_proline_count), y = mean_proline_count, fill = dataset)) +
  geom_col(position = "dodge")+
  #geom_point(position = position_dodge(width = 0.2), size = 2) +
  #geom_errorbar(aes(ymin = mean_proline_count - sd_proline_count, ymax = mean_proline_count + sd_proline_count),
               #position = position_dodge(width = 0.2), width = 0.2) +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))+
  scale_fill_manual(values = c("warm-hot" = "lightsalmon", "cold-freezing" = "skyblue2", "temperate" = "darkseagreen")) +
  labs(title = "proline_count averages",
       x = "Aromaticity",
       y= "Proline Count",
       color= "Temp Category")
```

```{r}
cold_freezing <- df_grouped %>% 
  filter(temp_cat=="cold-freezing") %>% 
  group_by(gene_name)
cold_freezing
```

```{r}
warm_hot <- df_grouped %>% 
  filter(temp_cat=="warm-hot") %>% 
  group_by(gene_name)
warm_hot
```

```{r}
temperate <- df_grouped %>% 
  filter(temp_cat=="temperate") %>% 
  group_by(gene_name)
temperate
```

```{r}
mean_characteristic_all<- bind_rows(
  mutate(temperate, dataset = "temperate"),
  #mutate(warm_hot, dataset = "warm-hot"),
  mutate(cold_freezing, dataset = "cold-freezing")
)

# Function to process and plot data for a specific column
process_and_plot <- function(column_name) {
  goi_mean_characteristic_all <- mean_characteristic_all %>% 
    filter(gene_name %in% c("D2_PsbD", "cya_PsbA", "bac_PsbA", "hik33", "hik33_cyano")) %>% 
    filter(temp_cat!="warm-hot") %>% 
    mutate(gene_name = case_when(
      gene_name == "D2_PsbD" ~ "D2 PSII",
      gene_name == "cya_PsbA" ~ "D1 PSII (Cyanobacteria)",
      gene_name == "bac_PsbA" ~ "D1 PSII (Bacteria)",
      gene_name == "hik33" ~ "Hik33 (Bacteria)",
      gene_name == "hik33_cyano" ~ "Hik33 (Cyanobacteria)",
      TRUE ~ as.character(gene_name)
    ))

  ggplot(goi_mean_characteristic_all, aes(x = reorder(gene_name, !!sym(column_name)), y = !!sym(column_name), fill = dataset)) +
    geom_boxplot(outlier.shape = NA, position = position_dodge(width = 0.8), width = 0.7) + 
    geom_violin(alpha = .5, na.rm = TRUE) +
    theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
          axis.text.y = element_text(hjust = 1, size = 7),
          #panel.background = element_blank(),
          axis.title = element_text(size = 9),
          legend.title = element_text(size = 8),
          legend.text = element_text(size = 6)) +
    scale_fill_manual(values = c("warm-hot" = "salmon", "cold-freezing" = "skyblue2", "temperate" = "darkseagreen")) +
    labs(title = "Amino Acid Characteristic Changes when Comparing Temperature of Environment", 
         x = "Genes Of Interest",
         y = str_to_title(str_replace_all(column_name, "_", " ")), 
         color = "Temp Category")
}

# Columns to compare
columns_to_compare <- c("gravy", "flexibility_avg", "flexibility_sum", "acidic_percentage_sum", "aromaticity", "aliphatic_percent_sum", "r_k_ratio", "lysine_count", "arginine_count", "serine_count", "glycine_count", "proline_count")

# Apply the function to each column
plots_list <- lapply(columns_to_compare, process_and_plot)

# You can access individual plots using plots_list[[i]], where i is the index of the column

plots_list[[9]]

```

### Example of above with out making it a fxn
```{r}
 mean_characteristic_all<- bind_rows(
  mutate(temperate, dataset = "temperate"),
  mutate(warm_hot, dataset = "warm-hot"),
  mutate(cold_freezing, dataset = "cold-freezing")
)

unique(mean_characteristic_all$gene_name)

goi_mean_characteristic_all <- mean_characteristic_all %>% 
  filter(gene_name=="D2_PsbD"| gene_name=="cya_PsbA"| gene_name=="bac_PsbA"| gene_name=="hik33"| gene_name=="hik33_cyano") %>% 
  mutate(gene_name = case_when(
    gene_name == "D2_PsbD" ~ "D2 PSII",
    gene_name == "cya_PsbA" ~ "D1 PSII (Cyanobacteria)",
    gene_name == "bac_PsbA" ~ "D1 PSII (Bacteria)",
    gene_name == "hik33" ~ "Hik33 (Bacteria)",
    gene_name == "hik33_cyano" ~ "Hik33 (Cyanobacteria)",
    TRUE ~ gene_name
  ))

goi_mean_characteristic_all

ggplot(goi_mean_characteristic_all, aes(x = reorder(gene_name, aromaticity), y = aromaticity, fill = dataset)) +
  geom_boxplot(outlier.shape = NA, position = position_dodge(width = 0.8), width = 0.7) + 
  geom_violin(alpha = .5, na.rm = TRUE) +# Hide default outliers
  #geom_point(position = position_dodge(width = 0.2), size = 0.1, color = "black") +  # Adjust the size and color of the points
  #geom_jitter(position = position_dodge(width = 0.2), size = 0.1, color = "grey", alpha = 0.5) +  # Add jittered points for outliers
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 7),
        axis.text.y = element_text(hjust = 1, size = 7),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6)) +
  scale_fill_manual(values = c("warm-hot" = "salmon", "cold-freezing" = "skyblue2", "temperate" = "darkseagreen")) +
  labs(title = "Aromaticity",
       x = "Genes Of Interest",
       y = "Aromaticity",
       color = "Temp Category")
```




























## So far this works! these are plots
```{r}

perform_aov_and_tukey_2 <- function(gene_data, columns_to_compare) {
  plots_list <- list()
  
  for (column in columns_to_compare) {
    # Perform ANOVA test including the specified column as a factor
    aov_results <- aov(as.formula(paste0(column, " ~ temp_cat")), data = gene_data)
  
    # Check if the column is significant
    anova_table <- anova(aov_results)
    #anova_table
    p_value <- anova_table$"Pr(>F)"[1]  # Assuming temp_cat is the first factor in the ANOVA table
    
    if (p_value < 0.05) {
      # Column is significant, proceed with Tukey HSD
      
      # Run Tukey HSD test
      tukey_results <- TukeyHSD(aov_results, "temp_cat")
      
      # Get only the pairwise comparisons
      pairwise_comparisons <- as.data.frame(tukey_result$temp_cat)
      pairwise_comparisons <- pairwise_comparisons %>% 
        rename(p.adj= "p adj")
      
      # Define the desired comparisons
      #desired_comparisons <- c("cold-freezing-temperate", "warm-hot-cold-freezing", "warm-hot-temperate")
      
      # Filter the DataFrame to include only the desired comparisons
      #filtered_comparisons <- pairwise_comparisons[rownames(pairwise_comparisons) %in% desired_comparisons, ]
      
      # Print the filtered comparisons
      #print(filtered_comparisons)
      
      # Extract significant differences for plotting
      #comparisons <- as.data.frame(tukey_results$`temp_cat`)
      #comparisons$comparison <- rownames(comparisons)
      pairwise_comparisons$comparison <- rownames(pairwise_comparisons)
      #comparisons <- comparisons[, c("diff", "lwr", "upr", "comparison")]
      pairwise_comparisons <- pairwise_comparisons[, c("comparison", "diff", "lwr", "upr", "p.adj")]
      

      # Plot using ggplot2
      #plot <- ggplot(comparisons, aes_string(x = "comparison", y = "diff")) +
      plot <- ggplot(pairwise_comparisons, aes_string(x = "comparison", y = "p.adj")) +
      geom_point(position = position_dodge(width = 0.5)) +
      geom_errorbar(aes(ymin = lwr, ymax = upr), width = 0.2, position = position_dodge(width = 0.5)) +
      theme(axis.text.x = element_text(angle = 90, hjust = 1)) +
      labs(
        title = paste("Significant Differences from Tukey HSD Test for", column, "-", unique(gene_data$gene_name)),
        x = "Temperature Comparison",
        #y = "Difference in Means"
        y = "P value"
      )

      
      plots_list[[column]] <- plot
    } else {
      cat("No significant differences found by Tukey HSD for", column, "\n")
    }
  }
  
  return(plots_list)
}

# Set "temperate" as the reference level
df_grouped$temp_cat <- relevel(df_grouped$temp_cat, "temperate")

# Use the function with the updated plotting method
columns_to_compare <- c("gravy", "flexibility_avg", "flexibility_sum", "acidic_percentage_sum", "aromaticity", "aliphatic_percent_sum", "r_k_ratio", "lysine_count", "arginine_count", "serine_count", "glycine_count", "proline_count")

# Split the data into groups based on gene_name
grouped_data <- df_grouped %>% group_split()

# Apply the function to each group and store the plots
all_plots_list <- list()


for (i in seq_along(grouped_data)) {
  current_group <- grouped_data[[i]]
  
  # Display the gene name being processed
  cat("Analyzing gene:", unique(current_group$gene_name), "\n")
  
  # Check the number of unique temp_cat values in the current group
  unique_cats <- unique(current_group$temp_cat)
  
  # Skip if there's only one unique temp_cat value
  if (length(unique_cats) == 1) {
    cat("Skipping group for gene_name:", unique(current_group$gene_name), "\n")
    cat("Only one temp_cat category found:", unique_cats, "\n")
    next  # Skip to the next iteration
  }
  
  # Process the group if it has more than one temp_cat category
  plots_list <- perform_aov_and_tukey_2(current_group, columns_to_compare)
  
  all_plots_list[[i]] <- plots_list
}

# View or save the plots as needed
all_plots_list[[37]]  # Example to view the first set of plots in the list

```

















```{r}
# List unique values before conversion
unique(df_complete$genus)

# Convert genus to factor with specified levels
df_complete$genus <- factor(
  df_complete$genus,
  levels = unique(df_complete$genus)  # Use unique values as levels
)

# Check the updated genus column
#df_complete$genus

```


```{r}
df_merged <- read.csv("data/GOI_concatenated.csv")
```

```{r}
df_merged <- clean_names(df_merged)
#names(df_merged)
```

```{r}
df_merged <- df_merged %>% 
  select(gen_bank_assembly_id_accession_version, organism_name_x, organism_tidy_name, ncbi_taxonomy_x, gtdb_taxonomy_x, check_m_completeness_x, check_m_contamination_x, gc_percentage_x, genome_size_x, regional_loaction, geographic_feature, environment_detail, genome_representation, temp_cat, gene_ref, gene_original, proline_count, glycine_count, serine_count, arginine_count, lysine_count, r_k_ratio, aliphatic_percent_sum, aromaticity, acidic_percentage_sum, flexibility_sum, flexibility_avg, gravy)
df_merged
```

```{r}
df_merged <- df_merged %>% 
  rename(accession=gen_bank_assembly_id_accession_version, organism_name=organism_name_x, ncbi_taxonomy=ncbi_taxonomy_x, gtdb_taxonomy=gtdb_taxonomy_x, check_m_completeness=check_m_completeness_x, check_m_contamination=check_m_contamination_x, gc_percentage=gc_percentage_x, genome_size=genome_size_x, gene_id=gene_ref, gene_name=gene_original)
df_merged
```


```{r}
df_complete <- merge(df_complete, df_merged, by = "accession", all = TRUE)
df_complete
```


```{r}
# Combine the 'City' columns from both data frames
df_complete$organism_tidy_name <- ifelse(is.na(df_complete$organism_tidy_name.x), df_complete$organism_tidy_name.y, df_complete$organism_tidy_name.x)

df_complete$organism_name <- ifelse(is.na(df_complete$organism_name.x), df_complete$organism_name.y, df_complete$organism_name.x)

df_complete$ncbi_taxonomy <- ifelse(is.na(df_complete$ncbi_taxonomy.x), df_complete$ncbi_taxonomy.y, df_complete$ncbi_taxonomy.x)

df_complete$gtdb_taxonomy <- ifelse(is.na(df_complete$gtdb_taxonomy.x), df_complete$gtdb_taxonomy.y, df_complete$gtdb_taxonomy.x)

df_complete$check_m_completeness <- ifelse(is.na(df_complete$check_m_completeness.x), df_complete$check_m_completeness.y, df_complete$check_m_completeness.x)

df_complete$check_m_contamination <- ifelse(is.na(df_complete$check_m_contamination.x), df_complete$check_m_contamination.y, df_complete$check_m_contamination.x)

df_complete$gc_percentage <- ifelse(is.na(df_complete$gc_percentage.x), df_complete$gc_percentage.y, df_complete$gc_percentage.x)

df_complete <- df_complete %>% 
  select(-gc_percentage.x, -gc_percentage.y, -check_m_contamination.x, -check_m_contamination.y, -check_m_completeness.x, -check_m_completeness.y, -gtdb_taxonomy.x, -gtdb_taxonomy.y, -ncbi_taxonomy.x, -ncbi_taxonomy.y, -organism_name.x, -organism_name.y, -organism_tidy_name.x, -organism_tidy_name.y)
df_complete

df_complete$temp_cat <- ifelse(is.na(df_complete$temp_cat.x), df_complete$temp_cat.y, df_complete$temp_cat.x)

df_complete$gene_id <- ifelse(is.na(df_complete$gene_id.x), df_complete$gene_id.y, df_complete$gene_id.x)

df_complete$gene_name <- ifelse(is.na(df_complete$gene_name.x), df_complete$gene_name.y, df_complete$gene_name.x)

df_complete$proline_count <- ifelse(is.na(df_complete$proline_count.x), df_complete$proline_count.y, df_complete$proline_count.x)

df_complete$glycine_count <- ifelse(is.na(df_complete$glycine_count.x), df_complete$glycine_count.y, df_complete$glycine_count.x)

df_complete$serine_count <- ifelse(is.na(df_complete$serine_count.x), df_complete$serine_count.y, df_complete$serine_count.x)

df_complete <- df_complete %>% 
  select(-temp_cat.x, -temp_cat.y, -gene_id.x, -gene_id.y, -gene_name.x, -gene_name.y, -proline_count.x, -proline_count.y, -glycine_count.x, -glycine_count.y, -serine_count.x, -serine_count.y)
df_complete

df_complete$arginine_count <- ifelse(is.na(df_complete$arginine_count.x), df_complete$arginine_count.y, df_complete$arginine_count.x)

df_complete$lysine_count <- ifelse(is.na(df_complete$lysine_count.x), df_complete$lysine_count.y, df_complete$lysine_count.x)

df_complete$r_k_ratio <- ifelse(is.na(df_complete$r_k_ratio.x), df_complete$r_k_ratio.y, df_complete$r_k_ratio.x)

df_complete$aliphatic_percent_sum <- ifelse(is.na(df_complete$aliphatic_percent_sum.x), df_complete$aliphatic_percent_sum.y, df_complete$aliphatic_percent_sum.x)

df_complete$aromaticity <- ifelse(is.na(df_complete$aromaticity.x), df_complete$aromaticity.y, df_complete$aromaticity.x)

df_complete <- df_complete %>% 
  select(-arginine_count.x, -arginine_count.y, -lysine_count.x, -lysine_count.y, -r_k_ratio.x, -r_k_ratio.y, -aliphatic_percent_sum.x, -aliphatic_percent_sum.y, -aromaticity.x, -aromaticity.y)
df_complete

df_complete$acidic_percentage_sum <- ifelse(is.na(df_complete$acidic_percentage_sum.x), df_complete$acidic_percentage_sum.y, df_complete$acidic_percentage_sum.x)

df_complete$flexibility_sum <- ifelse(is.na(df_complete$flexibility_sum.x), df_complete$flexibility_sum.y, df_complete$flexibility_sum.x)

df_complete$flexibility_avg <- ifelse(is.na(df_complete$flexibility_avg.x), df_complete$flexibility_avg.y, df_complete$flexibility_avg.x)

df_complete$gravy <- ifelse(is.na(df_complete$gravy.x), df_complete$gravy.y, df_complete$gravy.x)

df_complete$genome_size <- ifelse(is.na(df_complete$genome_size.x), df_complete$genome_size.y, df_complete$genome_size.x)

df_complete <- df_complete %>% 
  select(-acidic_percentage_sum.x, -acidic_percentage_sum.y, -flexibility_sum.x, -flexibility_sum.y, -flexibility_avg.x, -flexibility_avg.y, -gravy.x, -gravy.y, -genome_size.y, -genome_size.x)
df_complete
```


## cleaning up the data 
```{r}
table(df_merged$gene_id)
```

```{r}
df_complete$gene_id <- gsub("gyra", "gyrA", df_complete$id)
```

```{r}
#unique_values <- unique(df_complete$gene_name)
#unique_values
```











```{r}
df_aa <- df_complete %>%
  filter(temp_cat != "NA") %>% 
  ggplot(aes(x=aromaticity/genome_size, y=proline_count/genome_size, color=temp_cat))+
  geom_point(na.rm=T)+
  theme(axis.text.x = element_text(hjust = 1, size = rel(1)),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))+
  scale_color_manual(values = c("warm-hot" = "lightsalmon", "cold-freezing" = "skyblue2", "temperate" = "darkseagreen")) +
  labs(title = "DesABCD of Cyanobacteria",
       x = "Aromaticity",
       y= "Proline Count",
       color= "Temp Category")
df_aa

#ggsave("DesABCD_proline_aromaticity.png")
```

```{r}
df_plot2 <- df_complete %>%
  filter(temp_cat != "NA") %>%
  ggplot(aes(x=aromaticity/genome_size, y=r_k_ratio/genome_size, color=temp_cat))+
  geom_point(na.rm=T, size=0.75)+
  theme(axis.text.x = element_text(hjust = 1, size = rel(1)),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))+
  scale_color_manual(values = c("warm-hot" = "lightsalmon", "cold-freezing" = "deepskyblue3", "temperate" = "darkseagreen3")) +
  labs(title = "Aromaticity vs Arginine/Lysine ratio DesABCD of Cyanobacteria",
       x = "Arginine/Lysine ratio",
       y= "Aromaticity",
       color= "Temp Category")
df_plot2

#ggsave("DesABCD_rkratio_aromaticity.png")
```

```{r}
df_plot3 <- df_complete %>%
  filter(temp_cat != "NA") %>% 
  ggplot(aes(x=serine_count/genome_size, y=proline_count/genome_size, color=temp_cat))+
  geom_point(na.rm=T, size=0.5)+
  theme(axis.text.x = element_text(hjust = 1, size = rel(1)),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))+
  scale_color_manual(values = c("warm-hot" = "lightsalmon", "cold-freezing" = "skyblue2", "temperate" = "darkseagreen")) +
  labs(title = "DesR of Cyanobacteria",
       x = "Serine Count",
       y= "Proline Count",
       color= "Temp Category")
df_plot3

#ggsave("DesABCD_proline_serine.png")
```

```{r}
df_separate_name <- df_complete %>% 
  separate(organism_tidy_name, into = c("genus", "species", "strain", "strain1", "strain2"), sep = "\\_")
df_separate_name
```

```{r}
df_plot4 <- df_separate_name %>% 
  select(genus, proline_count, genome_size, temp_cat) %>% 
  filter(!is.na(proline_count), !is.na(temp_cat)) %>% 
  ggplot(aes(x=genus, y=proline_count/genome_size, color=temp_cat))+
  geom_boxplot(alpha = .3, na.rm = TRUE, outlier.size = 0.2) +
  facet_wrap(~temp_cat, ncol=4) +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 3),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))+
  scale_color_manual(values = c("warm-hot" = "lightsalmon", "cold-freezing" = "deepskyblue3", "temperate" = "darkseagreen3")) +
  labs(title = "Proline Counts within TCS Genes of Cyanobacteria",
       x = "Cyanobacteria",
       y= "Proline Counts",
       color= "Temp Category")
  
df_plot4

#ggsave("DesABCD_proline.png")
```

```{r}
df_plot5 <- df_separate_name %>% 
  select(organism_name, gravy, genome_size, temp_cat) %>% 
  filter(!is.na(gravy), !is.na(temp_cat)) %>% 
  ggplot(aes(x=organism_name, y=gravy/genome_size, color=temp_cat))+
  geom_boxplot(alpha = .3, na.rm = TRUE, outlier.size = 0.2) +
  facet_wrap(~temp_cat, ncol=4) +
  theme(axis.text.x = element_text(angle = 60, hjust = 1, size = 3),
        panel.background = element_blank(),
        axis.title = element_text(size = 9),
        legend.title = element_text(size = 8),
        legend.text = element_text(size = 6))+
  scale_color_manual(values = c("warm-hot" = "lightsalmon", "cold-freezing" = "deepskyblue3", "temperate" = "darkseagreen3")) +
  labs(title = "Proline Counts within TCS Genes of Cyanobacteria",
       x = "Cyanobacteria",
       y= "Proline Counts",
       color= "Temp Category")
  
df_plot5

#ggsave("DesABCD_proline1.png")
```



:::success

:::

# Command line
```
#FARM

CLUSTALo
$ conda activate trees
$ clustalo --in=filtered_seq.fa -o output_file.fa
```
Nostoc_sp__ATCC_53789
```
Modeltest−NG
$ modeltest-ng -d aa -i output_file.fa -t ml -o marker_model.txt -h g 

or 

$ modeltest-ng --datatype aa
               --input alignment11_17_24.fasta 
               --topology ml
               --output 16S_111724_complete.txt
               --model-het g  #Discrite Gamma rate categories (+G)
```


```
$ raxmlHPC -s output_file.fa -n tree_output -m PROTCATLG -p 12345 
```
















New snakefile
:::danger
This is a work in progress bc I'm learning. don't actually use this!!!!
```
FILENAMES ="file_names.txt"
#TEST_FILENAMES ="subset_file_names.txt"
#TEST_SAMPLE_LST= [x.strip().split(".fna")[0] for x in open(FILENAMES, 'r')]

EXTERNAL_GENOMES ="external_genomes.txt"    # no idea if this is ok

SAMPLE_LST= [x.strip().split(".fna")[0] for x in open(FILENAMES, 'r')]
#TEST_SAMPLE_LST= SAMPLE_LST[3:5]

HMM_DIR_NAME="hmms"
PHOTO_HMM_DIR_NAME="photo_hmms"
MEMBRANE_HMM_DIR_NAME="membrane_hmms"

rule all:
    input:
        #reformat_fasta
        #    expand("{sample_i}.fa", sample_i=SAMPLE_LST),
        #        tsv is key file for contigs
        #    expand("{sample_i}.tsv", sample_i=SAMPLE_LST)
        #    expand("{sample_i}.db", sample_i=SAMPLE_LST)
        #    expand("{sample_i}.stats.txt", sample_i=SAMPLE_LST)
        #    expand("{sample_i}.stats.photo.txt", sample_i=SAMPLE_LST)
        #    expand("{sample_i}.stats.membrane.txt", sample_i=SAMPLE_LST)
        #    expand("reports/seq_report/{sample_i}.hmm.sequence.txt", sample_i=SAMPLE_LST)
        #    expand("reports/seq_report/{sample_i}.photo.sequence.txt", sample_i=SAMPLE_LST)
        #    expand("reports/seq_report/{sample_i}.membrane.sequence.txt", sample_i=SAMPLE_LST)
        #    expand("")
        
    

rule reformat_fasta:
    input: 
        "mags_fna/{sample_i}.fna"
    output:
        seq="reformatted_mags/{sample_i}.fa",
        rpt="reports/reformat_reports/{sample_i}.tsv"
    shell: 
        """
        anvi-script-reformat-fasta {input} -o {output.seq}\
        -l 0 --simplify-names --report-file {output.rpt}
        """

rule create_contigs_db:
    input:
        "reformatted_mags/{sample_i}.fa"
    output:
        "db/{sample_i}.db"
    threads: 4
    shell:
        """
        anvi-gen-contigs-database -f {input} -o {output}\
        -T {threads} --project-name ctg_cyanos
        """


rule run_hmms:
    input:
        "db/{sample_i}.db" 
    output:
        "reports/hmm_hits/{sample_i}.stats.txt"
    shell:
        """
        anvi-run-hmms -c {input} --hmm-profile-dir {HMM_DIR_NAME}
        anvi-display-contigs-stats {input} --report-as-text -o {output}
        """
        
rule run_photo_hmms:
    input:
        "db/{sample_i}.db"
    output:
        "reports/hmm_hits/{sample_i}.stats.photo.txt"
    shell:
        """
        anvi-run-hmms -c {input} --hmm-profile-dir {PHOTO_HMM_DIR_NAME}
        anvi-display-contigs-stats {input} --report-as-text -o {output}
        """
        
rule run_membrane_hmms:
    input:
        "db/{sample_i}.db"
    output:
        "reports/hmm_hits/{sample_i}.stats.membrane.txt"
    shell:
        """
        anvi-run-hmms -c {input} --hmm-profile-dir {MEMBRANE_HMM_DIR_NAME}
        anvi-display-contigs-stats {input} --report-as-text -o {output}
        """

rule hmm_sequence_hits:
    input:
        db="db/{sample_i}.db",
        
        #we dont actually use these anymore, they are a placeholder for snakemake while .dbs are updated
        rpt="reports/hmm_hits/{sample_i}.stats.txt"
    output:
        "reports/seq_report/{sample_i}.hmm.sequence.txt"
    shell:
        """
        anvi-get-sequences-for-hmm-hits -c {input.db} --hmm-source {HMM_DIR_NAME} -o {output}
        """
        
rule photo_sequence_hits:
    input:
        db="db/{sample_i}.db",
        
        #we dont actually use these anymore, they are a placeholder for snakemake while .dbs are updated
        rpt="reports/hmm_hits/{sample_i}.stats.txt"
    output:
        "reports/seq_report/{sample_i}.photo.sequence.txt"
    shell:
        """
        anvi-get-sequences-for-hmm-hits -c {input.db} --hmm-source {PHOTO_HMM_DIR_NAME} -o {output}
        """
        
rule membrane_sequence_hits:
    input:
        db="db/{sample_i}.db",
        
        #we dont actually use these anymore, they are a placeholder for snakemake while .dbs are updated
        rpt="reports/hmm_hits/{sample_i}.stats.txt"
    output:
        "reports/seq_report/{sample_i}.membrane.sequence.txt"
    shell:
        """
        anvi-get-sequences-for-hmm-hits -c {input.db} --hmm-source {MEMBRANE_HMM_DIR_NAME} -o {output}
        """

rule hmm_matrix:
    input:
        "./{list_i}.txt"
    output:
    shell:
        """
        anvi-script-gen-hmm-hits-matrix-across-genomes -e external-genomes-filamentous-names-v2.txt \
                                               -o {output}.txt \
                                               --hmm-source {MEMBRANE_HMM_DIR_NAME}
        """
        

```
`# touch /home/kmrcello/cyanobacteria/cyano_MAGs/mags_fna/*`
:::




|  | V1 | V2 |V3 |
| -------- | -------- | -------- |-------- |
| comparison     | cold-freezing - temperate     | cold-freezing - warm-hot     |temperate - warm-hot     |
| z_value     | Text     | Text     |Text     |
| p_adj     | Text     | Text     |Text     |
| gene_name     | CT_aceE     | CT_aceE     |CT_aceE     |
| column     | sum_gravy     | sum_gravy     |sum_gravy     |
| comparison.1     | cold-freezing - temperate     | cold-freezing - warm-hot     |temperate - warm-hot     |
| z_value.1     | Text     | Text     |Text     |
| p_adj.1     | Text     | Text     |Text     |
| gene_name.1     | CT_aceE     | CT_aceE     |CT_aceE     |
| column.1     | sum_flexibility_avg     | sum_flexibility_avg     |sum_flexibility_avg     |


comparison
cold-freezing - temperate
cold-freezing - warm-hot
temperate - warm-hot
z_value
1.07182376985841
1.27855346215837
0.774083085873009
p_adj
0.425698791860368
0.301581520394982
0.658322488691775
gene_name
CT_aceE
CT_aceE
CT_aceE
column
sum_gravy
sum_gravy
sum_gravy
comparison.1
cold-freezing - temperate
cold-freezing - warm-hot
temperate - warm-hot
z_value.1
-2.58637911235926
-2.86463820904907
-1.63722229809407
p_adj.1
0.0145485279062301
0.00626228887444328
0.152376044329904
gene_name.1
CT_aceE
CT_aceE
CT_aceE
column.1
sum_flexibility_avg
sum_flexibility_avg
sum_flexibility_avg



| aa_char | temp_cat | CT_aceE |CT_aceF| CT_csp|
| -------- | --------| --------|--------|------|
| aa_proline_count_prop_mean | cold-freezing| Text|Text|Text|Text|
|aa_proline_count_prop_mean|temperate|
|aa_proline_count_prop_median|cold-freezing|
|aa_proline_count_prop_median|temperate|
|aa_proline_count_prop_mode|cold-freezing|
|aa_proline_count_prop_mode|temperate|
