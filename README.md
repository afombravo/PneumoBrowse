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
Go to "full_pipeline" folder and tun 'pipeline.sh', replacing the paths were appropriate:
./pipeline.sh 12 /mnt/d/UNIL/PneumoBrowse/allFromJelleAndDoran/annotation/D39V.fna /mnt/d/UNIL/PneumoBrowse/allFromJelleAndDoran/full_pipeline/S_pneumoniae /mnt/d/UNIL/PneumoBrowse/allFromJelleAndDoran/annotation/GFF_files/D39V_020223_jbrowse.gff3 /mnt/d/UNIL/PneumoBrowse/allFromJelleAndDoran/annotation/GFF_files/D39V_180601_operons.gff3 /mnt/d/UNIL/PneumoBrowse/jbrowse D39V

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
