# MMseqs 
MMseqs (Many-against-Many sequence searching) is a software suite for very fast protein sequence searches and clustering of huge protein sequence data sets. 
MMseqs is around 1000 times faster than protein BLAST and sensitive enough to capture similarities down to less than 30% sequence identity.

## Requirements

To compile from source, you will need:

  * a recent C compiler (we suggest GCC 4.4 or later)

### Memory Requirement 
When doing computations on the, the available memory limits the size of database you will be able to compute. 
We recommend at least 128 GB of RAM so you can compute databases up to 50.000.000 entries:

You can calculate the memory requirements in bytes for L columns and N rows using the following formula:
        
        M = (4*N*L + 8*a^k) byte

For a database containing N sequences with an average length L, the memory consumption of the index lists is (4*N*L) byte. Note that the memory consumption grows linearly with the size of the sequence database. 
In addition, the index table stores the pointer array and two auxiliary arrays with the memory consumption where a is the size of the amino acid alphabet (usually 21 including the unknown amino acid X) and k is the k-mer size.

## Installation
### Cloning from GIT
If you want to compile the most recent version, simply clone the git repository. Then, from the repository root, initialize the libconjugrad submodule:

        git clone git@bitbucket.org:soedinglab/mmseqs.git

### Compile 
First, set environment variables:

        export MMDIR=$HOME/path/to/mmseqs/
        export PATH=$PATH:$MMDIR/bin

MMseqs uses ffindex, a fast and simple database for wrapping and accessing huge amounts of small files. Setting the environment variable LD_LIBRARY_PATH ensures that ffindex binaries are in the path:

        export LD_LIBRARY_PATH = $LD_LIBRARY_PATH:$MMDIR/lib/ffindex/src
        cd $MMDIR/src/lib/ffindex
        make
 
Then create MMseqs binaries:

        cd $MMDIR/src
        make

MMseqs binaries are now located in $MMDIR/bin.

## Run MMseqs 
### Clustering
Before clustering, convert your FASTA database into ffindex format:

        fasta2ffindex DB.fasta DB

Please ensure that in case of large input databases tmp provides enough free space. For
the disc space requirements, see the user guide. 

        mkdir tmp
        mmseqs_cluster DB DB_clu tmp --cascaded

To generate a FASTA-style formatted output file from the ffindex output file, type:

        ffindex2fasta DB_clu DB_clu.fasta

To run the more sensitive cascaded clustering and convert the result into FASTA format, type:

        mmseqs_cluster DB DB_clu_s7 tmp --cascaded -s 7
        ffindex2fasta DB_clu_s7 DB_clu_s7.fasta

###Search
You can use the query database queryDB.fasta and target database targetDB.fasta to test the search workflow.
Before clustering, you need to convert your database containing query sequences (queryDB.fasta) and your target database (targetDB.fasta) into ffindex format:

        fasta2ffindex queryDB.fasta queryDB
        fasta2ffindex targetDB.fasta targetDB

It generates ffindex database files, e. g. queryDB and ffindex index file queryDB.index
from queryDB.fasta. Then, generate a directory for tmp files:

        mkdir tmp

Please ensure that in case of large input databases tmp provides enough free space. For
the disc space requirements, see the user guide.
To run the search, type:

        mmseqs_search queryDB targetDB outDB tmp

Then, convert the result ffindex database into a FASTA formatted database: 

        ffindex2fasta outDB outDB.fasta

##License terms
The software is made available under the terms of the GNU General Public License v3.0. Its contributors assume no responsibility for errors or omissions in the software.
