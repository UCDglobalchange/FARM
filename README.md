# FARM instructions
Information and code relating to using the FARM Linux-based supercomputing cluster for the College of Agricultural and Environmental Sciences at UC Davis

## Instructions on setting up Jupyter Lab on FARM (Courtesy of Lucas Sterzinger with modifications from Erwan Monier)
You can run Jupyter Lab on a computer node in FARM, but it’s a bit involved because the compute nodes are not directly accessible from the internet/your machine. Instead, you have to tunnel your connection through the login node (farm.cse.ucdavis.edu). Here’s how to setup Jupyter Lab and tunnel your connection properly:
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

## Where to store large datasets
If you need to download large datasets that other group members might use in the future, you should download them and store them in our group folder located at
`/group/moniergr/`
It would be good to let everyone know as well what the dataset is, so please include a short description of the data in the README file in the group folder, including the version of the data, a link to the repository/website, the temporal and spatial resolution, spatial extent and variables.

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


