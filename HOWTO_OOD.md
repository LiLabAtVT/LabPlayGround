# How OOD works on the backend and how to do this without using the OOD interface? 

This is based on my discussion with a previous ARC engineer who implemented the OOD for Rstudio among other things.

There are two main steps: (1) get a SIF file based on a docker image; and (2) submit a job to start Rserver on a work node, and tunnel to the work node. 

## Step 0. Load Apptainer (this was called Singularity before)
use the following command (tested on OWL server)
```
module load containers/apptainer/1.3.3
```

## Step 1. Pull a docker image. 
It is better to first create a folder for this and work within this folder.
```
mkdir testood
cd testood
apptainer pull docker://rsettlag/ood-rstudio-bio:4.1.2
```
The link "docker://rsettlag/ood-rstudio-bio:4.1.2" is from Bob Settlage's docker image.
The souce code is located at this [github repository](https://github.com/rsettlage/ood-rstudio-basic/). 
More specifically, this [docker file](https://github.com/rsettlage/ood-rstudio-basic/blob/master/Dockerfile.4.1.2), is what you see on one of the the OOD Rstudio.

After the apptainer pull the docker image, you will find a file called ***ood-rstudio-bio_4.1.2.sif***

## Step 2. Start a slurm job.
Use this [script](HOWTO_OOD/TestOOD_012425.sh).

You need to edit some part of the script first before use it.

```
line 8: 
#SBATCH --account=nanoporedisease  <- use an account you have access to such as I2GDS

line 73:
cd /home/songli/TestOOD <- change this to the folder where you saved the OOD file.

```

Once you made these edits, submit a slurm job.
```
sbatch TestOOD_012425.sh
```

This will generate a "slurm_xxxxx.out" file. See [example](HOWTO_OOD/slurm-68618.out). Follow the instructions in this file. Open a terminal in your own computer and type in the command:
```
ssh -N -L 8787:owl002:41399 songli@LOGIN-HOST
```
1. the owl002:41399 will change every time based on which worknode is allocatedto you.
2. songli@LOGIN-HOST will be songli@owl2.arc.vt.edu. I typically set this as an environmental variable.
3. to further simplify the login process, you want to setup public key authentication to avoid using 2 factor.
   





