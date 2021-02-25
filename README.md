# vcf_ref_extraction

## Bioinformatics pipeline for SNP data

For SNP data sets of T. arizonensis and T. pomonae ants and associated fungi, RAxML was used in Linux commandline (Stamatakis 2014). The data sets returned from SNPsaurus nextRAD & Genome GBS Library Preparation Service were: PHYLIP files, variant call format (.vcf) files, and text files (with statistics). The PHYLIP files contained SNPs from all of the samples. As previously described, a SNP occurs anywhere at least one of the samples differs from the reference sequence. The reference sequence, however, was not included in the PHYLIP file. The .vcf file is a record of the contigs and scaffolds used from the reference sequence, and it contains coded information about where SNPs were identified. In some cases, it may be of interest to use the bases at the SNP sites from the reference genome as an outgroup. In the event that this is not readily available from the genomics center, it is possible to extract a sequential list of all of the SNP sites and incorporate this into the PHYLIP (or FASTA) file containing all of the SNPs from the .vcf file.

The Python script, ‘vcf_ref_extraction,’ is available on GitHub (github.com/kbeigel/vcf_ref_extraction). This script takes a .vcf file as input and examines the .vcf file and extracts all of the bases from sites in the reference genome that were identified as SNP sites. These SNPs are kept in the same order as they appear in the .vcf (which is also the same order of SNPs in the PHYLIP file of sample SNP data) and output into a text file. Using Mesquite (v. 3.61), the output reference file (.txt or FASTA) was added back into the PHYLIP file (Maddison and Maddison 2019). As a side note, this script could be tweaked to extract other information store in the .vcf file such as counts and identities of insertions and deletions that appeared in the sequencing process (which are not a part of the output SNP dataset). 
The raxml_ascbias.py Python script (github.com/btmartin721/raxml_ascbias) was run to evaluate the number of invariant sites and remove them. This script produces a PHYLIP file with invariant sites removed. The script also produces a text file with the number of invariant sites that were removed. This script was run from command line, and its outputs of variant-only SNP data and number of removed sites were used in RAxML. RAxML (version 8.2.11) was used to construct phylogenies based on this variant-only SNP data set (Stamatakis 2006, 2014).

# 1. Extraction of reference sequence from .vcf
For reference sequence extraction, the script was run in the PyCharm IDE Community Edition 2019.3.3 with the Python 3.8 interpreter on Windows 10; execution of the script took a couple of minutes. The script contains information about how to call the .vcf file and designate an output file name. The script also prints (to the Run window) the total number of characters output, the number of characters omitted, and the total characters. The sequence in the output file was then added to the SNP data PHYLIP file containing the sample data. This action was done using Mesquite v3.61 by opening the PHYLIP file in Mesquite and adding the sequence from a .txt or FASTA file [15]. The SNP alignment was exported to a PHYLIP file from Mesquite and used for the rest of the methods [15].

# 2. Removal and count of invariant sites
The raxml_ascbias Python script (github.com/btmartin721/raxml_ascbias) was run to evaluate the number of invariant sites. This script produces a PHYLIP file with invariant sites removed (Fig. S2). The script also produces a text file with the number of invariant sites that were removed. This script was run from Unix command line using the follow command:

$ python ascbias.py -p phylipInputFileName -o phylipOutputFileName

# 3a. Make a partition file for the invariant sites
Choose a correction method (felsenstein or stamatakis were used here; lewis is also available but does not require a partition file)
The partition file set-up consists of two text files: part.txt and p1.txt
part.txt should contain: information about the partition files(s) with information on the invariant site count(s), a designation of the type of data (DNA used here), the partition range (only one partition here)
the part.txt file:

	[asc~p1.txt], ASC=DNA, p1=1-96319

# 3b. Make a partition file for the invariant sites: felsenstein
felsenstein correction: the number of invariant sites is a total sum of the invariant sites removed
the p1.txt file contains a single integer, the number of invariant sites
example of a p1.txt file:

	205127

# 4a. RAxML rapid hill-climbing maximum likelihood search
Rapid hill-climbing maximum likelihood search, recommended 200-1000 replicates. 

$ raxmlHPC-PTHREADS-SSE3 -T 20 -f d -# 1000 -p 12345 -m ASC_GTRGAMMA --asc-corr=felsenstein -q part.txt -s alignment_ascbias.phy -n NAME_ascbias_bestTree_1000-MLrep.txt


# 4b. RAxML autoMRE to produce bootstrap replicates

$ raxmlHPC-PTHREADS-SSE3 -# autoMRE -T 20 -x 12345 -p 23445 -e 0.001 -m ASC_GTRGAMMA  --asc-corr=felsenstein -q part.txt -s alignment_ascbias.phy 
-n NAME_ascbias_BSreplicates.txt



# 4c. RAxML mapping of bootstraps to best maximum likelihood tree
$ raxmlHPC-PTHREADS-SSE3 -T 20 -f b -p 12234 -m ASC_GTRGAMMA 		             -t RAxML_bestTree.NAME_ascbias_bestTree_1000_MLrep.txt           
-z RAxML_bootstrap.NAME_ascbias.BSreplicates.txt -n NAME_BS_Tree



 
# 4d. Descriptions of RAxML flags used in code

-T numberOfThreads -f algorithm -# numberOfRuns|autoMRE 					 -x rapidBootstrapRandomNumberSeed -p parsimonyRandomSeed -e likelihoodEpsilon           -m substitutionModel –asc-corr=ascBiasModel -q partitionFileName –s inputAlignmentName  -n outputFileName -z multipleTreesFile
 
-e should be set to 0.001 for models that use proportion of invariant sites estimate
-s input file should be file from ascbias script that has invariant sites removed
-p part.txt file should be the partition file
-f should be set to d for rapid hill-climbing tree search algorithm
-f should be set to b to draw bipartition information onto -t tree (best ML tree) based on bootstrapping file designated by -z

 
# 5. Visualization of trees Interactive Tree of Live (iTOL) v4
iTOL can be used to display, edit, and annotate trees from a variety of input filetype (Letunic and Bork 2019). iTOL was used to edit taxa names and re-root the tree using the reference sequence (Letunic and Bork 2019). 
