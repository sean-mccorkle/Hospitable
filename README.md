# Hospitable

Trying to give [Condor](https://research.cs.wisc.edu/htcondor/) the convenience of using a shared file system and a friendly parallel scheduler (we're aiming for something as easy to use as [GNU parallel](https://www.gnu.org/software/parallel/) in the long term here folks)


###Scripts

1. **`hosp`** Hospitable condor interface for parallel execution of unix commands.

   Takes a single command-line argument which is itself a unix command (*i.e.* a quoted string) where substitution patterns `$n`, `$$n` and `@n` are to be replaced by values from whitespace-separated tuples read from stdin.  Each line of the input tuples creates one parallel condor process execution of the unix command, where `$1` is replaced by the first element of the tuple, `$2` by the 2nd, etc.  Using `$$n` instead of `$n` informs hosp that the element is a file and the file must be transfered to the remote execute node. Using `@n` indicates the tuple element is a comma-separated list of files, all of which are to be transfered.


        hosp  'tophat db/gen  @1  @2' <tuples

    
   where `tuples` contains
    
        file_a_f,file_b_f,file_c_f  file_a_r,file_b_r,file_c_r
        file_d_f,file_e_f           file_d_r,file_e_r
    
   `hosp` will generate files for remote parallel execution of these two commands

        tophat db/gen  file_a_f,file_b_f,file_c_f  file_a_r,file_b_r,file_c_r

   and
    
        tophat db/gen  file_d_f,file_e_f           file_d_r,file_e_r
    
   ensuring that the specified environment and all files needed for the execution are pre-prepared for the execution. On the head node, `hosp` will create, in the current directory, one condor submit file `hosp.condor` for two processes, one executable driver script  `hosp_remote_driver`, and two subdirectories for collecting the output from each process, `hosp.Subdir[01]`.

   The job will be immediately ready for submission to condor with this command:
    
        condor_submit hosp.condor


   Various environmental files (libraries, binaries, *etc.*) as well as additional files which are not specified in the tuple input, which are necessessary for the execution, can be specified via the -x'env string' option.
    
        hosp  -x'~/src/tophat-v2.1.0 ~/src/bowtie-2.1.0 ../mydatabase/gen*.bt1,~/db' \
              -p'./src/tophat-v2.1.0 ./src/bowtie-2.1.0' \
              'tophat db/gen  @1  @2' <tuples

   The -x string will cause ~/src/tophat-v2.1.0 and ~src/bowtie-2.1.0 to be transferred to the execute node before the specfied command is run, as well as files matching the glob ../mydatabase/gen*.bt1 which will be placed in a subdirectory ~/db created on the remote node.  This transfer is effected by creating a tarball on the host machine that will be transferred over by condor and untarred by the remote driver script.
   
   The -p option adds the remote directories ~/src/tophat-v2.1.0 and ~/src/bowtie-2.1.0 to the execution path.
   
2. **`pbsHosp`**  PBS version of `Hosp`

    generates a series of scripts for submission with qsub, each of which executes a copy of the unix command template for each line of input tuples on stdin, with template args ($1,$2,...) substituted with the corresponding tuple values.

3. **`Dev/b1`**  Bowtie2 test case

    This is a developmental test script (perl) to try to get multiple [bowtie2](http://bowtie-bio.sourceforge.net/bowtie2/index.shtml) jobs running as separate processes with a minimum of fuss.  Given tuples of input files, this will make entries into a single Condor submit file, and create numbered subdirectories for back-and-forth transfer.

4. **`Dev/pbs_b1`**  PBS queuing system version of b1 (bowtie2 test case)

