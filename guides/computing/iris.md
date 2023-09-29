# IRIS

- [Homepage](https://www.iris.ac.uk/)

The cluster is essentially a standard Slurm cluster, though with some caveats. The cluster is built across two different compute clouds, and hence there are two main job queues and two different shared filesystems.

(Euclid) accounts are added onto the ‘ral’ and ‘cam’ queues (which run on workers at Rutherford Appleford Lab and Cambridge HPC respectively). Your home directory on the head node is not shared and is not visible on the workers.
Any code/environment builds you use need to be placed in `/mnt/ral/username` (for ral jobs) or `/mnt/cam/username` (for cam jobs).

Input and output files should go in those directories as well. Keep in mind that the shared filesystems have capacity in the 50TB range and are intended as a workspace, not long term storage. It is also worth noting that they are not backed up, and any data there should be considered “at-risk”.

Finally, the shared filesystems are not visible across the entire cluster: ‘cam’ nodes can only see `/mnt/cam`, ‘ral’ nodes can only see `/mnt/ral`. Using `/mnt/ral` on the headnode can be a bit slow due to the way it’s mounted (which in itself is a hack to work around RAL’s firewall), so if you’re trying to build code and work on `/mnt/ral` you can do so by logging into euclid-ral-build (from head node: `ssh username@euclid-ral-build`) which is a 30 core virtual machine at RAL created specifically for that sort of work. It can also be used for transferring data (through rsync or scp).

If you are using official Euclid code (pipeline codes developed with CODEEN and distributed via CVMFS) then these are already visible on all the worker nodes (and head node) in the usual `/cvmfs/euclid/*` locations. If you have software that needs to be installed on the workers let me know as we’ll need to discuss it (since we try to keep the workers EDEN standard).

If you require a custom Python environment it is best to build one using anaconda in the shared directory.

Workers on ‘ral’ have 60 cores and 246GB RAM, with a total of ~1800 cores available. Workers on ‘cam’ have 52 cores and 86GB RAM, with a total of ~1800 cores available.

```
MaxArraySize            = 1001
MaxJobCount             = 100000
```

Note: For the Euclid allocations at least there is almost zero usage so even large array jobs will run instantly

## Getting access

Euclid:UK members have access to IRIS through annual applications. Individual members don't need to apply to IRIS. Instead they need to fill a form that goes to the Euclid:UK Coordination Group (EUCG). The form is found on the [Euclid:UK redmine](https://euclid.roe.ac.uk/projects/euclid-uk/wiki)

You will then be emailed with further instructions.

General access can be requested through the annual call.

## Usage

- For more on Slurm see [here](../slurm.md)

Different to UoB resources (Blue Pebble / Blue Crystal) and undocumented is that we need to define the partition we want to access. Default seems to be 'ral' so if working in the 'cam' cluster we need to define:

```
#SBATCH --partition=cam
```

There is also no default output file location so make sure to define in the sbatch options (e.g.):

```
#SBATCH --output=/mnt/cam/username/code_directory/slurm_output/%j.out
```

### Python

Python is available in `/usr/bin/python`, however this is limited in packages accessible etc.

Need to setup a virtual environment accessible by the code you are running (ensure pip is available to user):

```bash
/usr/bin/python -m venv myenv
source myenv/bin/activate
python -m pip install -r requirements.txt
```

Now we have an environment set up with our requirements. Now add this line to the beginning of our slurm submission script:

```bash
cd /path/to/code
source /path/to/myenv/bin/activate
```

For example:

```bash
cd /mnt/cam/username/codedir
source /mnt/cam/username/codedir/myenv/bin/activate

echo Start Time: $(date) && \
    python run_script.py ${SLURM_ARRAY_TASK_ID} && \
    echo End Time: $(date)
```

Issues:

- On RAL node there is sometimes a slurm error when the job is sent and all jobs fail. When this happens the job can be sent again immediately until it queues properly.
- `squeue –j [jobid]` sometimes repeatedly fails on RAL node. Not sure why but will eventually work if you are persistent.
