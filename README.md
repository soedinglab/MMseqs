# MMseqs 
MMseqs (Many-against-Many sequence searching) is a software suite for very fast protein sequence searches and clustering of huge protein sequence data sets. 
MMseqs is around 1000 times faster than protein BLAST and sensitive enough to capture similarities down to less than 30% sequence identity.

## Requirements

To compile from source, you will need:

  * a recent C and C++ compiler (Minimum requirement is GCC 4.4. GCC 4.8 or later is recommended).

### Memory Requirements
When using MMseqs the available memory limits the size of database you will be able to compute. 
We recommend at least 128 GB of RAM so you can compute databases up to 50.000.000 entries:

You can calculate the memory requirements in bytes for L columns and N rows using the following formula:
        
        M = (4*N*L + 8*a^k) byte

MMseqs stores an index table and two auxiliary arrays, which have a total size of M byte.

For a database containing N sequences with an average length L, the memory consumption of the index table is `(4*N*L) byte`.
Note that the memory consumption grows linearly with the number of the sequences N in the database.

The two auxiliary arrays consume `(8*a^k) byte`, with a being the size of the amino acid alphabet (usually 21 including the unknown amino acid X) and the  k-mer size k.

## Installation
### Cloning from GIT
If you want to compile the most recent version, simply clone the git repository. 

        git clone git@bitbucket.org:soedinglab/mmseqs.git

### Compile 
First, set environment variables:

        export MMDIR=$HOME/path/to/mmseqs/
        export PATH=$PATH:$MMDIR/bin

MMseqs uses ffindex, a fast and simple database for wrapping and accessing a huge number of small files. Setting the environment variable `LD_LIBRARY_PATH` ensures that the needed ffindex libraries are available:

        export LD_LIBRARY_PATH = $LD_LIBRARY_PATH:$MMDIR/lib/ffindex/src
        cd $MMDIR/src/lib/ffindex
        make
 
Then build the MMseqs binaries:

        cd $MMDIR/src
        make

MMseqs binaries are now located in $MMDIR/bin.

## Overview of MMseqs
MMseqs contains six binaries. Three commands execute complete workflows that combine MMseqs core modules. 
The other three commands execute the single modules which are used by the workflows and are available for advanced users.

### Workflows
* `mmseqs_search` Compares all sequences in the query database with all sequences in the target database.
* `mmseqs_cluster` Clusters the sequences in the input database by sequence similarity.
* `mmseqs_update` Given an existing clustering of a sequence database and a new version of the sequence database (with some new sequences being added and others having been deleted), MMseqs incrementally updates the existing clustering.
### Single modules
* `mmseqs_pref` Computes k-mer similarity scores between all sequences in the query database and all sequences in the target database.
* `mmseqs_aln` Computes Smith-Waterman alignment scores between all sequences in the query database and the sequences of the target database whose prefiltering scores computed by `mmseqs_pref` pass a minimum threshold.
* `mmseqs_clu` Computes a similarity clustering of a sequence database based on Smith-Waterman alignment scores of the sequence pairs computed by `mmseqs_aln`.

### FFindex Database Format

All modules take ffindex databases as input and produce ffindex databases as output. ffindex was developed to avoid drastically slowing down the file system when millions of files need to be written and accessed. ffindex hides the files from the file system by storing them as unstructured data records in a single data file. In addition to this data file, an ffindex database includes a second index file: 
This index file stores an unique accession code, the start position in bytes of the data record in the FFindex data file and the length of the record for each file. When transforming a FASTA file with multiple sequences into an ffindex database, the accession code is the ID of the sequence parsed from the header. If no ID can be identified, the accession code is the whole header without the `>` character before the first blank space.

The binaries `fasta2ffindex` and `ffindex2fasta` located in mmseqs/bin do the format conversion from and to the ffindex database format. `fasta2ffindex` generates a ffindex database from a FASTA sequence database. `ffindex2fasta` converts an ffindex database to a FASTA formatted text file: the headers are ffindex accession codes preceded by `>`, with the corresponding dataset from the ffindex data file following.
However, for a fast access to the particular datasets in very large databases it is advisable￼to use the ffindex database directly without converting. We offer the binary `ffindex_get` ($MMDIR/lib/ffindex/src/) for direct access to the datasets stored in an ffindex database.


### How to cluster 
Before clustering, convert your FASTA database into ffindex format:

        fasta2ffindex DB.fasta DB

Please ensure that in case of large input databases the temporary folder tmp  provides enough free space.
For the disc space requirements, see the user guide. 

        mkdir tmp
        mmseqs_cluster DB DB_clu tmp --cascaded

To generate a FASTA-style formatted output file from the ffindex output file, type:

        ffindex2fasta DB_clu DB_clu.fasta

To run the more sensitive cascaded clustering and convert the result into FASTA format, type:

        mmseqs_cluster DB DB_clu_s7 tmp --cascaded -s 7
        ffindex2fasta DB_clu_s7 DB_clu_s7.fasta

### How to search
You can use the query database queryDB.fasta and target database targetDB.fasta to test the search workflow.
Before clustering, you need to convert your database containing query sequences (queryDB.fasta) and your target database (targetDB.fasta) into ffindex format:

        fasta2ffindex queryDB.fasta queryDB
        fasta2ffindex targetDB.fasta targetDB

It generates ffindex database files, e. g. queryDB and ffindex index file queryDB.index
from queryDB.fasta. Then, generate a directory for tmp files:

        mkdir tmp

Please ensure that in case of large input databases tmp provides enough free space.
For the disc space requirements, see the user guide.
To run the search type:

        mmseqs_search queryDB targetDB outDB tmp

Then convert the result ffindex database into a FASTA formatted database: 

        ffindex2fasta outDB outDB.fasta

## License Terms

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.
