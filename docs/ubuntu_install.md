###Installation on Linux (Ubuntu)

You will need working knowledge of terminal and command line utilities in order to install/run funannotate.  By far the most challenging aspect is installing all of the dependencies correctly. You will also need to have `sudo` privileges to get all of these tools installed.  I'm going to use LinuxBrew to install a few of these packages, note there are other ways (notably sudo-apt), however Linux Brew provides up to date binaries for many science based programs and for a non-computer scientist (me) I find it easier to use.  Note, you may have most if not all of these dependencies installed on a server, thus you can skip ahead to install funannotate if you know what you are doing.  The setup script will help you determine what is not installed.

####Step-by-step instructions:


1) install dependencies (instructions for brand new Ubuntu box, i.e. Virutal box)
```
#now install libraries and necessary files
sudo apt-get update
sudo apt-get install -y build-essential
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install -y git cmake
sudo apt-get install python-dev python-setuptools python-pip
sudo apt-get install libatlas-base-dev libfreetype6-dev libz-dev libboost-iostreams-dev libpng-dev pkg-config curl
sudo apt-get install python-numpy python-scipy python-pandas python-matplotlib python-biopython python-psutil python-sklearn
sudo apt-get install bioperl cpanminus
```

2) Install perl modules via cpanm or cpan or manually
```
cpanm Getopt::Long Pod::Usage File::Basename threads threads::shared \
        Thread::Queue Carp Data::Dumper YAML Hash::Merge Logger::Simple Parallel::ForkManager \
        DBI Text::Soundex Scalar::Util::Numeric
```

3) Install LinuxBrew
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Linuxbrew/linuxbrew/go/install)"

#add to ~/.bash_aliases
sudo gedit ~/.bash_aliases

#add the following and save, reload terminal
export PATH="$HOME/.linuxbrew/bin:$PATH"
export MANPATH="$HOME/.linuxbrew/share/man:$MANPATH"
export INFOPATH="$HOME/.linuxbrew/share/info:$INFOPATH"

#setup linuxbrew
brew doctor
brew tap homebrew/dupes
brew tap homebrew/science
brew tap nextgenusfs/tap
brew update
```

4) Install funannotate and dependencies using LinuxBrew
```
brew install freetype
brew install augustus
brew install funannotate
```

5) Install python modules via PIP - some of the packages in apt-get are too old, so upgrade with `-U`
```
pip install -U biopython numpy pandas scipy matplotlib seaborn natsort goatools fisher scikit-learn
```

6) Download RepeatMasker libraries from [RepBase](http://www.girinst.org/repbase/) you will need to register
```
#download RepeatMasker libraries and install
wget --user name --password pass http://www.girinst.org/server/RepBase/protected/repeatmaskerlibraries/repeatmaskerlibraries-20160829.tar.gz
tar zxvf repeatmaskerlibraries-20160829.tar.gz -C $HOME/.linuxbrew/opt/repeatmasker/libexec

#now setup RepeatMasker
cd $HOME/.linuxbrew/opt/repeatmasker/libexec
./configure <config.txt

#softlink GFF script to bin
ln -s $HOME/.linuxbrew/opt/repeatmasker/libexec/util/rmOutToGFF3.pl $HOME/.linuxbrew/bin
```

7) Download and install GeneMark-ES/ET [here](http://exon.gatech.edu/GeneMark/license_download.cgi)
```
#uncompress and then move gmes_petap subdirectory to /usr/local
tar zxvf $HOME/Downloads/gm_et_linux_64.tar.gz
sudo mv gm_et_linux_64/gmes_petap/ /usr/local

#download key, then move to home directory
gunzip gm_key_64.gz
cp $HOME/Downloads/gm_key_64 ~/.gm_key
```

8) Add several components and ENV variables to `~/.bash_aliases` which will get sourced by bashrc
```
#example using gedit
sudo gedit ~/.bash_aliases

#add folder for GeneMark to PATH, HomeBrew will take care of other tools
export PATH="/usr/local/gmes_petap:$PATH"

#add environmental variables
export AUGUSTUS_CONFIG_PATH=$HOME/.linuxbrew/opt/augustus/libexec/config
export EVM_HOME=$HOME/.linuxbrew/opt/evidencemodeler
export GENEMARK_PATH=/usr/local/gmes_petap
export BAMTOOLS_PATH=$HOME/.linuxbrew/opt/bamtools/bin
```

9) Re-launch a terminal window (or type `source ~/.bash_aliases`). Finally run funannotate setup script to download databases and identify any problems.
```
funannotate setup --all
```
The script will download and format necessary databases and then check all of the dependencies of funannotate - any tool not properly installed will be flagged by the script.


####Python Dependencies:
* Python 2
* Biopython
* psutil
* natsort
* goatools
* fisher
* numpy
* pandas
* matplotlib
* seaborn
* sklearn library

####Software Dependencies:
* HomeBrew
* Perl
* BioPerl
* Blast+
* Hmmer3
* EVidenceModeler
* RepeatModeler
* RepeatMasker
* GMAP
* Blat - if using PASA results to train Augustus
* pslCDnaFilter (kent tools) - if using PASA results to train Augustus
* BedTools
* Augustus
* GeneMark-ES/ET (gmes_petap.pl)
* BamTools
* Genome Annotation Generator (gag.py)
* tbl2asn
* BRAKER1 (optional if training Augustus with RNA-seq data BAM file)
* Mummer
* RAxML
* mafft
* trimal

####Environmental variables:
EVM_HOME, GENEMARK_PATH, BAMTOOLS_PATH, AUGUSTUS_CONFIG_PATH

