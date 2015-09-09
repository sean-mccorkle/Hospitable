# Hospitable
Trying to give [Condor](https://research.cs.wisc.edu/htcondor/) the convenience of using a shared file system and a friendly parallel scheduler (we're aiming for [GNU parallel](https://www.gnu.org/software/parallel/) in the long term here folks)

###Scripts

**`pbsHosp`**  PBS version of `Hosp`

generates a series of scripts for submission with qsub, each of which executes a copy of the unix command template for each line of input tuples on stdin, with template args ($1,$2,...) substituted with the corresponding tuple values.

**`Dev/b1`**  Bowtie2 test case

This is a developmental test script (perl) to try to get multiple [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml) jobs running as separate processes with a minimum of fuss.  Given tuples of input files, this will make entries into a single Condor submit file, and create numbered subdirectories for back-and-forth transfer.

**`Dev/pbs_b1`**  PBS queuing system version of b1 (bowtie2 test case)

