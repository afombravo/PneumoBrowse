# PneumoBrowse
Repository for the PneumoBrowse pipeline


PneumoBrowse (PB) was originally created for the strain D39V by Jelle Slager, Rieza Aprianto and Jan-Willem Veening. The accompanying paper can be found here: https://doi.org/10.1093/nar/gky725


More recently, new features have been added. These can be checked by accessing PB and clicking on the 'important information' tab on the top right corner.
https://veeninglab.com/pneumobrowse


Besides new features, more *Streptococcus pneumoniae* strain were added: 
670-6B; AP200; BHN837; EF3030; G54; NCTC7465; P1031; PMEN1; PMEN19; PMEN25; PMEN28; PMEN3; PMEN31; Taiwan19F; VL1312; VL1313; TIGR4


## HOW TO INSTALL AND RUN JBROWSE LOCALLY:
OPEN LINUX TERMINAL AND GO AT IT AS FOLLOWS:

### 0.
Get your system ready, and fix any underlying issues:
sudo apt update

sudo apt upgrade

### 1.
Open new cmd line and install nvm (NODE.JS):

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash

chmod +x ~/.nvm/nvm.sh

source ~/.bashrc

nvm install 14 #version may vary depending on OS. Can use 16, 18.


### 2.
Open new cmd line and install cpanm for pearl modules:

sudo apt install cpanminus


### 3.
Install some Pearl modules:

sudo apt-get install expat

sudo apt-get install libexpat1-dev

sudo cpanm XML::Parser

sudo cpanm Bio::DB::SeqFeature::Store


### 4.
Download correct JBrowse version to the directory that will be your final working directory:

git clone https://github.com/gmod/jbrowse

cd jbrowse

git checkout 1.16.11-release  # or version of your choice


### 5.
install plugins. run:

cd plugins

git clone https://github.com/bhofmei/jbplugin-strandedplot.git StrandedPlotPlugin

git clone https://github.com/bhofmei/jbplugin-hierarchicalcheckbox.git HierarchicalCheckboxPlugin

git clone https://github.com/erasche/jbrowse-dark-theme.git jbrowse-dark-theme (this one is buggy)

git clone https://github.com/TAMU-CPT/SitewideNotices.git SitewideNotices

git clone https://github.com/bhofmei/jbplugin-screenshot.git ScreenShotPlugin


### 6.
go back to jbrowse folder and re-run:

cd ..

sudo ./setup.sh


### 7.
Install bedGraphToBigWig in jbrowse bin folder:

cd /bin

rsync -aP rsync://hgdownload.soe.ucsc.edu/genome/admin/exe/linux.x86_64/bedGraphToBigWig ./

chmod +x ./bedGraphToBigWig


### 8.
Install wigToBigWig in jbrowse bin folder:

cd /bin

rsync -aP rsync://hgdownload.soe.ucsc.edu/genome/admin/exe/linux.x86_64/wigToBigWig ./

chmod +x ./wigToBigWig


### 9.
Go to "full_pipeline" folder and tun 'pipeline.sh', replacing the paths were appropriate (see date update instructions for more detailed instructions):

./pipeline.sh /PneumoBrowse_compiled/full_pipeline/data/fasta/compiled.fna /PneumoBrowse_compiled/full_pipeline/ /PneumoBrowse_compiled/full_pipeline/data/annotations/excel/compiled_current_jbrowse.gff3 /PneumoBrowse_compiled/full_pipeline/data/annotations/excel/D39V_180601_operons.gff3 /PneumoBrowse_compiled/jbrowse output

### 10.
Start localserver by going to the original instalation folder of jbrowse:

npm run start  # starts a express.js dev server on port 8082, 


### Outro.
After each update, hyperlinks will be broken. 

To fix this, the trackList.json file from jbrowse direcory must be updated to include new lines.
Every time a new track with a feautre is added, a new field must be added as well to correcly parse the hyperlink.
The working model .json file is on full_pipeline and is automatically copied every time an update is performed by running the bash update script, atm.

The adding of new feature HTML must be done as follows:

"fmtDetailValue_[NAME_OF_THE_FIELD]": "function(html) { return unescape(html); }"

E.g:

"fmtDetailValue_Supporting_literature": "function(html) { return unescape(html); }"



## HOW TO UPDATE PNEUMOBROWSE DATA:

By Jelle Slager, 2019 (Readapted by Afonso Bravo, 2023)

################ Explanation of information contained in annotation excel file:

################ Since GFF3 and gbk formats have different requirements, most redundancies in the table are necessary. Be careful removing columns.

################ Note by Afonso: Some of these columns (most, actually), currently only have entries for D39V.

################ The first set of columns match GFF3 format:

contig_id: 			ID of contig, has to match ID in fasta file of genome

source:		 		Name of the program that generated this feature, or the data source (database or project name)

type: 				Type of feature. Must be a term or accession from the SOFA sequence ontology

start:				Start position of the feature, with sequence numbering starting at 1

stop:				End position of the feature, with sequence numbering starting at 1

strand:				Defined as + (forward) or - (reverse) [NOTE THERE IS NO COLUMN MATCHING THE SCORE COLUMN FROM GFF3 FORMAT]

old_locus_tag:		Locus tag (for D39V, as reported in D39W (Winkler lab, Lanie et al., 2007))

locus_tag:			Locus tag

name:				Name of feature, make sure that this is unique for genes (i.e. only one gene can be named 'dnaA')

synonyms:			Alternative name for this feature. Here, overlap with other genes is accepted. Multiple synonyms are separated by '%2C'. Entries in this column are also used in PneumoBrowse's search function

R6_equivalent:		Equivalent locus in R6. Entries in this column are also used in PneumoBrowse's search function

R6_note:			Note mentioning how D39V and R6 locus relate to each other

TIGR4_equivalent:	Equivalent locus in TIGR4. Entries in this column are also used in PneumoBrowse's search function

TIGR4_note:			Note mentioning how D39V and TIGR4 locus relate to each other

obsolete_D39:		D39W (Winkler) locus/loci that overlap with the annotated gene. Different from old_locus_tag. Used when annotated feature type deviates (e.g. D39V: CDS, D39W: pseudogene). Entries in this column are also used in PneumoBrowse's search function

function:			Function/description of feature

gene_note:			Note(s) concerning the gene (NOT the encoded entity, see below). Multiple notes are separated by '%3B'

notes:				Note(s) concerning the encoded entity (NOT the gene, see above). Multiple notes are separated by '%3B'

essentiality:		Indication of whether gene is essential or not, based on several studies. Four options: 'Probably essential' (essential in all studies), 'Possibly essential' (essential in >=1 study), 'Probably not essential' (not essential in any study), 'Insufficient data on essentiality'

feature_id:			Unique identifier of feature, no hard restriction other than it being unique

parent:				Applies only to features of type repeat_unit: every detected repeat region is at least annotated as 'repeat_region'. Optionally, subelements of a repat_region can be annotated as repeat_unit (e.g. boxA, boxB, boxC). This column contains the feature_id of the containing repeat_region

protein_id:			Some unique identifier of protein-encoding genes of form 'gnl|UNIL|[locus tag]'

protein_accession:	NCBI identifier for protein, provided after submission to genbank. This is needed when you want to update a specific feature

EC_code:			EC number as assigned by RAST annotation tool

figfam:				figfam identifier as assigned by InterProScan

TIGRFAM:			TIGRfam identifier as assigned by InterProScan

GO:					GO identifier as assigned by InterProScan

KEGG:				KEGG identifier as assigned by InterProScan

IPR:				InterPro identifier as assigned by InterProScan

Pfam:				Pfam identifier as assigned by InterProScan

Regulon:			Indicate of which regulons this gene is a member. Based on Transcription Factor Binding sites from RegPrecise and on sites reported in PneumoBrowse paper.

Rfam:				RFAM identifier as assigned by me (Jelle) after RFAM search

ISFinder:			ISFinder identifier as assigned by me (Jelle) after ISBlast


################ The next couple of columns are needed for genbank format:

anticodon:			tRNA anticodon

bound_moiety:		required for gbkey 'protein_bind' and gbkey 'regulatory' with regulatory_class 'riboswitch'

gbkey:				Type of feature. Should follow INSDC guidelines. Note that pseudogenes are 'gene' here and only identified in the 'pseudogene' column (This will generate new tracks on the Annotation tab depending on how the feature type is parsed by xls2gff3.py)

regulatory_class:	Only if gbkey='regulatory'. See http://www.insdc.org/controlled-vocabulary-regulatoryclass

ncRNA_class:		Only if gbkey='ncRNA'. See http://www.insdc.org/rna_vocab.html

direction:			Only if gbkey='rep_origin'.

mobile_element_type:	Only if gbkey='mobile_element'. Should be of form 'insertion sequence:[ID]'

pseudogene:			Should contain a value from http://www.insdc.org/documents/pseudogene-qualifier-vocabulary if a gene is a pseudogene

gene_biotype:		Feature type, should be part of SOFA nomenclature: https://github.com/The-Sequence-Ontology/SO-Ontologies/blob/master/subsets/SOFA.obo

score:				Only needed for TSSs and terminators. Indicates TSS abundance and terminator efficiency, respectively

PMID:				Relevant PubMed IDs, separated by '%2C'

nucleotide_sequence:	Nucleotide sequence of feature

aa_sequence:		Amino acid sequence of protein


NOTE: some of the downstream tools use commas or semicolons as separators (e.g. GFF format). Therefore convert all commas in the excel file by '%2C' and all semicolons by '%3B'

NOTE: all 'empty' cells are filled with '.'


## Instructions for updating the annotation of Streptococcus pneumoniae D39V and the accompanying PneumoBrowse session:

In annotation folder:

1. Update the annotation excel file 'compiled_current.xlsx'
2. Save this file and also save as 'compiled_current.xlsx'
3. Open file xls2gff3.py: 
	- Change the date at the top of the file. This is typically set to: 'current'. When making an official update to PneumoBrowse, change to the date corresponding to the most recent Excel file.
	- Run xls2gff3.py
4. Copy the produced 'GFF_files/compiled_current_jbrowse.gff3' to a server-accessible location
5. Make sure the 'GFF_files/D39V_[OLDERDATE]_operons.gff3' is also available there (This corresponds to the operons from D39V only)

Move full_pipeline folder to server, make sure it can access both GFF files and the JBrowse folder.
In the full_pipeline folder:
1. Open 'pipeline.sh':
	- Comment out everything preceding '### Set-up JBrowse folder'
	- For future reference I keep the most recently applicable command for running the script at the top of the file. It is advisable to maintain two JBrowse sessions, one for the public and one for testing.
	- Adjust the input files in the command to the most recent ones.
	- When unsure whether it is going to work, first try running the script with your private JBrowse session set as 'jbrowse_folder'
	- Save 'pipeline.sh'
2. Make sure 'dojo.js', 'functions.conf', jbrowse.conf', 'notices.json', 'trackList.json' are up to date. So: add the new update to 'notices.json'
3. Run the pipeline
4. Update https://veeninglab.com/pneumobrowse-update-history accordingly (good luck).

## Explanation of pipeline files
The pipeline copies 'dojo.js', 'functions.conf', jbrowse.conf', 'notices.json', 'trackList.json' to the JBrowse folder. Therefore, you hardly ever have to change anything within the JBrowse folder. Instead, you can make your changes to your local copies of these files, which will then be copied across.

NOTE: sometimes JBrowse has some issues with cache management. If this happens, remove the 'data' folder within the JBrowse folder and rerun the pipeline.

In 'dojo.js' (which is a nightmare of a file), I made minor changes. One of these changes the color of arrows from black to white. Just compare to the original file to find location of changes.

'functions.conf' contains the rule that determines the color of forward/reverse/pseudogene features. Note that the 'customGlyph' function is not actually used in PneumoBrowse.

'jbrowse.conf' is quite similar to the original version, but includes lines required for activation of plugins and to set 'sortHierarchical' to 'false'.

'trackList.json' properly encodes the hyperlinks that some fields require. Everytime a hyperlink field is created, it must be manually changed in this file.

'notices.json' is the file that contains messages for users, through the SitewideNotices plugin. Update this file to add new messages.

