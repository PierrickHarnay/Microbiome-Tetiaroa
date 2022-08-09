# Microbiome-Tetiaroa
This study was conducted on the Tetiaroa atoll in French Polynesia. The purpose of the study is to show the impact/benefit of the deratting of the island. 
A total of 3 motus are studied (Reiono, Rimatu'u and Aie). Within these motus Reiono as well as Aie do not have rats while Rimatu'u has been deratted during 2022. Two zones were defined per motus. On each motus coral samples as well as water samples were sampled.

# Analysis Tetiaroa Microbiome corals seqs

### Harnay Pierrick, Hannah Epstein 08092022

# 1. Prepare data files on Andormeda 

Log onto Andromeda and start data folder 

```
cd/data/putnamlab/pharnay
cd Microtetia/raw
ls
```

Ask Hannah if she want permissions for all files

```
chmod u=rwx,g=rwx, o=rwx,a=rwx -R Microtetia
```

Now we need to check the quality information for our sequences.
We going to used our fastq.gz files for all our samples.

# 2. Generate MultiQC of raw files 

# Overview: 
This step generates a report that tells us about the quality of our sequences.

## Code: 
Run fastqc on the rax files and genrates a multiQC report 

In the `Microtetia`folder, make a folder `fastqc_results`

Now we need to start the script.

```
nano multiqc.sh 
```

Copy this informations on the bioinfo 

```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mean=200GB
#SBATCH --mail-type=BEGIN,END,FAIL 
#SBATCH --mail user=pierrick.harnay@uri.edu
#SBATCH --account=putnamlab
#SBATCH --error="multiqc_error_script"
#SBATCH --output="multiqc_output_script"
```
load the necessary programs 

```
source /usr/share/Modules/init/sh #load the module fonction

module load FastQC/0.11.9-Java-11
module load MultiQC/1.9-intel-2020a-Python-3.8.2

#run the fastQC on every samples
for file in ./raw/*fastqc_results
done 

multiqc --interactive./fastqc_results

mv multiqc_report.html./fastqc_results/microtetia_tagseq_raw_qc_report.html
#renames file
```

```
sbatch multiqc.sh
```

check the status of the job. 

```
squeue -u pierrick_harnay
```

Once the job is done, we want to view the newly made file.

For this, open a new terminal. Output file to local computer and view.

```
scp pierrick_harnay@ssh3.hac.uri.edu:/data/putnamlab/pharnay/Microtetia/fastqc_results/microtetia_tagseq_raw_qc_report.html /users/pierrickharnay/Dropbox/MyProjects/MicrobioTetia_20212022/fastQC/Output

```

# 3. Merge files for each sample across lanes.

## Overview
This step merge all files for each sample so taht we have one sequence file for each coral or water.

## Code:

Merge all lanes

In the Microtetia folder:

```
nano merge.sh

```

```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=200GB
#SBATCH --mail-type=BEGIN,END,FAIL 
#SBATCH --mail-user=pierrick_harnay@uri.edu 
#SBATCH --account=putnamlab 

ls -1 ./raw/...*R1*gz | awk -F '-''{print $1"_"$2}' | sort | uniq > ID
or i in `cat ./ID`; 

do cat "$i"_L001_R1_001.fastq.gz "$i"_L002_R1_001.fastq.gz > "$i"_R1_cat.fastq.gz; 

done;
```

```
sbatch merge.sh

```
Check that we have new "cat" sequences with `cd raw/` and `ls`.

# 4. Conduct QC and filtering of sequences
## Overview:

This step trims and cleans our reads and generates a new QC report taht shows our clean sequence information. 

## Code: 

In the Microtetia folder:
```
nano qc.sh

```

```
#!/bin/bash
#SBATCH -t 24:00:00
#SBATCH --nodes=1 --ntasks-per-node=1
#SBATCH --export=NONE
#SBATCH --mem=100GB
#SBATCH --mail-type=BEGIN,END,FAIL 
#SBATCH --mail-user=pierrick_harnay@uri.edu 
#SBATCH --account=putnamlab                  
#SBATCH --error="qc_script_error" #SBATCH --output="qc_output_script" 

#load module needed
module load fastp/0.19.7-foss-2018b
module load FastQC/0.11.8-Java-1.8
module load MultiQC/1.9-intel-2020a-Python-3.8.2

cd raw

#Make an array of sequences to trim 
array1=($(ls *cat.fastq.gz))

# fastp loop; trim the Read 1 TruSeq adapter sequence; trim poly x default 10 (to trim polyA) 

```



