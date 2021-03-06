More details can be found in the paper **Comparisons of computational methods for differential alternative splicing detection using RNA-seq in plant systems** http://www.biomedcentral.com/1471-2105/15/364/

###Updates:
The ASgenes_by_event folder contains the lists of genes categorized by AS events. Those genes all have two isoforms and excatly one AS event.  

###Prerequisites:
1. Install python 2.7+ but not 3.0+.
2. Install rpy2 (python library).
3. Install scipy (python library).
4. Install numpy (python library).
5. Install Flux Simulator. http://sammeth.net/confluence/display/SIM/2+-+Download
6. Copy myPara.par to the Flux Simulator root directory.
7. Edit myPara.par file for the first two arguments (for details please see Flux Simulator page)
  1. REF_FILE_NAME indicates the location of reference gtf file.
  2. GEN_DIR indicates the folder of reference fasta file. 
8. We are sorry for the fact that this simulation pipeline only accepts gff3 format and Flux Simulator only uses gtf format. Thus you have to prepare both gff3 and gtf formats. 

####Step1: Generate gene-level fragment counts, following Negative Binomial distribution, for two synthetic groups based on real experimental data. 

One group represents control group; the other is for experiment group. Scripts accept two groups of  SAM files and output simulated gene-level fragment counts. The SAM inputs function as starting point in this pipeline and it simulates differential alternative splicing base on that. We recommend for using real data.

usage: 

	cal_NB_counts.py [-h]	<annotation.gff3>	-g1	c1.sam c2.sam 	-g2	t1.sam t2.sam 	[-n 3] 	[-l 2000] 	[-m AS-genes]


Options:

	-h, --help 	show this help message and exit
	
	annotation.gff3		**required**	
	gene annotation file in GFF3 format.
	
	-g1 GROUP1 [GROUP1 ...], --group1 GROUP1 [GROUP1 ...]	**required**	
	First group SAM files separated by space.

	-g2 GROUP2 [GROUP2 ...], --group2 GROUP2 [GROUP2 ...]	**required**	
	Second group SAM files separated by space.

	-n NREPS, --num-reps NREPS	**optional**	
	Number of replicates. Default is 3.

	-l NTARG, --num-target-gene NTARG	**optional**	
	Number of AS genes. Default is 2000.

	-m {AS-genes, all-genes}, --mode {AS-genes, all-genes}	**optional**	
	Choose between AS-genes or all-genes: AS-genes simulates annotated AS genes only; all_genes simulates all genes in 	annotation.

####Output from step 1
group1.nbcounts and group2.nbcounts: simulated NB fragment counts.

group1.rawcounts and group2.rawcounts: raw fragment counts for the input data.

AS-genes.list contains the simulated AS genes.

####Step2: Simulate differentially alternative splicing for each of the two groups individually. 

Usage: DO NOT change the order of the required parameters (first four parameters). 

	python	generate_rnaseq.py	groupx.nbcounts		AS-genes.list 	path-to-myPara.par 	out-prefix 	[-p 0.2]	[-c 25]

Options:

	-h, --help            show this help message and exit
	
	groupx.nbcounts		**required**		
	Output from Step 1, containing the simulated fragments counts following Negative Binomial distribution. 
	
	AS-genes.list		**required**		
	Output from Step 1, containing the differentially AS genes.
	
	path-to-myPara.par	**required**		
	Absolute path for the file myPara.par. Note that myPara.par must be under the Flux Simulator root dir.
	
	out-prefix		**required**		
	Common prefix for all output files. E.g., control_1_1.fq, control_1.bed ...
	
	-p PALT, --percent-alt=PALT	**optional**			
	The percentage of signal coming from alternate splice forms. Default is 0.2

	-c COVERAGE, --mean-base-coverage=COVERAGE	**optional**		
	Mean base coverage. Default is 25

####Output from step 2:

Please look at Flux Simulator pages for output files from step 2.

Copy right: Ruolin Liu, ISU

