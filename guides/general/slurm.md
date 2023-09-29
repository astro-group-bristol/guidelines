<p align="center">
  <img width="20%" alt="BRImage" src="assets/slurm.png">
</p>

# Slurm Scheduler

Slurm is the system used by (many) HPC facilities to manage the submission, queueing and running of jobs.

The key features of a queueing system:

- It matches available computing resources to those required to run a particular job.
- It enforces a strict fair-share policy, where all users are given equal access to the compute resources. No monopolization is possible.
- It balances the work load across all the nodes of the cluster.
- It maintains a queue of jobs to be run on the cluster. Jobs are typically submitted in batch mode, meaning that no human intervention is required when it is time to run the job. Depending on the available resources, your job may run immediately or perhaps when you are away from your computer screen

Login nodes are intended for basic tasks such as uploading data, managing files, compiling software, editing scripts, and checking on or managing your jobs. Small-scale interactive work may also be acceptable if your resource requirements are minimal.
Otherwise, all jobs must be submitted to the queue.

## Useful links

- [Bristol training](https://www.acrc.bris.ac.uk/protected/hpc-docs/training/intro-to-hpc-slurm/summary.html)

## Examples

Example submit script name `test_job.sh`:

```bash
#!/bin/bash

#SBATCH --job-name=test_job
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --time=0:0:10
#SBATCH --mem=100M
#SBATCH --account=

echo 'My first job'
```

If we save that file as test_job.sh then we can submit it to Slurm using the sbatch command:

```bash
sbatch test_job.sh
```

This will produce an output like the following:

```bash
Submitted batch job 3955481
```

## Quick reference

Useful `#SBATCH` options include:

| Command                                                                                               | Action                                                                                                                                                                         |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `sbatch [jobscipt]`                                                                                   | Submits job script to Slurm queueing system                                                                                                                                    |
| `squeue -j [jobid]`                                                                                   | Check the status of a submitted job                                                                                                                                            |
| `scontrol show config `                                                                               | Gives entire configuration of compute resource                                                                                                                                 |
| `scontrol show config \| grep -E 'MaxArraySize\|MaxJobCount'`                                         | Returns only specific configuration items (in this case maximum index range for array jobs and maximum number of jobs allowed per user)                                        |
| `scancel-u $USER`                                                                                     | Cancel all your currently running jobs (for the big mistakes!)                                                                                                                 |
| `squeue --format="%.18i  %.40j %.8T %.10M  %.6D %R" --me`                                             | See the status of all your currently runnning jobs, with expanded NAME section to view longer job names. Useful to make an alias for this such as wr (what's running)          |
| `squeue -p your_partition_name`                                                                       | See who to blame for using up all the compute                                                                                                                                  |
| `srun --nodes=1 --time=00:45:00 --partition=devel --gres=gpu:1 --cpus-per-task=8  ./submit_script.sh` | Run interactive jobs where you get live output to terminal rather than submitting and looking at output files. Works best on shorter partitions (~1hr) for debugging purposes. |

Creating files to store output and error information:

```bash
#SBATCH --output=out.txt
#SBATCH --error=err.txt
```

Receiving emails when your job starts/stops running:

```bash
#SBATCH --mail-user=<user> #university email address for HPC
#SBATCH --mail-type=BEGIN #email when job starts
#SBATCH --mail-type=END #email when job ends
#SBATCH --mail-type=FAIL #email if job fails
```

Documentation on all available #SBATCH options and their use are available here: https://slurm.schedmd.com/sbatch.html

## Submitting multiple jobs

Instead of using one script to generate multiple submit scripts that each have to be `sbatch`ed, use the `<<-KEYWORD` script `KEYWORD` bash syntax to sbatch files on the fly.

Example:

```bash
#!/bin/bash

DATASETS=("ds1" "ds2" "ds3")
PARAMLIST=("param1" "param2" "param3" "param4")

for ds in ${DATASETS[*]} ; do
    for p in ${PARAMLIST[*]} ; do
        sbatch <<-END_SUBMIT
        #!/bin/bash

        # set the number of nodes
        #SBATCH --nodes=1

        # set max wallclock time
        #SBATCH --time=10:00:00
        #SBATCH --cpus-per-task=8

        # set name of job
        #SBATCH --job-name=$ds-$p

        # set number of GPUs
        #SBATCH --gres=gpu:1

        #SBATCH --partition=small

        # mail alert at start, end and abortion of execution
        #SBATCH --mail-type=ALL

        # send mail to this address
        #SBATCH --mail-user=your_emai@bristol.ac.uk

        python main.py --param $p --dataset $ds
        END_SUBMIT
    done
done
```

- Here the `END_SUBMIT` keywords are surrounding the text that would have been the individual bash scripts before.
- The example above will now submit 12 separate jobs, each with the respective parameters.

## Arrays jobs

| Resource     | MaxArraySize |
| ------------ | ------------ |
| Blue Crystal | 5000         |
| Blue Pebble  | 10000 😎     |
| IRIS         | 1001         |
| ISAMBARD     | -            |

Array jobs allow multiple similar script to be run at once.

In your job script add:

```bash
#SBATCH --array=0-100
```

This will run 101 jobs as the indices are inclusive. You can also you the % symbol to only run indices with a given step:

```bash
#SBATCH --array=0-100%5
```

Will run 0-100 in steps of 5.

It is important to note that when using arrays the parameters (ie. Wall time, memory allocation) included in the sbatch job script are applied to each array task, not the cumulative job. You should make sure to define:

```bash
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=1
#SBATCH --time=hh:mm:ss
#SBATCH --mem-per-cpu=3G
```

Setting output as:

```bash
#SBATCH --output=path_to_slurm_output/slurm-%A_%a.out
```

will name your slurm output files as `slurm-[jobid]-[arrayindex]`.

To vary input parameters for your job it can be useful to access the array index in your sbatch scripts. To do this you can use the variable `${SLURM_ARRAY_TASK_ID}` which will take the value of the current array index.

Example:

```bash
python ./main.py ${SLURM_ARRAY_TASK_ID}
```

To edit this value in the bash script define it as `INDEX=$((SLURM_ARRAY_TASK_ID))` and manipulate from there.

Here is an example where I used this to iterate through a list of missing input parameters (where previous jobs failed):

```bash
INDEX=$((SLURM_ARRAY_TASK_ID))
MISSINGID=$(sed -n ${INDEX}p ./output/missing.txt)
echo $MISSINGID

python ./main.py $MISSINGID
```

## Modules system

- [Bristol documentation](https://www.acrc.bris.ac.uk/protected/bc4-docs/software/index.html#using-modules)

Slurm is usually tightly coupled with the modules system, which allows you to load in specific tools or compiler version in your job.

### Overview

To see all available modules, use

```bash
module avail
```

NB: if you run this on a login node, you will not see the list of modules you can run in your jobs.
Hint: `grep` is your best friend.

Modules are "loaded" and "unloaded":

```bash
module load <module-name>
module unload <module-name>
```

Modules may have a whole directed acyclic diagram (DAG) of dependencies, which must also be loaded in sequence to ensure the module can function. In most cases, you don't need to be concious of this DAG and can just load the module you want, with the module system building up the layers.

The DAG of dependencies may be searched with

```bash
module spider <module-name>
```

To learn more about a module

```bash
module whatis <module-name>
```

To list all of the currently loaded modules

```bash
module list <module-name>
```

To remove all currently loaded modules (I.e. reset your modules) use

```bash
module purge <module-name?
```

### Using Modules in Slurm Jobs

Module commands are used afer the `SBATCH` pragmas and before your program is run. For example

```bash
#!bin/bash

#SBATCH --nodes=1
#SBATCH --time=00:01:00
#SBATCH --ntasks=1
#SBATCH --job-name=test-job
#SBATCH --output=test-job.%j.out

module purge  # purge to ensure everything is clean
module load python/3.5.1

python3 test-program.py
```
