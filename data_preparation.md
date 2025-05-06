# Rose pangenome
## step 1. Download available rose genomes
### 1. download [Rosa Chinensis (CH)](https://doi.org/10.1186/s43897-024-00088-1) genome
```
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/041/222/415/GCA_041222415.1_ASM4122241v1/GCA_041222415.1_ASM4122241v1_genomic.fna.gz
wget https://figshare.com/ndownloader/articles/26888665/versions/1
```
### 2. download [Rosa Chinensis (NP)](https://doi.org/10.1038/s41477-018-0166-1) genome
```aiignore
wget https://www.rosaceae.org/rosaceae_downloads/Rosa_chinensis/Rchinensis-genome.v1.0/assembly/OBDH_1.0_formated.fasta.gz
wget https://www.rosaceae.org/rosaceae_downloads/Rosa_chinensis/Rchinensis-genome.v1.0/genes/OBDH_1.0_gene_models.gff3.gz
```
### 3. download [Rosa Chinensis (NG)](https://doi.org/10.1038/s41588-018-0110-3) genome
```aiignore
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/002/994/745/GCF_002994745.2_RchiOBHm-V2/GCF_002994745.2_RchiOBHm-V2_genomic.fna.gz
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/002/994/745/GCF_002994745.2_RchiOBHm-V2/GCF_002994745.2_RchiOBHm-V2_genomic.gff.gz
```
### 4. download [Rosa hybrida](https://doi.org/10.1038/s41477-024-01820-x) genome
```aiignore
wget https://figshare.com/ndownloader/articles/22774097/versions/1
```
### 5. download [Rosa roxburghii (BMC)](https://doi.org/10.1186/s12870-024-05109-1) genome
```aiignore
wget https://download.cncb.ac.cn/gwh/Plants/Rosa_roxburghii_Rr_wild_GWHEROQ00000000/GWHEROQ00000000.genome.fasta.gz
wget https://download.cncb.ac.cn/gwh/Plants/Rosa_roxburghii_Rr_wild_GWHEROQ00000000/GWHEROQ00000000.gff.gz
```
### 6. download [Rosa roxburghii (TPJ)](https://doi.org/10.1111/tpj.16543) genome
```aiignore
wget ftp://ftp.cngb.org/pub/CNSA/data4/CNP0004212/CNS0724874/CNA0069570/Rrox.chr.fa.gz
```
### 7. download [Rosa rugosa (HR)](https://doi.org/10.1038/s41438-021-00594-z) genome
```aiignore
wget https://www.rosaceae.org/rosaceae_downloads/Rosa_rugosa/Rosa_rugosa_v1.0/assembly/Rosa_rugosa_genome.fasta.gz
wget https://www.rosaceae.org/rosaceae_downloads/Rosa_rugosa/Rosa_rugosa_v1.0/genes/Rosa_rugosa_genome.gff3.gz
```
### 8. download [Rosa rugosa (WOR)](https://doi.org/10.12688/wellcomeopenres.22910.1) genome
```aiignore
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/958/449/725/GCF_958449725.1_drRosRugo1.1/GCF_958449725.1_drRosRugo1.1_genomic.fna.gz
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/958/449/725/GCF_958449725.1_drRosRugo1.1/GCF_958449725.1_drRosRugo1.1_genomic.gff.gz
```
### 9. download Rosa rugosa (WUR) genome
```aiignore

```
### 10. download Rosa sericea (WUR) genome
```aiignore

```
### 11. download [Rosa sterilis (TPJ)](https://doi.org/10.1111/tpj.16543) genome
```aiignore
wget ftp://ftp.cngb.org/pub/CNSA/data4/CNP0004212/CNS0724875/CNA0069571/Rster.newchr.fa.gz
```
### 12. download [Rosa wichurana](https://doi.org/10.1093/nsr/nwab092) genome
```aiignore
wget https://www.rosaceae.org/rosaceae_downloads/Rosa_wichuraiana/rwichuraiana_BasyesThornless_v1/assembly/BT.genome.fa.gz
wget https://www.rosaceae.org/rosaceae_downloads/Rosa_wichuraiana/rwichuraiana_BasyesThornless_v1/genes/BT.gene.gff3.gz
```
After downloading the genomes, you can use the following command to extract the genome sequences and annotations from the downloaded files:
```aiignore
gunzip -c <genome_file.gz>
gunzip -c <annotation_file.gz>
```

## Step 2. raw data filtering

### 1. download [pantools-qc-pipeline](https://github.com/PanUtils/pantools-qc-pipeline) from PanUtils
```aiignore
git clone https://github.com/PanUtils/pantools-qc-pipeline.git
```
### 2. modify the config file `pantools-qc-pipeline/config/config.yaml` to specify the input and output directories and parameters for qc. We applied the following parameters:
```yaml
min_len: 5000

longest_isoform: True

orf_size: 30
```
### 3. run the pipeline
```aiignore
snakemake [rule] --use-conda --cores <threads> [--configfile <config>] [--conda-prefix <prefix>]
```
### 4. check the output directory for the filtered data
## step 3. Perform pangenomic analysis using [PanTools](https://pantools.readthedocs.io/en/stable/#)
### 1. build the pangenome using the filtered data
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar build_pangenome -t40 <path_to_database> <input_genomes_file>
```
### 2. add annotations to the pangenome
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar add_annotation -t40 <path_to_database> <input_annotations_file>
```
### 3. add phasing information to the pangenome
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar add_phasing --assume-unphased <path_to_database> <input_phasing_info_file>
```
### 4. perform busco analysis for the pangenome
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar busco_protein  <path_to_database> --odb10=eudicots_odb10 -t=40 --phasing
```
### 5. perform optimal grouping for the pangenome using busco results
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar optimal_grouping <path_to_database> <path_to_database_busco_results> -t=40 --phasing
```
### 6. choose grouping version based on the optimal grouping
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar change_grouping -v=<grouping_version> <path_to_database>
```
### 7. perform pangenome classification
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar gene_classification \
    --mlsa \
    --phasing \
    --sequence <path_to_database>
```
### 8. perform pangenome multiple sequence alignment on subgenome_single_copy_orthologs proteins 
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar msa \
    -t=40 \
    -H <path_to_database_subgenome_single_copy_orthologs.csv> \
    --align-protein <path_to_database>
```
### 9. perform pangenome multiple sequence alignment on subgenome_single_copy_orthologs nucleotides 
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar msa \
    -t=40 \
    -H <path_to_database_subgenome_single_copy_orthologs.csv> \
    --align-nucleotide \
    --trim-using-proteins <path_to_database>
```
### 10. infer a Maximum likelihood phylogeny from SNPs identified from subgenome_single_copy_orthologs
```aiignore
java -Xms200g -Xmx400g -jar pantools-4.3.1.jar core_phylogeny \
    -t=40 \
    -H <path_to_database_subgenome_single_copy_orthologs.csv> \
    --align-nucleotide \
    --phasing <path_to_database>
``` 
