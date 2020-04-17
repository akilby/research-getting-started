

## Other notes on the HPC, Unix, and Slurm

### sbatch

The HPC uses [Slurm](https://slurm.schedmd.com/quickstart.html) to manage job scheduling and job-related tasks. 

You'll mostly start by running jobs interactively using `srun` as discussed above. When you feel more confident, you may want to submit self-contained jobs using the jobs scheduler, and the command `sbatch`. You do this by preparing a job script - a text file, that contains the commands you'd use to start a job using `srun`.

```bash
#!/bin/bash

#SBATCH --mem=0
#SBATCH --nodes=1
#SBATCH --cpus-per-task=26
#SBATCH --exclusive
#SBATCH --time=1-00:00:00
#SBATCH --partition=reservation
#SBATCH --reservation=kilby
#SBATCH --output=pyscript.out
#SBATCH --error=pyscript.out

module add python/3.7.3-base

source /PATH/TO/YOUR/VENVS/venvs/general/bin/activate
python -u pyscript.py 
deactivate
```

### Useful Slurm commands

Here are some useful commands:

1. Check the status of jobs you have running on the general partitions (and if they are stuck, find out why they are still in the queue
    ```bash
    squeue --user=YOUR_USER_NAME 
    ```
2. Check the status of all jobs on my reservation:
    ```bash
    squeue --partition=reservation --reservation=kilby
    ```
3. Check resource availability on a partition (look for STATE=idle)
    ```bash
    sinfo --partition=short
    ```
4. Check the resources you have on the machine you've provisioned for a job    
    ```bash
    seff JOBID
    ```
5. Kill a job you submitted
    ```bash
    scancel JOBID
    ```

### Getting help



To generate a help ticket, you can email:
`rchelp@northeastern.edu`
