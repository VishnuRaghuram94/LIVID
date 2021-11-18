# LIVID
#### **Locus specIfic Vairant IDentifier**
###### LIVID is a tool for extracting a locus from any given bacterial genome sequence and identifying variants when compared to a reference locus.

---

# WORKFLOW:

![LIVID Workflow](https://github.com/VishnuRaghuram94/LIVID/blob/main/livid_workflow.png)

LIVID accepts a query genome, a reference genome and primers flanking the region of interest as inputs. LIVID then extracts the region of interest from the query genome using in-silico PCR and identifies variants in this region when compared to the reference genome. LIVID is designed to compare a single gene/operon across 1000s of microbial genomes. 

LIVID is based on [AgrVATE](https://github.com/VishnuRaghuram94/AgrVATE) and therefore has very similar installation instructions, prerequisites, usage instructions and output files. 

---

# INSTALLATION:

Please see the [PREREQUISITES](#PREREQUISITES) section for all the tools required to run LIVID. For ease of use, I recommended you install LIVID using [Conda](https://conda.io/en/latest/). LIVID will be uploaded to Bioconda soon. 

	
	conda create -n livid -c vishnuraghuram94 livid
	conda activate livid
	
This will install all necessary dependencies EXCEPT Usearch.  	
Due to Usearch's license, it cannot be provided with the conda installation. Please download and extract usearch11.0.667 (osx32 or linux32) from [here](https://www.drive5.com/downloads/) and add it to your PATH
	
For example (Use the version appropriate for your operating system):
	
	
	curl "https://www.drive5.com/downloads/usearch11.0.667_i86linux32.gz" --output usearch11.0.667_i86linux32.gz #Downloads usearch binary
	
	gunzip usearch11.0.667_i86linux32.gz #Decompresses usearch binary
	
	chmod 755 usearch11.0.667_i86linux32 #Changes permissions to executable
	
	cp ./usearch11.0.667_i86linux32 $(dirname "$(which livid)") #Copies usearch binary to the same directory as livid 

***NOTE:*** Currently, only the 32-bit version of usearch is free to use. This version is not supported by WSL or MacOS (post-Catalina). Therefore, it is recommended to use LIVID on Linux machines or older versions MacOS. 

---
	
# PREREQUISITES:

* __[Usearch 32 bit linux](https://drive5.com/usearch/)__  
Robert C. Edgar, Search and clustering orders of magnitude faster than BLAST, Bioinformatics, Volume 26, Issue 19, 1 October 2010, Pages 2460–2461, https://doi.org/10.1093/bioinformatics/btq461
	
* __[NCBI blast+](https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/LATEST/)__  
Camacho, C., Coulouris, G., Avagyan, V. et al. BLAST+: architecture and applications. BMC Bioinformatics 10, 421 (2009). https://doi.org/10.1186/1471-2105-10-421
	
* __[Snippy](https://github.com/tseemann/snippy)__  
Seemann T (2015). Snippy: fast bacterial variant calling from NGS reads. https://github.com/tseemann/snippy

* __[SeqKit](https://bioinf.shenwei.me/seqkit/)__  
Shen W, Le S, Li Y, Hu F (2016) SeqKit: A Cross-Platform and Ultrafast Toolkit for FASTA/Q File Manipulation. PLoS ONE 11(10): e0163962. https://doi.org/10.1371/journal.pone.0163962
	
---

# USAGE:

	livid -i filename.fasta -r reference.fasta -p primers.fasta --minamp <int> --maxamp <int> --maxdiff <int> [options]
	
* FLAGS: 
  * ```-i```&nbsp;&nbsp;&nbsp;REQUIRED: Input genome in FASTA format [alternate: ```--input```]
  * ```-r```&nbsp;&nbsp;&nbsp;REQUIRED: Reference locus/gene sequence in GENBANK or FASTA format (Use genbank for annotated frameshifts) [alternate: ```--reference```]
  * ```-p```&nbsp;&nbsp;&nbsp;REQUIRED: File containing forward and reverse primer in FASTA format [alternate: ```--primers```]
  * ```-d```&nbsp;&nbsp;&nbsp;REQUIRED: Integer for maximum number of primer mismatches allowed [alternate: ```--maxdiff```]
  * ```-x```&nbsp;&nbsp;&nbsp;REQUIRED: minamp parameter for usearch (minimum size of locus to be extracted) [alternate: ```--minamp```]
  * ```-y```&nbsp;&nbsp;&nbsp;REQUIRED: maxamp parameter for usearch (maximum size of locus to be extracted) [alternate: ```--maxamp```]
  * ```-f```&nbsp;&nbsp;&nbsp;Force overwrite existing results directory [alternate: ```--force```]
  * ```-h```&nbsp;&nbsp;&nbsp;Print this help message and exit [alternate: ```--help```]
  * ```-v```&nbsp;&nbsp;&nbsp;Print version and exit [alternate: ```--version```]
  

*LIVID supports a single FASTA file as input, but the file can be a multi-fasta file. To run multiple genomes, it is recommended to keep them as separate files in a common directory.*  
*For example*:

	ls fasta_files/* | xargs -I {} livid -i {} -r {} -p {} --minamp <int> --maxamp <int> --maxdiff <int> [options]
	
---

# OUTPUTS:

### RESULTS: 
		
A new directory with suffix ```-results```  will be created where all the following files can be found


* __**fasta-frameshifts.tab:**__  
	Frameshift mutations in CDS of extracted locus detected by Snippy. 
	
		col 1: Filename
		col 2: Position of variant on reference sequence
		col 3: Type of frameshift
		col 4: Effect of mutation
		col 5: Gene
		
* __**fasta-pcr_extracted.fna:**__  
	Locus extracted from in-silico PCR using USEARCH -SEARCH_PCR in fasta format
		
* __**fasta-pcr-log.tab:**__  
	Standard output of [USEARCH -SEARCH_PCR](https://drive5.com/usearch/manual/cmd_search_pcr.html)
	
* __**fasta-snippy_log.txt:**__  
	Standard output of [Snippy](https://github.com/tseemann/snippy)
		
* __**fasta-snippy/**__  
	All output files of [Snippy](https://github.com/tseemann/snippy)

# Author 

* Vishnu Raghuram
