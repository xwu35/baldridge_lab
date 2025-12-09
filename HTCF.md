
## High Throughput Computing Facility (HTCF)

These are some common uses of the HTCF server at Washington University in St. Louis. Further details are available on [HTCF website](https://htcf.github.io/docs/). No VPN is needed to log in to HTCF. 

### 1. Log in to the server

Open a terminal, and type:

```bash
ssh <WashU_key_id>@login.htcf.wustl.edu
```

Use your WashU password for the password prompt. After logging into HTCF, you will be in your home directory. Note that the home directory is limited to 20GB but is not subject to purging (see purge policy below). You can install small packages in your home directory. For larger software or databases, you may use the `/ref/mtblab` directory, which is also not purged. Before installing any software or database there, please check whether it has been installed by lab members to avoid redundancy and save space.

### 2. Scratch directory 

The scratch directory is your main working space, with 2 TB of storage space. Additional space can be requested for a short period. Use the command below to access the scratch directory for Baldridge lab. 

```bash
cd /scratch/mtblab
```

If the directory corresponding to your WashU_key_id is not listed, you can create one for yourself.

```bash
mkdir <WashU_key_id>
```

**Notes from HTCF web: Data stored in `/scratch` is subject to the [Scratch Data Cleaning Policy](https://htcf.github.io/docs/policies/#scratch-data-cleaning). Files on `scratch` that have not been modified for more than 60 days are garbage collected and placed in a “trash” location (`/scratch/trash`). After 30 days in the trash location, user files are purged from the system. Once purged, there is no way files can be restored.** 

Keep this in mind and back up your data frequently to the long-term storage (see storage directory below). You can also use the `touch` command to update file timestamps to prevent them from being purged (e.g. `touch -m filename`). If a file is garbage-collected, you can restore it by moving them out of the `/scratch/trash` directory.

### 3. Storage directory

This directory `/lts/mtblab/baldridge1` is the shared data backup space for all Baldridge lab members. Files stored here are **NOT** purged.
 
- `/lts/mtblab/baldridge1/PERMANENT_FILE_STORAGE`: This is where all the original sequences are stored.

- `/lts/mtblab/baldridge1/ANALYSIS`: This is where you store your work. Create your own directory if does not already exist.

`rsync` is recommended to transfer files between scratch and LTS storage directory: 

```bash
# put the directory named "transfer" into the directory named "location"
rsync -aHv --progress /directory/to/transfer /destination/directory/location/

# put the contents of the directory named "transfer" into the directory named "location" (note the trailing slash after "transfer")
rsync -aHv --progress /directory/to/transfer/ /destination/directory/location/
```

### 4. Interactive job submission

When you log into HTCF, you are on the login node. You should use the login node only for small tasks, such as editing scripts and submitting jobs. **DO NOT run computational jobs on the login node, as this will slow down the system for everyone.** 

If you need to run commands directly in the terminal (e.g. for debugging) instead of submitting a sbatch job, you must first request computing nodes by submitting an interactive job. You may adjust the memory and CPU settings as needed.

```bash
srun --mem-per-cpu=8G --cpus-per-task=4 -J interactive -p interactive --pty /bin/bash -l
```

### 5. SBATCH job submission

SBATCH jobs run in the background and are particularly useful for programs that require distributed computing or need to run overnight or longer. To submit a job, you need to create a job script (e.g.`myjob.sh`). You can use text editor, such as `emacs`, `nano` or `vim`, to write or modify your script. The example below shows you how to create a script file using `emacs`. You can use the up, down, left and right arrows on your keyboard to go to the location where you want to edit, e.g. email address. Press `control + x + s` to save your script and `control + z` to quit the editor.

```bash
emacs myjob.sh
```
```bash
#!/bin/bash

#SBATCH --cpus-per-task=1
#SBATCH --mem=1G
#SBATCH -J MyJobName 
#SBATCH --output=slurm-%x.%j.out  # %j gives job id, %x gives job name
#SBATCH --error=slurm-%x.%j.err  # Errors will be written in this file
#SBATCH --mail-user=<your_email_address> # Email address to receive notifications about submitted job
#SBATCH --mail-type=ALL

commands here
```

Submit the sbatch script using the following command.

```bash
sbatch myjob.sh
```

View the progress of your job using 

```bash
squeue -u <WashU_key_id>
```

### 6. Run dependency jobs

You can run dependency jobs to ensure tasks are executed in a specific order. See the commands below for examples.

```bash
# submit job1.sh
sbatch job1.sh

# run job2.sh if job1 finishes successfully
sbatch --dependency=afterok:jobid_of_job1 job2.sh
```

#### Parameters for running dependency jobs

| Parameters    | Description                                                          |
|---------------|----------------------------------------------------------------------|
|  after        | Execute current job after listed jobs have begun                     |
|  afterok      | Execute current job after listed jobs have terminated without error  |
|  afternotok   | Execute current job after listed jobs have terminated with an error  |
|  afterany     | Execute current job after listed jobs have terminated for any reason |
|  before       | Listed jobs can be run after current job begins execution            |
|  beforeok     | Listed jobs can be run after current job terminates without error    |
|  beforenotok  | Listed jobs can be run after current job terminates with an error    |
|  beforeany    | Listed jobs can be run after current job terminates for any reason   |

### 7. Check job status

* Check the status of your submitted jobs: `squeue -u username`

* Check the status of your job in detail: `scontrol show job jobid`

* Cancel a submitted job: `scancel jobid`

* Check job efficiency: `seff jobid`

* Check start time of your job: `squeue -start -j jobid`

* Release/Hold a job: `scontrol release/hold jobid`

* Modify the name of the job: `scontrol update JobID=jobid JobName=any_new_name`

* Modify the total number of tasksh: `scontrol update JobID=jobid NumTasks=Total_tasks`

* Modify the number of CPUs per node: `scontrol update JobID=jobid MinCPUsNode=CPUs`

* Modify the wall time of the job: `scontrol update JobID=jobid TimeLimit=day-hh:mm:ss`

### 8. Transfer files between local computer and HTCF 

- using rsync or scp

Open a new terminal and DO NOT log into HTCF. 

```bash
rsync -aHv --progress <WashU_key_id>@login.htcf.wustl.edu:/directory/to/transfer /destination/directory/location/ 

scp -r <WashU_key_id>@login.htcf.wustl.edu:/directory/to/transfer /destination/directory/location/ 
```

- using Globus

You need to install the Globus Connect Personal application to your local computer. See Globus Connect Personal Installation for details (https://docs.globus.org/globus-connect-personal/install/). Once you have installed it, check the globus documentation for instruction on how to log in and transfer with Globus (https://docs.globus.org/guides/tutorials/manage-files/transfer-files/). You need your WashU_key_id and password to log in to Globus. The Globus "Collection" for HTCF is called "HTCF@WUSTL". 

### 9. Check disk quota

- check home directory quota

```bash
du -sh /home/<WashU_key_id>
```

- check LTS usage

```bash
df -h /lts/mtblab/baldridge1
```

- check Scratch quotas

```bash
beegfs-ctl --getquota --uid <WashU_key_id>
```

- check group quotas:

```bash
beegfs-ctl --getquota --gid mtblab
```

### 8. Check files that will be purged

The command below lists all files in the specified directory that are older than 50 days.

```bash
find <directory> -type f -mtime +50
```

