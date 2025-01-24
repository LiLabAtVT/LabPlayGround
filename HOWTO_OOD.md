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

After the apptainer pull the docker image, you will find a file called ***ood-rstudio-bio_4.1.2.sif8***

## Step 2. Start a slurm job.
Use this script.


