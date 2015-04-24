C_FILES := $(shell find commons -name "*.cpp")
C_FILES += $(shell find prefiltering -name "*.cpp" ! -name "Main.cpp")
C_FILES += $(shell find alignment -name "*.cpp" ! -name "Main.cpp")
C_FILES += $(shell find alignment -name "*.C")
C_FILES += $(shell find clustering -name "*.cpp" ! -name "Main.cpp")

PREF_SOURCES := $(C_FILES)
PREF_SOURCES += prefiltering/Main.cpp

ALN_SOURCES := $(C_FILES)
ALN_SOURCES += alignment/Main.cpp

CLU_SOURCES := $(C_FILES)
CLU_SOURCES += clustering/Main.cpp

C_FILES += workflow/WorkflowFunctions.cpp

TT_SOURCES := $(C_FILES)
TT_SOURCES += workflow/TimeTest.h workflow/TimeTest.cpp workflow/TimeTestMain.cpp

SEARCH_SOURCES := $(C_FILES)
SEARCH_SOURCES += workflow/Search.cpp

CLUSTER_SOURCES := $(C_FILES)
CLUSTER_SOURCES += workflow/Clustering.cpp

UPDATING_SOURCES := $(C_FILES)
UPDATING_SOURCES += workflow/Updating.cpp 

FFINDEX2FASTA_SOURCES := $(C_FILES)
FFINDEX2FASTA_SOURCES += util/ffindex2fasta.cpp 

FASTA2FFINDEX_SOURCES := $(C_FILES)
FASTA2FFINDEX_SOURCES += util/fasta2ffindex.cpp 

CLUSTER2FFINDEX_SOURCES := $(C_FILES)
CLUSTER2FFINDEX_SOURCES += util/clusters2ffindex.cpp
 
PREF_OBJS := $(patsubst %.cpp, %.o, $(PREF_SOURCES))
ALN_OBJS := $(patsubst %.cpp, %.o, $(ALN_SOURCES))
CLU_OBJS := $(patsubst %.cpp, %.o, $(CLU_SOURCES))
SEARCH_OBJS := $(patsubst %.cpp, %.o, $(SEARCH_SOURCES))
CLUSTER_OBJS := $(patsubst %.cpp, %.o, $(CLUSTER_SOURCES))
UPDATING_OBJS := $(patsubst %.cpp, %.o, $(UPDATING_SOURCES))
FFINDEX2FASTA_OBJS := $(patsubst %.cpp, %.o, $(FFINDEX2FASTA_SOURCES))
FASTA2FFINDEX_OBJS := $(patsubst %.cpp, %.o, $(FASTA2FFINDEX_SOURCES))
CLUSTER2FFINDEX_OBJS := $(patsubst %.cpp, %.o, $(CLUSTER2FFINDEX_SOURCES))
TT_OBJS := $(patsubst %.cpp, %.o, $(TT_SOURCES))

CC = g++ 
#CFLAGS = -g -Wall  -I../../lib/ffindex/src/ -L../../lib/ffindex/src/ -lffindex  -Wno-write-strings
CFLAGS = -fopenmp -DOPENMP=1 -m64 -ffast-math -ftree-vectorize -O3 -Wno-write-strings -I../lib/ffindex/src/ -fno-strict-aliasing 
LDFLAGS = -L../lib/ffindex/src/ -lffindex

TARGETS = mmseqs_pref mmseqs_aln mmseqs_clu mmseqs_search mmseqs_cluster mmseqs_update ffindex2fasta cluster2ffindex fasta2ffindex time_test

all: $(TARGETS)

mmseqs_pref: $(PREF_OBJS)
	$(CC) $(CFLAGS) $(PREF_OBJS) $(LDFLAGS) -o ../bin/mmseqs_pref

mmseqs_aln: $(ALN_OBJS)
	$(CC) $(CFLAGS) $(ALN_OBJS) $(LDFLAGS) -o ../bin/mmseqs_aln

mmseqs_clu: $(CLU_OBJS)
	$(CC) $(CFLAGS) $(CLU_OBJS) $(LDFLAGS) -o ../bin/mmseqs_clu

mmseqs_search: $(SEARCH_OBJS)
	$(CC) $(CFLAGS) $(SEARCH_OBJS) $(LDFLAGS) -o ../bin/mmseqs_search

mmseqs_cluster: $(CLUSTER_OBJS)
	$(CC) $(CFLAGS) $(CLUSTER_OBJS) $(LDFLAGS) -o ../bin/mmseqs_cluster

mmseqs_update: $(UPDATING_OBJS)
	$(CC) $(CFLAGS) $(UPDATING_OBJS) $(LDFLAGS) -o ../bin/mmseqs_update

ffindex2fasta: $(FFINDEX2FASTA_OBJS)
	$(CC) $(CFLAGS) $(FFINDEX2FASTA_OBJS) $(LDFLAGS) -o ../bin/ffindex2fasta

fasta2ffindex: $(FASTA2FFINDEX_OBJS)
	$(CC) $(CFLAGS) $(FASTA2FFINDEX_OBJS) $(LDFLAGS) -o ../bin/fasta2ffindex

cluster2ffindex: $(CLUSTER2FFINDEX_OBJS)
	$(CC) $(CFLAGS) $(CLUSTER2FFINDEX_OBJS) $(LDFLAGS) -o ../bin/cluster2ffindex

time_test: $(TT_OBJS)
	$(CC) $(CFLAGS) $(TT_OBJS) $(LDFLAGS) -o workflow/time_test

# These are the pattern matching rules. In addition to the automatic
# variables used here, the variable $* that matches whatever % stands for
# can be useful in special cases.
%.o: %.cpp
	$(CC) $(CFLAGS) $(LDFLAGS) -c $< -o $@

%: %.cpp
	$(CC) $(CFLAGS) $(LDFLAGS) -o $@ $<

clean:
	rm -f ../bin/mmseqs_pref ../bin/mmseqs_aln ../bin/mmseqs_clu ../bin/mmseqs_search ../bin/mmseqs_cluster ../bin/mmseqs_update workflow/time_test
	rm -f ../bin/ffindex2fasta ../bin/fasta2ffindex ../bin/cluster2ffindex
	rm -f commons/*.o
	rm -f alignment/*.o
	rm -f prefiltering/*.o
	rm -f clustering/*.o
	rm -f workflow/*.o
	rm -f util/*.o
	rm -f test/*.o

