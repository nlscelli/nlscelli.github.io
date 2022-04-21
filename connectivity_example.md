---
title: Connectivity code example
permalink: https://nlscelli.github.io/codes/connectivity_example
---

# Writing a code for computer connectivity
Here I am going to show what are the main components when writing a script for connectivity. As an example, I am going to use the `jptremote.sh` code I wrote. The full script can be downloaded [on my github "Utilities" page](https://github.com/nlscelli/Utilities/blob/main/jptremote.sh).

The main goal of this code is to operate on your laptop/workstation a jupyter notebook/jupyter-lab running on a remote computer/server. This is useful if you have data or computational resources that are accessible only remotely.

At first, we begin by writing which interpreter we are using and we use the `trap` command to kill all subprocesses when terminating the script. This is important to make sure we close all connections and remote processes when we finish.
```bash
#!/usr/bin/env bash
trap "kill 0" SIGINT   #- kill all subshells on exit
```

Next, we need to start to define our options and set some defaults:
```bash
#- default inputs
port=8889                #- default port to use
d_server='None'          #- default destination server
t_server='None'          #- default tunnelling server (the one hosting the proxy)
conda_env='None'         #- conda environment where the notebook is stored
jupyter='jupyter-lab'    #- which program do we want to use
USER='None'              #- username of the remote server
```
After this, it is good practice to add a help function. Note that here I use the `EOM` (End Of Mark) to write a long string with newlines efficiently:
```bash
#- help function
HELP () {
cat << EOM
##############################################################
Program:   $0, Nicolas Celli 2022
Purpose:   Run jupyterlab/jupyter notebooks on remote machines
Syntax:
           -u Username for access to server
           -s Destination server where to run the jupyter notebook
           -t Tunnelling server needed as intermediate step to
              reach the destination server
           -p port on which to open jupyter (default 8889)
           -e anaconda environment from which to open jupyter (if any)
					 -j what notebook to run: either "jupyter-lab" or "jupyter notebook"
              (within quotes)
Example:
           $0 -u me -s my.server.com -t my.tunnel.server -p 8889 -e my_conda
##############################################################
EOM
}
```


Now we set up the reading of our options using bash `getopts` program. It reads a list of options (letters) that can either have an argument (denoted with `:`) or not (e.g. "h" below):
```bash
#- read in options
while getopts hs:t:p:e:j: OPT; do
  case ${OPT} in
    h)  HELP; exit;;
    u)  USER=`echo ${OPTARG}`;;
    s)  d_server=`echo ${OPTARG}`;;
    t)  t_server=`echo ${OPTARG}`;;
    p)  port=`echo ${OPTARG}`;;
    e)  conda_env=`echo ${OPTARG}`;;
    j)  jupyter=`echo ${OPTARG}`;;
    \?) echo "No correct option given; see help:"; HELP; exit;;
  esac
done
```

Here I am checking the options for the conda environment and the jupyter type, ensuring that the arguments make sense.
```bash
#- if any, enter a conda environment before running the notebook
if [ ! ${conda_env} == "None" ]; then
	conda_comm="conda activate ${conda_env} &&"
else
	conda_comm=""
fi

#- check for which jupyter program to run
if [ ${jupyter} != "jupyter-lab" ] && [ ${jupyter} != "jupyter notebook" ]; then
	echo '-j has to indicate either "jupyter-lab" or "jupyter notebook", exit' && exit
fi
```
In this line, we build the BASH command we want to execute remotely by concatenating the conda environment setup command `$conda_comm` (if any) and the jupyter lauch command. Note that the jupyter command is launched with `nohup`, so that it will run in the background and won't break even if the terminal shell is closed. Also note that we send the jupyter to a certain port defined in our options.
```bash
#- build remote command for jupyter
remotecomm="${conda_comm} nohup ${jupyter} --port=${port} --no-browser --ip=0.0.0.0 > jptset.out"
```
Now we use ssh (removing any possible alias by prepending `\`) through a proxy (`-J` option) to source the remote BASH and run our remote command. Also, we run the process in the background so that we can run the next bit of code.
```bash
#- execute remotely the command for jupyter
echo "Starting remote jupyter session on ${d_server}"
\ssh -J ${USER}@${t_server} ${USER}@${d_server} "source ~/.bashrc && ${remotecomm}" &
```
In this last section, we forward the chosen port from the remote server to our laptop/workstation using `ssh`. This will allow us to open the jupyter notebook/jupyter-lab on our own machine by going to the localhost:`${port}`.
```bash
#- forwarding the port to your computer for you to access the notebook
echo "Forwarding port ${port} to DIAS server ${d_server} "

printf "\n Access notebook by navigating in your browser to:\n  http://localhost:${port}/ \n\n"
\ssh -l ${USER} -L ${port}:"${d_server}":${port} -N ${t_server} || echo "Connection aborted"

#- Done!
```

And that's it! Once you add the folder with this script to your `$PATH`, you can call your remote jupyter by simply typing:
```bash
jptremote.sh -u my_username -s my.destination.server -t my.proxy.server -p my_port -e my_conda
```
