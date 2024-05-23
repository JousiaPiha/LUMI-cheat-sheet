# Jousia's LUMI cheat sheet

This is my personal cheat sheet for getting started with LUMI supercomputer. I hope this will help others as well.

## List of resources
- CSC Computing Environment course at [https://e-learn.csc.fi](https://e-learn.csc.fi/)
  - Highly recommended for people with no experience in HPC (High-performance computing aka supercomputers)
- [CSC Tutorials](https://docs.csc.fi/support/tutorials/)
- [LUMI documentation](https://docs.lumi-supercomputer.eu/)
- [Slurm documentation](https://slurm.schedmd.com/documentation.html)
- [**SSH connection**](https://docs.csc.fi/computing/connecting/)

## Commands

Load basic AI/ML tools
```
module use /appl/local/csc/modulefiles; module load pytorch
```

Nano is not loaded on compute nodes by default. If you are like me and like it, you can load it with:
```
module load nano
```

If you are using software that will download models from Hugging Face, you can set a location for them. (By default they are downloaded in the user's home folder, the quota of which is only 20GB.)
```
export HF_HOME=/scratch/project_462000558/hf_cache
```
If you accidentally fill up your home folder, you can `rm -rf ~/.local`

### A script for starting an interactive compute node
I tend to start my test sessions with the following script:
```bash
#!/bin/bash
module use /appl/local/csc/modulefiles/   #Modules installed by CSC
module purge   #Unload previously loaded modules
module load LUMI   #To start with a clean slate
module load pytorch nano  #Load basic AI/ML tools and Nano text editor and anything you like.
export HF_HOME=/scratch/<PROJECT_FOLDER>/hf_cache   #Set a cache folder for models downloaded from Hugging Face

#The following will start an interactive computer node with two GPUs for two hours.
srun \
    --account=project_<PROJECT_ID> \
    --partition=dev-g \
    --nodes=1 \
    --gres=gpu:mi250:2 \
    --time=3:00:00 \
    --mem=0 \
    --pty \
    bash
```
### About jobs on LUMI
To get your job id, use:
```
squeue -u $USER
```
To get an additional compute node terminal, you can open a login node and use:
```
srun --jobid $JOBID --overlap --pty bash
```
To cancel a job, use:
```
scancel $JOBID
```
### Python virtual environments
You may not use Conda on LUMI. I usually install Python programs in a virtual environment in `/projappl/$PROJECT_FOLDER/$USER` with
```
python -m venv .venv --system-site-packages
```
