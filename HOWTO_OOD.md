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

After the apptainer pull the docker image, you will find a file called ***ood-rstudio-bio_4.1.2.sif*** in your folder.

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

This will generate a "slurm_xxxxx.out" file. See [example](HOWTO_OOD/slurm-68618.out). Follow the instructions in this file. Open a terminal in **your own computer** and type in the command:
```
ssh -N -L 8787:owl002:41399 songli@LOGIN-HOST
```
1. the owl002:41399 will change every time based on which worknode is allocatedto you.
2. songli@LOGIN-HOST will be songli@owl2.arc.vt.edu. I typically set this as an environmental variable. You should use your login information for this step. Use OWL2 or OWL3 is usually better because they are less crowded. 
3. to further simplify the login process, you want to setup public key authentication to avoid using 2 factor.

The next step is to open a browser and use http://localhost:8787 to connect the Rstudio.

## Other information and future steps.
1. these are based on Bob's work about 3 years ago. More examples can be found in his [github](https://github.com/rsettlage/ood-rstudio-basic/tree/master).
2. as for now (01-24-2025), I cannot find a way to increase the number of CPUs to be used, we can only use 1 CPU. If I modify the #SBATCH header to increase CPU or cores or tasks, the OOD cannot open the Rstudio session. I can reach the point of entering the password, but after the password, there will be no response, and return an error message.
3. there are other OOD or non-OOD docker you can pull. For example, you can pull the [Rocker](https://rocker-project.org/) images to run Rstudio server. This will run the latest R.  
```
apptainer  pull docker://rocker/rstudio:4.4.2
```
The problem is that we cannot install Seurat completely, because some dependency is needed to be installed separately.

4. following point 3. next step could be to use Bob's 4.1.2.sif as a template, update that to the latest rocker image (4.4.2) and latest Seurat (5.0). **This is the most promising longer term solution.** 

5. Alternatively, we can directly pull the Seurat 5.0 docker image. This one does not include Rstudio server, but we can run "Rscript" or "R".

```
apptainer pull docker://satijalab/seurat:5.0.0
apptainer exec --bind=/work,/projects seurat_5.0.0.sif R

```
This is **useful** if you are comfortable with running R script only. For example, to load Rachel's data which requires a large amount of memory, you can write a script and submit your job to execute your script as a typical SLURM job without he Rstudio interface. 

6. Another alternative method is to use vxcode to connect to ARC and run R within this vscode session. I have not found any tutorial about this online.
   


