# High-Performance Computing with SLURM on a Compute Cluster

This tutorial provides an introduction and guide to high-performance computing (HPC) using [**SLURM**](https://slurm.schedmd.com/documentation.html) (Simple Linux Utility for Resource Management) on a compute cluster. It is intended for biology/BME researchers who want to leverage HPC resources for running large-scale computations and simulations.

# Introduction
High-performance computing (HPC) allows you to perform large-scale computations by distributing tasks across multiple computing nodes. This repository will guide you through the process of using SLURM, a powerful and flexible workload manager, to efficiently manage and execute jobs on a compute cluster.

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

# SLURM 

## SLURM Scripts
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
## Modify Your Scripts

## Common SLURM Commands
+ sinfo: Display information about available nodes and partitions.
+ sbatch: Submit a batch job script to the scheduler.
+ squeue: View the list of jobs currently in the queue.
+ scancel: Cancel a job.
+ srun: Run a command in parallel on allocated nodes.

Check out SLURM [documentation](https://slurm.schedmd.com/documentation.html) for advanced functions.

# Best Practice
+ For large-scale simulations, it is suggested to make each job within a job array run for at least a few minutes. This helps ensure efficient utilization of resources.
+ When allocating resources, ensure you are using the appropriate number of compute cores and memory. Allocating more compute cores may allow for more parallel tasks, improving overall efficiency. Avoid over-requesting resources to prevent underutilization and longer queue times.

