# High-Performance Computing with SLURM on a Compute Cluster

This tutorial provides an introduction and guide to high-performance computing (HPC) using [**SLURM**](https://slurm.schedmd.com/documentation.html) (Simple Linux Utility for Resource Management) on a compute cluster. It is intended for researchers who want to leverage HPC resources for running large-scale computations and simulations.

# Introduction
High-performance computing (HPC) allows you to perform large-scale computations by distributing tasks across multiple computing nodes. This repository will guide you through the process of using SLURM, a powerful and flexible workload manager, to efficiently manage and execute jobs on a compute cluster.

# Prerequisites
Before diving into HPC with SLURM, ensure you have the following:
+ Basic knowledge of Linux command line.
+ Access to an HPC cluster with SLURM installed.
+ Familiarity with shell scripting (e.g., Bash).
  
# Cluster Architecture

# SLURM 

## SLURM Scripts

## Modify Your Scripts

## Common SLURM Commands
+sinfo: Display information about available nodes and partitions.
+sbatch: Submit a batch job script to the scheduler.
+squeue: View the list of jobs currently in the queue.
+scancel: Cancel a job.
+srun: Run a command in parallel on allocated nodes.

## More Details
Check out SLURM [documentation](https://slurm.schedmd.com/documentation.html) for more details and advanced functions.

# Best Practice
+ For large-scale simulations, it is suggested to make each job within a job array run for at least a few minutes. This helps ensure efficient utilization of resources.
+ When allocating resources, ensure you are using the appropriate number of compute cores and memory. Allocating more compute cores may allow for more parallel tasks, improving overall efficiency. Avoid over-requesting resources to prevent underutilization and longer queue times.

