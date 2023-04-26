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
