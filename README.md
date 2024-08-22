# High-Performance Computing with SLURM on a Compute Cluster

This tutorial provides an introduction and guide to high-performance computing (HPC) using [**SLURM**](https://slurm.schedmd.com/documentation.html) (Simple Linux Utility for Resource Management) on a compute cluster. It is intended for biology/BME researchers who want to leverage HPC resources for running large-scale computations and simulations.

# Introduction
High-performance computing (HPC) allows you to perform large-scale computations by distributing tasks across multiple computing nodes. This repository will guide you through the process of using SLURM, a powerful and flexible workload manager, to efficiently manage and execute jobs on a compute cluster.

Universities and research groups often have their own compute cluster. There are also commercial options that are more flexible and scalable, such as AWS, GCP, XSEDE and etc. 

# Do You Need a Compute Cluster?
There are pros and cons with using a compute cluster. It is really useful if:
+ Your job requires a lot of computational resources (ex. too slow to run on a personal computer)
+ You need to run many jobs in parallel
Otherwise, consider run you job locally or on a single CPU/GPU.

# Prerequisites and Tools
Before diving into HPC with SLURM, ensure you have the following:
+ Basic knowledge of Linux command line.
+ Access to an HPC cluster with SLURM installed.
+ Familiarity with shell scripting (e.g., Bash).

Mac users can simply use the Terminal that comes with MacOS
Windows users need to install an SSH client ([a free one](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html ))

# Cluster Architecture
There are usually different types of compute nodes:
```
common - Core CPU nodes
scavenger - Nodes owned by other groups, you have “low priority” such that the your jobs maybe requeued or killed if the owners start their job
gpu-common - Core GPU nodes
scavenger-gpu - GPU nodes owned by other groups, you have “low priority” such that the your jobs maybe requeued or killed if the owners start their job
```
View all partitions and nodes on the cluster:
```
sinfo
```

Prioritize using the “common” nodes or nodes owned by your group. The partition is allocated through the SLURM script:
```
#SBATCH -p gpu-common --gres=gpu:1 # Use gpu-common, 1 GPU per node
#SBATCH -c 4 # 4 CPUs per node
```

## SLURM Scripts
[SLURM](https://slurm.schedmd.com/documentation.html) is an open source, fault-tolerant, and highly scalable cluster management and job scheduling system for large and small Linux clusters. It has three key functions. First, it allocates exclusive and/or non-exclusive access to resources (compute nodes) to users for some duration of time so they can perform work. Second, it provides a framework for starting, executing, and monitoring work (normally a parallel job) on the set of allocated nodes. Finally, it arbitrates contention for resources by managing a queue of pending work. 

Write the SLURM scripts in a .q or .sh file

MATLAB
```
#!/bin/bash
#SBATCH --output=slurm.out # Give the output file name
#SBATCH --error=slurm.err # Give the error file name
#SBATCH --mem=100 # Set the required memory per node as 100MB
/opt/apps/MATLAB/R2012b/bin/matlab -nojvm -nodisplay -r MATLAB_code # Run MATLAB_code with R20212b MATLAB
```

Python
```
#!/bin/bash
#SBATCH -o slurm.out
#SBATCH --mem=100
module load Python-GPU/3.6.5
python python_code.py
```

R
```
#!/bin/bash
#SBATCH -o slurm.out
#SBATCH --mem=100
module load R/3.6.0
R CMD BATCH R_code.R
```

Run a job array
```
#!/bin/bash
#SBATCH --output=slurm.out
#SBATCH --array=1-100 # Run MATLAB_code 100 times in parallel
#SBATCH --mem=100
/opt/apps/MATLAB/R2012b/bin/matlab -nojvm-nodisplay–singleCompThread-r “rank=$SLURM_ARRAY_TASK_ID;MATLAB_code;quit”
```
## Adapt Your Scripts
In many cases, it is useful to read in the taskID from the environment. To do so, add the following to the beginning of your code:

MATLAB
```
taskID = str2num(getenv('SLURM_ARRAY_TASK_ID'));
```
Python
```
import os
taskID=int(os.environ['SLURM_ARRAY_TASK_ID'])
```
R
```
taskID <- as.integer(Sys.getenv(SLURM_ARRAY_TASK_ID)) 
```

The task ID to run the same code with different parameters. For instance, for parameter screening, taskID can be used to read in the parameters from a pre-defined list. 
```
parameter_list = [1,2,3]
parameter = parameter_list[taskID-1]
```
You can also use it to save the outputs from different jobs:
```
filename = path + str(taskID) + ‘.txt’
with open(filename,’w’) as f:
f.write(my_results)
```

## Common SLURM Commands
+ sinfo: Display information about available nodes and partitions.
+ sbatch: Submit a batch job script to the scheduler.
+ squeue: View the list of jobs currently in the queue.
+ scancel: Cancel a job.
+ srun: Run a command in parallel on allocated nodes.
Check out SLURM [documentation](https://slurm.schedmd.com/documentation.html) for advanced functions.

To submit a job, upload all the code and the SLURM script into a folder. Open Terminal, and go to the directory containing your code, type in:
```
sbatch [slurm_script_file]
```
To view the status of the running jobs under your account
```
squeue -u [UserID]
```
To cancel a job,
```
scancel [JobID]
```
It is always useful to use .err and .out files, thus if you wonder the progress of your job or need to debug, simply check these files. 


# Best Practice
+ For large-scale simulations, it is suggested to make each job within a job array run for at least a few minutes. This helps ensure efficient utilization of resources.
+ When allocating resources, ensure you are using the appropriate number of compute cores and memory. Allocating more compute cores may allow for more parallel tasks, improving overall efficiency. Avoid over-requesting resources to prevent underutilization and longer queue times.
+ The cluster does not have unlimited space, it is important not explode the cluster storage as it is shared by many users. It is a good habbit to download data and back up frequency.

