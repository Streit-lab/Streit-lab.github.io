---
title: Using Rosalind
category: Rosalind
order: 2
---

### Transferring files 

Within Rosalind you can move, copy and delete files in the same way as a normal linux system (see linux command line cheat sheet). 
In order to transfer files between your local computer and your folders in Rosalind you use the `scp` command but you need to include your Rosalind login before the path. 

To transfer a file from Rosalind to your local computer:
`scp ­k12345678@login.rosalind.kcl.ac.uk:~/origin_folder_path ~destination_local_path`

To transfer a file from your local computer to Rosalind:
`scp  ~origin_local_path k12345678@login.rosalind.kcl.ac.uk:~/destination_folder_path`

When you do this you will be prompted for your Rosalind password. 

Tranferring multiple files at once - ?


### Doing analysis and running jobs

The "quickstart" version is that Rosalind is divided into "partitions" with certain nodes dedicated for certain things.
KCL members have access to the grc "cpu" and "gpu" partitions.
Mostly you will submit batch jobs to the "cpu" partition.
Never run anything substantial (ie. more than simple head, cd, mkdir .etc commands) on the login node (this is where you are when you first ssh into the cluster).

Note that "threads" are equivalent to --cpus-per-task and the max seems to be 32 on the cpu partition.
We can only have *one* interactive session running at a time. If you can't get one, it might be that you have one running that you forgot about.

To check what you've got running:
`squeue -u <username>`

To cancel a job:
`scancel <JOBID>`

To cancel all user jobs:
`scancel -u <username>`


You can run jobs in two different ways:

1) Using `srun` makes your job interactive. This means you have to wait for resources, and then you can interact with your job in real-time. If you close the terminal window the job will stop. This approach works for things like setting up an interactive rstudio session where you can change code and run things interactively (see below). It is not recommended for very computationally intensive jobs. 

2) Using `sbatch` submits your job to the cluster. This means it will join a queue and will run once it has resources. You can close the window and this job will still run. 

You can add flags to either of these commands to request certain reources, output in certain locations, etc. 


### Running rstudio interactively

First you need to set up an interactive job using `srun`. To save time, you can make an alias (i.e. a custom command) that will make an interactive job with some default parameters. 

Here is an alias you can use called `srun_default`. Make a new file called `.aliases.sh` in the base directory in Rosalind and copy paste this into that file. 

`function srun_default () {`
`        local TIME=3:00:00`
`        local CPU=8`
`        local MEM=64G`

`        while [[ $# -gt 0 ]]`
`        do`
`            case $1 in`
`            -t|--time)`
`                local TIME=$2`
`                ;;`
`            -c|--cpu)`
`                local CPU=$2`
`                ;;`
`                -m|--mem)`
`                local MEM=$2`
`                ;;`
`            esac`
`            shift`
`        done`

`    srun --ntasks=1 --cpus-per-task=$CPU --partition=brc --time=$TIME --mem=$MEM --pty bash`
`}`

Once you have run this command (just type `srun_default`) you need to start an rstudio session that you can access on your computer. 
To do this you can add another alias to your `.aliases.sh` file called `singularity_rstudio`:

`function singularity_rstudio() {`
`        while [[ $# -gt 0 ]]`
`        do`
`            case $1 in`
`            -v|--volume)`
`                local VOLUME=$2`
`                ;;`
`            -c|--containder)`
`                local CONTAINER=$2`
`                ;;`
`            esac`
`            shift`
`        done`

`	# load the module`
`        module purge`
`        module load utilities/use.dev`
`        module load apps/singularity/3.5.3`

`        export PASSWORD='password'`
`        # get unused socket per https://unix.stackexchange.com/a/132524`
`        # tiny race condition between the python & singularity commands`
`        readonly PORT=$(python -c 'import socket; s=socket.socket(); s.bind(("", 0)); print(s.getsockname()[1]); s.close()')`
`        # This will be printed out in the out-$JOB_ID file`

`        printf "\n1) SSH tunnel from your workstation using the following command: \nssh -N -L 8787:$SLURMD_NODENAME:${PORT} ${USER}@login.rosalind.kcl.ac.uk\n\n"`
`        printf "2) Point your web browser to http://localhost:8787\n\n"`

`        printf "3) Log in to RStudio Server using the following credentials:\nuser: ${USER}\npassword: ${PASSWORD}\n\n"`


`        # this will start the server`
`        singularity exec -c -B $VOLUME:/home/rstudio $CONTAINER rserver --www-port $PORT --www-address 0.0.0.0 --auth-none=0 --auth-pam-helper-path=pam-helper`

`}`

Now you can run `singularity_rstudio` inside your interactive node to start the interactive rstudio session. 

`singularity_rstudio -c ./path_t0_singularity_file -v /folders_you_want_to_load`

You will be given a ssh tunnel which you need to copy into a new terminal window on your computer. You will then be prompted for your Rosalind password. Then you can go to http://localhost:8787/ on your browser to access the interactive rstudio session. 


As you have now added these two aliases to your Rosalind home directory, they will be there in the future and you don't need to copy them in. You can just use the commands `srun_default` and `singularity_rstudio` as you like. You can also adjust these aliases in your alises.sh file if you wish, or add flags to these commands to adjust their parameters. 