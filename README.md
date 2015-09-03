# Hospitable
Trying to give [Condor](https://research.cs.wisc.edu/htcondor/) the convenience of using a shared file system

###Scripts

**`b1`**

This is a developmental test script (perl) to try to get multiple bowtie2 jobs running as seperate processes with a minimum of fuss.  Given tuples of input files, this will make entries into a single Condor submit file, and create numbered subdirectories for back-and-forth transfer.
