## README
The Python script, ‘vcf_ref_extraction.py,’ is available on GitHub (github.com/kbeigel/vcf_ref_extraction). This script takes a .vcf file as input and examines the .vcf file and extracts all of the bases from sites in the reference genome that were identified as SNP sites. These SNPs are kept in the same order as they appear in the .vcf (which is also the same order of SNPs in the PHYLIP file of sample SNP data) and output into a text file. Using Mesquite (v. 3.61), the output reference file (.txt or FASTA) was added back into the PHYLIP file (Maddison and Maddison 2019). As a side note, this script could be tweaked to extract other information stored in the .vcf file such as counts and identities of insertions and deletions that appeared in the variant-calling process (which are not a part of the output SNP dataset; SNPs are the result of TYP == SUB). 

#### 1. Extraction of reference sequence from .vcf
The script was written and desgined to be run in the PyCharm IDE Community Edition 2019.3.3 with the Python 3.8 interpreter on Windows 10; execution of the script may take a couple of minutes since .vcf files can be large. The script contains information about how to call the .vcf file and designate an output file name. The script also prints (to the Run window) the total number of characters output, the number of characters omitted, and the total characters. The sequence in the output file can then be added to the SNP data PHYLIP file containing the sample data. The order of sites is retained (SNP bases are concatenated). Adding the reference to the SNP data file can be done using a program like Mesquite v3.61: Open the SNP PHYLIP/fasta file in Mesquite and add the reference sequence from a .txt or FASTA file.
