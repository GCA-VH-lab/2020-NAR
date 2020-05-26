# 5p-Seq
Scripts for 5' Seq data analysis in Python 3 (ver 3.6)
In addition to basic Python libraries it assumes `pysam`, `argparse`, `pandas`, `collections`, `glob` to be installed.   

## Data files
```bash
# data files in the folder    0-References/
Genome.fa  # is Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fasta
Genome.gtf # is Saccharomyces_cerevisiae.R64-1-1.95.gtf
#
# reformated and indexed annotation for tabix (part of pysam)
genome.gtf.gz
genome.gtf.gz.tbi
```

## Mapping 5PSeq data
`fivePSeqMap.py`  
maps reads 5' ends. Output is in compressed HDF5 format. HDF file have keys for each strand chromosome; for example: `"For_rpm/I"`   and    `"Rev_rpm/I"`  for the first chromosome.


## Metagene plots  before STOP
1. Get coverage around stop  
	`hdf_2_metagene_tables_stop_5PSeq.py`
Creates metagene tables for stop codon. Input is output of previous script (`fivePSeqMap.py` )   files ending with `*_idx_iv.h5`.  Output is table with relative coordinates from -120  (default value in nt inside gene) up to 120. 0 position corresponds to 5' position of stop codon.
```bash
# command line parameters 
./hdf_2_metagene_tables_stop_5PSeq.py 

-i      input *.h5:      None
-prefix part of oufile : None
-annot   annotation GTF: 0-References/genome.gtf.gz
-genome    genome FastA: 0-References/Genome.fa
-col            columns: rpm
-th    region threshold: 15
-span   included region: 120
-norm  to gene coverage: Yes
-subsets  by stop codon: None
-glist  subsets by list: None


  usage:
	./hdf_2_metagene_tables_1_dev.py  -i WTS1_5-End_21-33_idx_assign_rpm.h5   -prefix WTS1_stop_metagene   -norm  Yes
```

2. Merge coverage of different samples
`merge_metagene_tbl_5PSeq.py`

```bash
./merge_metagene_tbl_5PSeq.py -files "8-MetagTbl/*sum*" -o meta-Stop_th18-Span180.csv

```

3. Plot metagene
  Use you favorite plotting program for line-plots. 
	or `Plot-STOP-MetaG-5PSeq.ipynb`
	 
## Computing queuing score
`compute_queuing_5PSeq.py` 
Computes weighted queuing for each gene with a coverage above threshold.
```bash
./compute_queuing_5PSeq.py -h

Computes ribosomes queing score at stop

  -h, --help    show this help message and exit
  -i I          input table of gene coverage in *.hd5 format
  -o O          output file name *.csv
  -annot ANNOT  GTF annotation file
  -th1 TH1      Summary gene coverage 150 nt before stop - 10(rpm) default
  -th2 TH2      Background coverage - codon mean from -115 up to Span
  -span SPAN    Positions before - stop recommended 150 or bigger
  -bkg BKG      region for bakground con be 1 or 2: 1 - before and 2 - between
                peaks
  -col COL      column for values: "sum"; "rpm"; "counts"
```


## Extract sequence around stop codon
`extract_subsequence_stop.py`

```bash
./extract_subsequence_stop.py -test Yes

-annot   annotation GTF: 0-References/genome.gtf.gz
-genome    genome FastA: 0-References/Genome.fa
-b    before stop codon: 30
-a    after  stop codon: 9
-translate    translate: No
-list   subsets by list: None
-outpf  subsets by list: plain

  usage:
	./extract_subsequence_stop.py -annot 0-References/genome.gtf.gz  -genome  0-References/Genome.fa   -b 30 -a 9  > outfile.seq


# last amino acid for all genes
./extract_subsequence_stop.py -b 3 -a -3 -translate Yes -outpf Table  > last_aa_tbl.txt

# last coding codon
./extract_subsequence_stop.py -b 3 -a -3  -outpf Table  > last_coding-codon_tbl.txt

# stop codon
./extract_subsequence_stop.py -b 0 -a 0  -outpf Table  > stop-codon_tbl.txt

# stop codon extended by one towards  3 prime
./extract_subsequence_stop.py -b 0 -a 1  -outpf Table  > stop-codon-extended-3pr_tbl.txt

# stop codon extended by one towards  5 prime
./extract_subsequence_stop.py -b 1 -a 0  -outpf Table  > stop-codon-extended-5pr_tbl.txt
```
