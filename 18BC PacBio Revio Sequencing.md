---
title: 18BC PacBio Revio Sequencing

---

# 18BC PacBio Revio Sequencing
scp fastq fileto Farm:
```
scp m84066_240413_062748_s1_fastq.zip \
kmrcello@farm.cse.ucdavis.edu:/home/kmrcello/cyanobacteria/18BC
```
unzip file:
```
unzip  m84066_240413_062748_s1_fastq.zip
```
create environment and install tools
```
conda create -n bioinfo -c conda-forge -c bioconda \
    fastp \
    flye \
    quast \
    metabat2 \
    prokka \
    minimap2 \
    samtools \
    bwa \
    checkm-genome \
    seqtk
```

Quality control: **fastp**
```
fastp -i m84066_240413_062748_s1.18BC.54_54.fastq -o cleaned.fastq -h fastp_report.html --disable_adapter_trimming
```
Assembly: **flye**
```
seqtk sample -s100 cleaned.fastq 75000 > cleaned_subsampled.fastq

flye --pacbio-hifi cleaned_subsampled.fastq --out-dir flye_out --genome-size 6m
```
Quality assessment of assembly: **quast**
```
quast flye_out/assembly.fasta -o quast_report
```
Binning:
* map reads back: **minimap2**, **samtools**
```
minimap2 -ax map-pb flye_out/assembly.fasta cleaned.fastq | samtools view -bS - > aligned_reads.bam
samtools sort aligned_reads.bam -o sorted_reads.bam
samtools index sorted_reads.bam
```
issues with an error
```
jgi_summarize_bam_contig_depths --outputDepth depth.txt sorted_reads.bam
```
* Bin: **metabat2**
```
metabat2 -i flye_out/assembly.fasta -a depth.txt -o bins/bin
```
* ~~Bin: **metabat2**~~
~~metabat2 -i flye_out/assembly.fasta -a sorted_reads.bam -o bins/bin~~

Bin quality assessment: **checkM**
```
checkm lineage_wf -x fa bins/ checkm_out
```
annotation: **prokka**
```
prokka --outdir prokka_out --prefix mygenome flye_out/assembly.fasta
```

taxonomy: **GTDBtk**
```
conda create -n km-gtdbtk -c conda-forge -c bioconda gtdbtk=2.4.0
```
```
Automatic:

        1. Run the command "download-db.sh" to automatically download and extract to:
            /home/kmrcello/.conda/envs/km-gtdbtk/share/gtdbtk-2.4.0/db/
            
download-db.sh
```
7/8/24 - waiting for download
```
gtdbtk classify_wf --genome_dir bins --out_dir gtdbtk_output --cpus 8 
```
```
conda env config vars set GTDBTK_DATA_PATH="/home/kmrcello/.conda/envs/km-gtdbtk/share/gtdbtk-2.4.0/db"
```
**31 HMM work**
download HMM from eggnog

```
scp Downloads/COG0290_hmm.txt kmrcello@farm.cse.ucdavis.edu:/home/kmrcello/cyanobacteria/18BC
```
```
hmmpress COG0290_hmm.txt
```
```
for file in split_bins/*.fa; do
  hmmscan --tblout results_$(basename $file .fa).tbl COG0290_hmm.txt $file
done
```
```
cat results_*.tbl > combined_results.tbl
```
what I did was just find the genes in the prokka_out directory

**Chapter 1 analysis**
in Farm
```
anvi-get-sequences-for-hmm-hits --external-genomes external-genomes.tsv \
                                -o real-concatenated-proteins.fa \
                                --hmm-source Bacteria_71 \
                                --return-best-hit \
                                --get-aa-sequences \
                                --concatenate 
```
```
anvi-gen-phylogenomic-tree -f real-concatenated-proteins.fa \
                           -o real-phylogenomic-tree.txt
```
```
anvi-interactive -p phylogenomic-profile.db \
                 -t real-phylogenomic-tree.txt \
                 --title "Phylogenomics Tutorial Example #1" \
                 --manual
```

```
bin.2.fa      bin_2_MEM.fa  bin_2_cyano.db   ctg_hmms       photo_hmms
bin_2_CTG.fa  bin_2_PS.fa   bin_2_marker.fa  membrane_hmms
```
scp'd to local

```
import os
import csv
from Bio import SeqIO
from Bio.SeqUtils.ProtParam import ProteinAnalysis

# Define the fasta file to process and the output directory
fasta_file = "bin.2.fa"
output_file = "timaviella_genes.csv"

# Define the header for the CSV file
header = ['name', 'aa_proline_count', 'aa_glycine_count', 'aa_serine_count', 'aa_arginine_count', 'aa_lysine_count', 'aa_count', 
          'calc_aliphatic_percent_sum', 'calc_acidic_percentage_sum', 'calc_aromaticity', 'calc_flexibility', 'calc_flexibility_sum', 
          'calc_flexibility_avg', 'calc_gravy', 'sequence']

# Open the fasta file and the output CSV file
with open(fasta_file) as handle, open(output_file, 'a', newline='') as output:
    writer = csv.writer(output)

    # Check if the file is empty by checking its size
    is_empty = os.path.getsize(output_file) == 0

    # Write header if the file is empty (first time)
    if is_empty:
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
        aliphatic_index = ['A', 'I', 'L', 'V']
        acidic = ['D', 'E']
        aliphatic_percent_sum = sum(x.get_amino_acids_percent().get(aa, 0) for aa in aliphatic_index)
        acidic_percentage_sum = sum(x.get_amino_acids_percent().get(aa, 0) for aa in acidic)
        aromaticity = x.aromaticity()
        flexibility = x.flexibility()
        flexibility_sum = sum(flexibility)
        flexibility_avg = flexibility_sum / len(flexibility)
        gravy = x.gravy()

        # Write the data row to the CSV file
        data = [record.id, p_count, g_count, s_count, r_count, k_count, aa_count, aliphatic_percent_sum, 
                acidic_percentage_sum, aromaticity, flexibility, flexibility_sum, flexibility_avg, gravy, 
                sequence]
        writer.writerow(data)


```

### August 2, 2024
new tree

```
for i in `ls *fna | awk 'BEGIN{FS=".fna"}{print $1}'`
do
    anvi-script-reformat-fasta $i.fna -o $i.fa -l 0 --simplify-names
    anvi-gen-contigs-database -f $i.fa -o $i.db -T 4
    anvi-run-hmms -c $i.db -I Bacteria_71
done
```
```
for i in `ls *db | awk 'BEGIN{FS=".db"}{print $1}'`
do
    anvi-run-hmms -c $i.db -H ctg_hmms
done
```
```
for i in `ls *db | awk 'BEGIN{FS=".db"}{print $1}'`
do
    anvi-run-hmms -c $i.db -H photo_hmms
done
```
```
for i in `ls *db | awk 'BEGIN{FS=".db"}{print $1}'`
do
    anvi-run-hmms -c $i.db -H membrane_hmms
done
```
```
anvi-get-sequences-for-hmm-hits --external-genomes external-genomes-2.tsv \
                                -o two-S16-concatenated-proteins.fa \
                                --hmm-source Bacteria_71 \
                                --gene-names Ribosomal_S16 \
                                --return-best-hit \
                                --get-aa-sequences \
                                --concatenate 
```
```
anvi-gen-phylogenomic-tree -f two-S16-concatenated-proteins.fa \
                           -o two-S16-phylogenomic-tree.txt
```
```
anvi-interactive -p phylogenomic-profile.db \
                 -t two-phylogenomic-tree.txt \
                 --title "Phylogenomics Tutorial Example #1" \
                 --manual
```













