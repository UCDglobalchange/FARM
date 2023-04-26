# FARM instructions
Information and code relating to using the FARM Linux-based supercomputing cluster for the College of Agricultural and Environmental Sciences at UC Davis.
- [Logging in](#logging-in)
- [Transferring Your Data](#transferring-your-data)
- [Using Software and Modules](#using-software-and-modules)
- [Using the Batch Queue SLURM](#using-the-batch-queue-slurm)
- [The /scratch/ Directory and Disk I/O](#the-scratch-directory-and-disk-io)
- [Jupyter Lab](#jupyter-lab)

## Logging In

Farm is accessible using [SSH](https://www.openssh.com/) in a terminal emulator.

### SSH Keys

An SSH key is required to log in. SSH keys are generated as a matched pair of a private key and a public key. Keep your private key safe and use a strong, memorable passphrase.

We support one key per user. If you need to access the cluster from multiple computers, such as a desktop and a laptop, copy your private key. Directions are on the [ssh key webpage](https://wiki.cse.ucdavis.edu/support/general/security/ssh).

Note that if you forget your passphrase or lose your private key, we cannot reset it. You'll need to generate a new key pair, following the same directions as when you first created it.

Visit the [ssh key webpage](https://wiki.cse.ucdavis.edu/support/general/security/ssh) for much more information on generating and using an ssh key on a PC, Mac, or Linux computer.

### Terminal Emulator Software

You will need terminal emulator software to log into Farm and run jobs. The software you choose must be able to use ssh keys to connect. This information is typically available in the documentation for the software. Common software choices include:

- [terminal.app](https://support.apple.com/guide/terminal/welcome/mac) or [iTerm 2](https://www.iterm2.com/iTerm2) are common MacOS terminal emulator options.
- [Mobaxterm](https://mobaxterm.mobatek.net/) is an all-in-one terminal emulator for Windows that gives a very Linux-like terminal environment along with the ability to edit files in a local editor and have changes automatically uploaded back to Farm.
- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) is the most common free and open-source terminal emulator for Windows. ([How to configure PuTTY](https://wiki.cse.ucdavis.edu/support/general/security/putty) for your SSH keys to connect to a cluster.)
- [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10) can provide a Linux terminal within Windows 10. Once you have it installed, you can follow the Linux-based directions to generate a key pair and use ssh at the command-line to connect.
- [Windows Terminal](https://devblogs.microsoft.com/commandline/introducing-windows-terminal/) for Windows 10 can provide a terminal experience much like Linux or MacOS. Preview code may be available on [Microsoft's Github](https://github.com/Microsoft/Terminal).

### Connecting

Once you have an SSH key and your account has been created, you can connect to Farm. In most text-based terminal emulators (Linux and MacOS), this is how you will connect:

`ssh yourusername@farm.cse.ucdavis.edu`

## Transferring Your Data
Farm uses SSH key-pairs ONLY so you need to point any local scp/sftp clients at that the same private part that you use to SSH to Farm.

### File Transfer Software

[Filezilla](https://filezilla-project.org/) is a multi-platform client commonly used to transfer data to and from the cluster.

[Cyberduck](https://cyberduck.io/) is another popular file transfer client for Mac or Windows computers that has the ability to edit files in a local editor and have changes automatically uploaded back to Farm.

[WinSCP](https://winscp.net/eng/index.php) is Windows-only file transfer software.

[Globus](https://wiki.cse.ucdavis.edu/globus?s[]=globus) is another common solution, especially for larger transfers.

rsync and scp are command-line tools to transfer data to and from the cluster.

### Example Transfer Commands

These commands should be run on your computer, not on Farm.

To transfer something to Farm from your local computer:

`scp -r local-directory username@farm.cse.ucdavis.edu:~/destination/`

Note: outbound scp initiated from Farm is disabled. Please initiate an inbound scp using the above method.

To transfer something from Farm to your local computer:

`scp -r username@farm.cse.ucdavis.edu:~/farmdata local-directory`
To use rsync to transfer a file or directory from Farm to your local computer:

`rsync -aP -e ssh username@farm.cse.ucdavis.edu:~/farmdata .`
rsync has the advantage that if the connection is interrupted for any reason you can just up-arrow and run the exact same command again and it will resume where it stopped.

See man scp and man rsync for more information.

### Where to store large datasets
If you need to download large datasets that other group members might use in the future, you should download them and store them in our group folder located at `/group/moniergr/`.

It would be good to let everyone know as well what the dataset is, so please include a short description of the data in the `README` file in the group folder, including the version of the data, a link to the repository/website, the temporal and spatial resolution, spatial extent and variables.

## Using Software and Modules

Farm has many software packages available for a wide range of needs. Most packages that are installed are available as environment modules using the `module avail` command. Use `module load <module/version>` to load a module, and `module unload <module/version>` when done.

To help minimize software environment conflicts, our clusters use the module command to alter the user's shell environment. This allows for reproducible environment changes that are easily loaded or unloaded at will.

When a user requests that a piece of software be made available to all users on the cluster, the sysadmins will often write an environment module to make it easily accessible for everyone with the module load command.

Typical module commands:

- `module show` - shows what modules you currently have loaded
- `module avail` - a list of ALL available modules
- `module load <modulename>` - load a module
- `module unload <modulename>` - unload a module
- `module purge` - unload ALL modules
- `module whatis <modulename>` - show a short description of the module, if available
- `module help <modulename>` - show more detailed information about a module, if available
Full help for the `module` command is available on the cluster by running `man module`, or on the web at the [modules package website](https://modules.readthedocs.io/en/stable/module.html).

Multiple versions of a the same software package may be available. By default, `module load <modulename>` will usually load the latest version. If you want to use the non-default version of a module, specify the version with a `/` after the module name. For example, to load a specific version of Hmmer: `module load hmmer/2.3.2`

The environment variables that get set by a module depend on the requirements of the software. At a minimum, most modules will add the software's executable location to the user's `$PATH`, but it may also load other modules as dependencies, ensure that specific conflicting modules are not loaded, or take other actions to ensure that the software will run correctly.

If you find an error in a module on one of our clusters, please contact the helpdesk and cut/paste the entire command line prompt, including your username, clustername, directory, the command you attempted to run and error output you received so that we can start the troubleshooting process to fix the module.

Generally, use as few modules as possible at a time–once you're done using a particular piece of software, unload the module before you load another one, to avoid incompatibilities.

Many of the most up-to-date Python-based software packages may be found under the `bio3` module. Load the module with `module load bio3` and run `conda list` to see a complete and up-to-date list.

Many additional Python 2 packages may be found under the `bio` module. Note that the `bio` and `bio3` modules are mutually incompatible with one another, so do not load both at the same time.

Visit the Environments page for much more information on getting started with software and the modules command on the cluster.

If you can't find a piece of software on the cluster, you can request an installation for cluster-wide use. Contact the [helpdesk](farm-hpc@ucdavis.edu) with the name of the cluster, your username, the name of the software, and a link to the software's website, documentation, or installation directions, if applicable.

## Using the Batch Queue SLURM

Job scheduling with [SLURM](https://wiki.cse.ucdavis.edu/support/hpc/software/slurm) is a key feature of computing on the cluster.

A *job* in the context of the cluster is a running piece of software performing some kind of function, such as computation, analysis, simulation, analysis, modeling, comparing, sorting, and other research-related tasks.

The *job scheduler* or *batch queue* system allows for the fair provisioning of limited resources (nodes, CPUS, memory, and time) on a shared system.

Farm uses the SLURM job scheduler to manage user jobs, passing the work to the compute nodes for execution, primarily through the use of `sbatch` and `srun` commands. Jobs are placed in a queue and executed according to a priority system.

**Do not skip the batch queue by running your compute tasks directly on the head/login node.**

Running jobs on the login node degrades performance for all users and can damage the cluster. Jobs found running outside of the job queue will be terminated and your account may be temporarily suspended until you contact the [helpdesk](farm-hpc@ucdavis.edu), so that the admins can work with you to help you run your job most effectively without damaging the cluster.

### Batch Partitions

The batch queue is divided into job priority queues called partitions. Access to a particular partition is determined by your college, department, lab, or sponsor's contribution to the cluster by buying nodes. You will be informed what partitions you have access to when you receive your account creation email.

Farm's primary partitions include:

- low, med, high - Farm II CPU compute nodes
- low2, med2, high2 - Farm III CPU compute nodes
- bigmeml, bigmemm, bigmemh - Farm II high-memory compute nodes
- bml, bmm, bmh - Farm III high-memory compute nodes
When purchasing a node, it will typically be added to the pool of nodes in the latest generation of Farm (Farm III, as of 2019) unless special arrangements are made.

**Choosing a Partition:**

**Low** priority - jobs in this queue may killed at any time when superceded by jobs in the medium or high partitions, with the possibility of being restarted at a later time when there are resources available again. The low queue is useful for soaking up unused cycles with short jobs, and is a particularly good fit for large array jobs with short run times. Low priority jobs can use more resources than your group paid for, if there are no other higher-priority jobs.

**Medium** priority - jobs in this queue may be temporarily suspended when superceded by jobs in the high partition, but will resume when higher priority job finishes. Medium jobs can also use more resources than your group paid for, if there are no higher-priority jobs. It is NOT recommended to run MPI jobs in medium.

**High** priority - jobs in this queue will kill/suspend lower priority jobs. Jobs in high will keep the allocated hardware until it's done (or there's a system or power failure.) Jobs in the high partition are limited to using the number of CPUs that your group contributed to the cluster. This partition is recommended for MPI jobs.

For more information about submitting your job to the batch queue with the sbatch and srun commands, visit our SLURM page.

### SLURM Quick Introduction

- `sinfo` reports the state of partitions and nodes managed by SLURM. It has a wide variety of filtering, sorting, and formatting options.
- `smap` reports state information for jobs, partitions, and nodes managed by SLURM, but graphically displays the information to reflect network topology.
- `sbatch` is used to submit a job script for later execution. The script will typically contain one or more srun commands to launch parallel tasks.
squeue reports the state of jobs or job steps. It has a wide variety of filtering, sorting, and formatting options. By default, it reports the running jobs in priority order and then the pending jobs in priority order.
- `srun` is used to submit a job for execution or initiate job steps in real time. srun has a wide variety of options to specify resource requirements, including: minimum and maximum node count, processor count, specific nodes to use or not use, and specific node characteristics (so much memory, disk space, certain required features, etc.). A job can contain multiple job steps executing sequentially or in parallel on independent or shared nodes within the job's node allocation.smap reports state information for jobs, partitions, and nodes managed by SLURM, but graphically displays the information to reflect network topology.
- `scancel` is used to stop a job early. Example, when you queue the wrong script or you know it's going to fail because you forgot something.

More in depth information on SLURM can be found at [http://slurm.schedmd.com/documentation.html](http://slurm.schedmd.com/documentation.html)

**Example Script:**
```
#!/bin/bash -l
# NOTE the -l flag!

# If you need any help, please email farm-hpc@ucdavis.edu

# Name of the job - You'll probably want to customize this.
#SBATCH --job-name=benchmark-test

# Use the med2 partition (or which ever you have access to)
# Run this to see what partitions you have access to:
# sacctmgr -s list user $USER format=partition
#SBATCH --partition=med2

# Standard out and Standard Error output files with the job number in the name.
#SBATCH --output=bench-%j.output
#SBATCH --error=bench-%j.output

# Request 4 CPUs and 8 GB of RAM from 1 node:
#SBATCH --nodes=1
#SBATCH --mem=8G
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4 

# The useful part of your job goes below

# run one thread for each one the user asks the queue for
# hostname is just for debugging
hostname
export OMP_NUM_THREADS=$SLURM_NTASKS
module load benchmarks

# The main job executable to run: note the use of srun before it
srun stream
```

**Example Run:**

We will ask the batch queue for 1 node and 2, 4, 8, 16, and 32 CPUs to see how well this OpenMP code scales.
```
bill@gauss:~$ sbatch -N 1 -n 2 -t 5 test.sh 
Submitted batch job 12
bill@gauss:~$ sbatch -N 1 -n 4 -t 5 test.sh 
Submitted batch job 13
bill@gauss:~$ sbatch -N 1 -n 8 -t 5 test.sh 
Submitted batch job 14
bill@gauss:~$ sbatch -N 1 -n 16 -t 5 test.sh 
Submitted batch job 15
bill@gauss:~$ sbatch -N 1 -n 32 -t 5 test.sh 
Submitted batch job 16
```

**Example Output:**

This benchmark uses OpenMP to benchmark memory bandwidth with a 915MB array. This show increasing memory bandwidth as you use more CPUs up to a maximum of 60GB/sec using an entire node. If you are curious about the benchmark, additional information is available at [https://www.cs.virginia.edu/stream/](https://www.cs.virginia.edu/stream/).

```
bill@gauss:~$ ls
bench-12.output  bench-14.output  bench-16.output
bench-13.output  bench-15.output  test.sh
bill@gauss:~$ cat *.output | grep ":"
STREAM version $Revision: 5.9 $
Copy:       12517.8583       0.0513       0.0511       0.0516
Scale:      12340.2147       0.0521       0.0519       0.0524
Add:        12495.4439       0.0770       0.0768       0.0772
Triad:      12490.9087       0.0782       0.0769       0.0796
STREAM version $Revision: 5.9 $
Copy:       16879.8667       0.0381       0.0379       0.0384
Scale:      16807.9956       0.0384       0.0381       0.0388
Add:        16733.7084       0.0578       0.0574       0.0583
Triad:      16482.7247       0.0585       0.0582       0.0589
STREAM version $Revision: 5.9 $
Copy:       16098.1749       0.0399       0.0398       0.0400
Scale:      16018.8248       0.0402       0.0400       0.0405
Add:        15887.7032       0.0606       0.0604       0.0610
Triad:      15839.4543       0.0608       0.0606       0.0611
STREAM version $Revision: 5.9 $
Copy:       31428.3070       0.0205       0.0204       0.0206
Scale:      31221.0489       0.0206       0.0205       0.0207
Add:        31324.3960       0.0308       0.0306       0.0311
Triad:      31151.6049       0.0310       0.0308       0.0313
STREAM version $Revision: 5.9 $
Copy:       61085.8038       0.0106       0.0105       0.0108
Scale:      60474.7806       0.0108       0.0106       0.0110
Add:        61318.3663       0.0159       0.0157       0.0162
Triad:      61049.6830       0.0159       0.0157       0.0160
bill@gauss:~$ 
```

**Array Jobs:**

The newest version of slurm supports array jobs. For example:
```
$ cat test-array.sh
#!/bin/bash
hostname
echo $SLURM_ARRAY_TASK_ID
```

```
# Submit a job array with index values between 0 and 10,000 on all free CPUs:
$ sbatch --array=0-10000 --partition=low test-array.sh
```

On the Farm cluster the maximum array size is 10001.

More information at [http://www.schedmd.com/slurmdocs/job_array.html](http://www.schedmd.com/slurmdocs/job_array.html)

**SLURM Script Quick Reference**

*prefixed with #SBATCH*

| : | THING-TO-RANK |
|-----:|---------------|
|     1|               |
|     2|               |
|     3|               |

| `:` | A comment |
|-----|-----------|
| `–job-name=myjob` |Job Name |
| `–output=myjob.out` | Output sent to this file |
| `–output=myjob.%j.%N.out` | Output file named with job number and the node the job landed on |
| `–error=myjob.err` | Errors written to this file |
| `–partition=med` | Run is the med partition (known as a queue in SGE) |
| `–nodes=4` | Request four nodes |
| `–ntasks-per-node=8` | Request eight tasks per node. The number of tasks may not exceed the number of processor cores on the node |
| `–ntasks=10` | Request 10 tasks for your job |
| `–time=2-12:00:00` | The maximum amount of time SLURM will allow your job to run before it is killed. (2 days and 12 hours in the example) |
| `–mail-type=type` | Set type to: BEGIN to notify you when your job starts, END for when it ends, FAIL for if it fails, or ALL for all of the above |
| `–mail-user=email@ucdavis.edu` |	  |
| `–mem-per-cpu=MB` | Specify a memory limit for each process of your job |
| `–mem=MB` | Specify a memory limit for each node of your job |
| `–exclusive` | Specify that you need exclusive access to nodes for your job |
| `–share` | Specify that your job may share nodes with other jobs |
| `–begin=2013-09-21T01:30:00` | Start the job after this time |
| `–begin=now+1hour` | Use a relative time to start the job |
| `–dependency=afterany:100:101` | Wait for jobs 100 and 101 to complete before starting |
| `–dependency=afterok:100:101` | Wait for jobs 100 and 101 to finish without error |

Show all available options
```
$ sbatch --help
```
Another useful command
```
 $ sbatch --usage
```

## The /scratch/ Directory and Disk I/O

Disk I/O (input/output) happens when reading to or from a file on the hard drive. Please avoid heavy I/O in your home directory, as this degrades file server performance for everyone. If you know that your software is I/O intensive, such as software that rapidly reads/writes to many files, performs many small reads/writes, and so on, you may want to copy your data out of your home directory and onto the compute node as a part of your batch job, or the network file system (NFS) can bottleneck, slowing down both your job and others, as well.

To prevent NFS bottlenecking, Farm supports the use of the `/scratch/` directory on the compute nodes when you have I/O-intensive code that needs temporary file space. Each compute node has its own independent scratch directory of about 1TB.

Please create a unique directory for each job when you use scratch space, such as `/scratch/your-username/job-id/`, to avoid collisions with other users or yourself. For example, in your sbatch script, you can use `/scratch/$USER/$SLURM_JOBID` or `/scratch/$USER/$SLURM_JOBID/$SLURM_ARRAY_TASK_ID` (for array jobs).

When your job is finished, copy any results/output that you wrote to your `/scratch` subdirectory (if any) and remove ALL of your files from your `/scratch` location.

Note that `/scratch/` is a shared space between everyone who runs jobs on a node, and is a limited resource. It is your responsibility to clean up your scratch space when your job is done or the space will fill up and be unusable by anyone.

`/scratch/` is local to each node, and is not shared between nodes and the login node so you will need to perform setup and cleanup tasks at the start and end of every job run. If you do not cleanup at the end of every run you will leave remnants behind that will eventually fill the shared space.

The `/scratch/` directory is subject to frequent purges, so do not attempt to store anything there longer than it takes your job to run.

If you would like to purchase additional scratch space for yourself or your lab group, contact the helpdesk for more information.

## Jupyter Lab

The instruction on setting up and running Jupyer Lab is courtesy of Lucas Sterzinger with minor modifications from Erwan Monier. You can run Jupyter Lab on a computer node in FARM, but it’s a bit involved because the compute nodes are not directly accessible from the internet/your machine. Instead, you have to tunnel your connection through the login node (farm.cse.ucdavis.edu). Here’s how to setup Jupyter Lab and tunnel your connection properly:

1.	Download and install a python3 distribution, if you don’t have one already. I prefer [miniforge](https://github.com/conda-forge/miniforge) - it uses the conda-forge channel by default so that you get the most up-to-date versions of packages (the rest of this guide assumes you’re using miniforge instead of something like Anaconda). The python installed by default in FARM is difficult to work with and this will make your life much easier in the future.
  - Best way to do this is to wget/curl the Linux (x86) installer [https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh](https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh)
  - Run the installer with `bash ./Miniforge3-Linux-x86_64.sh`
  - This should create the folder "~/miniforge3" with lots of files inside of it.
  - Run `~/miniforge3/bin/conda init bash` - this will set up your .bashrc (or equivalent) with the correct paths. Change "bash" to your actual shell if it's different (I use zsh).
  - Log out and back into FARM. A `(base)` should appear at the beginning of your prompt and `which python` should return `/home/<username>/miniforge3/bin/python`
If `(base)` doesn’t appear at the beginning of your prompt and `which python` doesn’t return the right python path, you may need to copy the text relating to `# >>> conda initialize >>>` from your .bashrc to your .bash_profile.
  - Packages can now be installed with `conda install <package>`
  - Install Jupyter Lab with `conda install jupyterlab`
  - Set a password to access your jupyter lab with `jupyter lab password`
2.	[Here is a bash script](https://github.com/UCDglobalchange/FARM/blob/main/start_notebook.sh) that can be submitted to SLURM with `sbatch start_notebook.sh` to start Jupyter Lab on a compute node and print the necessary tunneling information.
  NOTE: Change the partition, cpus-per-task, and memory allocations as desired. **ntasks-per-node should always equal 1**.
3.	Once the job is submitted, a log file called `jupyter-notebook.log` should be created in the same directory. Near the top of the log file should be instructions for tunneling into the compute node that the JupyterLab was launched on. For me, that looks something like: `ssh -N -L 8888:c8-62.farm.cse.ucdavis.edu:9632 -i ./.ssh/id_rsa emonier@farm.cse.ucdavis.edu`
4.	Copy and paste that line into a new terminal window (a local one, on your machine). Then, in your web browser, go to [https://localhost:8888](https://localhost:8888)
