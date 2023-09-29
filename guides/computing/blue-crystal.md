# BlueCrystal and BluePebble

## BlueCrystal Phase 4

Phase 4 has the following hardware:

- Each of the 525 Lenovo nx360 m5 compute nodes has two 14 core 2.4 GHz Intel E5-2680 v4 (Broadwell) CPUs, and 128 GiB of RAM.
- There are 17 extra high memory nodes, each of which has 512 GiB of RAM.
- There are 32 GPU nodes with two cards each, plus 1 GPU in a login node, totalling 65 GPUs.

### Usage

BlueCrystal uses [Slurm](../slurm.md).

BlueCrystal job scripts must contain a line that specifies which project you are working on (as part of the queueing system):

```bash
#SBATCH --account=your_hpc_project_code
```

You can find your project code by running the following line of code:

```bash
sacctmgr show user withassoc format=account where user=$USER
```

More information and FAQ answers are available in the [HPC documentation](https://www.acrc.bris.ac.uk/protected/hpc-docs/job_types/accounting.html).

## Useful links

- [Connecting to Blue Crystal](https://www.acrc.bris.ac.uk/protected/hpc-docs/connecting/index.html)
- [Introduction to HPC](https://www.acrc.bris.ac.uk/protected/hpc-docs/introduction/index.html)
- [General HPC documentaton](https://www.acrc.bris.ac.uk/protected/hpc-docs/index.html)

## Blue Pebble

- [General information](https://www.star.bris.ac.uk/local/bluepebble.html)

BluePebble a non-homogenous cluster, meaning that the nodes vary slightly:
Standard Nodes

- Approx 170 nodes in use with 24, 28 or 32 cores (majority are 24 cores)
- The majority of nodes have 192GB of RAM
- Next generation 100 Gbit Ethernet switches (nodes are connected at 10 Gbit or 25 Gbit, no InfiniBand interconnect)
- U Nodes
- 18 GPU enabled nodes: NVIDIA RTX2080Ti (each GPU node has 4 cgpu cards and 96GB RAM)
- erating System
- GNU/Linux (CentOS 7)

### Getting access

In order to request access bluepebble compute and storage, you need to use the [webform](here:https://www.acrc.bris.ac.uk/login-area/apply.cgi).

Login node

```bash
bp1-login.acrc.bris.ac.uk
```

### Usage

Current scheduler is [Slurm](../slurm.md), basic example scripts can be found here.

For monitoring SLURM jobs see [here](https://help.jasmin.ac.uk/article/4892-how-to-monitor-slurm-jobs).

BluePebble job scripts must contain a line that specifies which project you are working on (as part of the queueing system):

```bash
#SBATCH --account=your_hpc_project_code
```

You can find your project code by running the following line of code:

```bash
sacctmgr show user withassoc format=account where user=$USER
```

### Storage

Our storage is

```
/bp1/phaethon
```

This storage can be accessed through the Blue Pebble cluster to store the output of your computation.

This is divided into two parts, one part called `diana` (around 150Tb) is for Sotiria and her students who partially funded the storage, and the other `astro_users` is for general use.

When you request access, you must request a disk space quota (see below). There is about 150Tb in `astro_users` and currently it is not very full, use your discretion when requesting space, 20Tb is probably a reasonable starting place.

#### Transferring files to Phaethon example

After your account has been created, and you have logged in to check access, then files can be transferred using a command like the following.

```bash
scp /data/scratch4/<your-username>/some_huge_file
bp1-login01b.acrc.bris.ac.uk:/bp1/phaethon/astro_users/XXXX/some_subdirectory/
```

You can also transfer many files using wildcards, eg

```bash
scp /data/scratch4/<your-username>/some_files*
bp1-login01b.acrc.bris.ac.uk:/bp1/phaethon/astro_users/XXXX/some_subdirectory/
```
