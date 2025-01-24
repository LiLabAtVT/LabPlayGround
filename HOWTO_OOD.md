# How OOD works on the backend and how to do this without using the OOD interface? 

This is based on my discussion with a previous ARC engineer who implemented the OOD for Rstudio among other things.

There are two main steps: (1) get a SIF file based on a docker image; and (2) submit a job to start Rserver on a work node, and tunnel to the work node. 

## Step 0. Load Apptainer (this was called Singularity before)
use the following command (tested on OWL server)
```
module load containers/apptainer/1.3.3
```

Step 1. 


Step 2. 
