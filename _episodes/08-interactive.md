---
title: "Running an interactive job on Palmetto"
teaching: 15
exercises: 0
questions:
- "How do I request and interact with a compute node?"
objectives:
- "`qsub`, `pbsnodes`, modules"
keypoints:
- "`whatsfree` shows the current Palmetto usage"
- "`qsub` sends a request for a compute node to the scheduler"
- "software available on Palmetto is organized into modules according to version"
- "modules need to be loaded before use"
---

Now, we arrive at the most important part of today's workshop: getting on the compute nodes. Compute nodes are the real power of Palmetto. Let's see which of the compute nodes are available at the moment:

~~~
whatsfree
~~~
{: .bash}

We can see that the cluster is quite busy, but there is a fair amount of compute nodes that are available for us. Now, let's request one compute node. Please type the following (or paste from the website into your SSH terminal):

~~~
qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=1g,walltime=2:00:00
~~~
{: .bash}

It is very important not to make typos, use spaces and upper/lowercases exactly as shown, and use the proper punctuation (note the `:` between `ncpus` and `mem`, and the `,` before walltime). If you make a mistake, nothing wrong will happen, but the scheduler won't understand your request.

Now, let's carefully go through the request:

- `qsub` means that we are asking the scheduler to grant us access to a compute node;
- `-I` means it's an interactive job (we'll talk about it in a bit);
- `-l` is the list of resource requirements we are asking for;
- `select=1` means we are asking for one compute node;
- `ncpus=4` means that we only need four CPUs on the node (since all Palmetto compute nodes have at least 8 CPUs, we might share the compute node with other users, but it's OK because users who use the same node do not interfere with each other);
- `mem=10gb` means that we are asking for 10 Gb of RAM (you shouldn't ask for less than 8 Gb); again, memory is specific to the user, and not shared between different users who use the same node);
- `interconnect=1g` is the type of interconnect (the allowed types are `1g`, `fdr` and `hdr`). If you look at the output of `whatsfree` and `cat /etc/hardware-table`, you will see the different CPU/RAM configurations that are available for these three types of interconnect. Typically, but not always, `1g` nodes have less RAM and a smaller number of CPUs than `fdr` and `hdr` (with the `hdr` nodes being the most powerful interms of RAM and CPUs).
- finally, `walltime=2:00:00` means that we are asking to use the node for 2 hours; after two hours we will be logged off the compute node if we haven't already disconnected.

This is actually a very modest request, and the scheduler should grant it right away. Sometimes, when we are asking for much substantial amount of resources (for example, 20 nodes with 40 cores and 370 Gb of RAM), the scheduler cannot satisfy our request, and will put us into the queue so we will have to wait until the node becomes available. 

Once the request is granted, you will see something like that:

~~~
qsub (Warning): Interactive jobs will be treated as not rerunnable
qsub: waiting for job 631266.pbs02 to start
qsub: job 631266.pbs02 ready

(base) [gyourga@node0193 ~]$
~~~
{: .output}

Please note two important things. First, our prompt changes from `login001` no `nodeXXXX`, where `XXXX` is some four-digit number. This is the number of the node that we got (in our case, 0193). The second one is the job ID, which is 631266. We can see the information about the compute node by using the `pbsnodes` command:

~~~
pbsnodes node0193
~~~
{: .bash}

Here is the information about the node that I was assigned to (node0102):

~~~
(base) [gyourga@node0193 ~]$ pbsnodes node0193
node0102
     Mom = node0193.palmetto.clemson.edu
     ntype = PBS
     state = job-busy
     pcpus = 8
     Priority = 1
     jobs = 626489.pbs02/0, 626489.pbs02/1, 626489.pbs02/2, 626489.pbs02/3, 631266.pbs02/4, 631266.pbs02/5, 631266.pbs02/6, 631266.pbs02/7
     resources_available.arch = linux
     resources_available.chip_manufacturer = intel
     resources_available.chip_model = xeon
     resources_available.chip_type = e5520
     resources_available.host = node0193
     resources_available.hpmem = 0b
     resources_available.interconnect = 1g
     resources_available.make = dell
     resources_available.manufacturer = dell
     resources_available.mem = 31922mb
     resources_available.model = r610
     resources_available.ncpus = 8
     resources_available.ngpus = 0
     resources_available.node_make = dell
     resources_available.node_manufacturer = dell
     resources_available.node_model = r610
     resources_available.nphis = 0
     resources_available.phase = 1a
     resources_available.qcat = c1_workq_qcat, c1_solo_qcat, osg_qcat, phase01a_qcat, mx_qcat
     resources_available.ssd = False
     resources_available.vmem = 32882mb
     resources_available.vnode = node0193
     resources_assigned.accelerator_memory = 0kb
     resources_assigned.hbmem = 0kb
     resources_assigned.mem = 18874368kb
     resources_assigned.naccelerators = 0
     resources_assigned.ncpus = 8
     resources_assigned.ngpus = 0
     resources_assigned.nphis = 0
     resources_assigned.vmem = 0kb
     resv_enable = True
     sharing = default_shared
     last_state_change_time = Mon Oct 12 13:15:56 2020
     last_used_time = Thu Oct  1 01:31:30 2020
~~~
{: .output}

You can see that the node has 8 CPUs, no GPUs, belongs to phase 1a, and at the moment runs 8 jobs. One of these jobs is mine. When I submitted `qsub` request, the scheduler told me that my job ID is 631120. The `pbsnodes` command gives us the list of jobs that are currently running on the compute node, and, happily, I see my job on that list. It appears four times, because I have requested four CPUs.

To exit the compute node, type:

~~~
exit
~~~
{: .bash}

This will bring you back to the login node. See how your prompt has changed to `login001`. It is important to notice that you have to be on a login node to request a compute node. One you are on the compute node, and you want to go to another compute node, you have to exit first.

For some jobs, you might want to get a GPU, or perhaps two GPUs. For such requests, the `qsub` command needs to specify the number of GPUs (one or two) and the type of GPUs (which you can get from `cat /etc/hardware-table`). For example, let's request a NVIDIA Tesla K40 (these nodes are on the `fdr` interconnect so we have to specify that as well):

~~~
qsub -I -l select=1:ncpus=4:mem=10gb:ngpus=1:gpu_model=k40:interconnect=fdr,walltime=2:00:00
~~~
{: .bash}

Regarding the interconnect, the three examples below ask for the same combination of CPUs and RAM but with diffrent interconnect types: 
- `qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=1g,walltime=2:00:00`
- `qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=fdr,walltime=2:00:00`
- `qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=hdr,walltime=2:00:00`

If the scheduler receives a request it cannot satisfy, it will complain and not assign you to a compute node (you will stay on the login node). For example, if you ask for 40 CPUs and `interconnect=1g`.

It is possible to ask for several compute nodes at a time, for example `select=4` will give you 4 compute nodes. Some programs, such as LAMMPS or NAMD, work a lot faster if you ask for several nodes. This is an advanced topic and we will not discuss it here, but you can find some examples on our website.

It is very important to remember that you shouldn't run computations on the login node, because the login node is shared between everyone who logs into Palmetto, so your computations will interfere with other people's login processes. However, once you are on a compute node, you can run some computations, because each user gets their own CPUs and RAM so there is no interference. If you are on the login node, let's get on the compute node:

~~~
qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=1g,walltime=2:00:00
~~~
{: .bash}

We have a lot of software installed on Palmetto, but most of it is organized into *modules*, which need to be loaded. For example, we have many versions of Matlab installed on Palmetto, but if you type

~~~
matlab
~~~
{: .bash}

you will get an error:
~~~
-bash: matlab: command not found
~~~
{: .error}

In order to use Matlab, as well as most other software installed on Palmetto, you need to load the Matlab module. To see which modules are available on Palmetto, please type

~~~
module avail
~~~
{: .bash}

Hit `SPACE` several times to get to the end of the module list. This is a very long list, and you can see that there is a lot of software installed for you. If you want to see which versions of Matlab are installed, you can type

~~~
module avail matlab
~~~
{: .bash}

~~~
-------------------------------------------------------------- /software/AltModFiles ---------------------------------------------------------------
   matlab/MUSC2018b    matlab/2018b    matlab/2019b    matlab/2020a (D)

  Where:
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
~~~
{: .output}

Let's say you want to use Matlab 2020. To load the module, you will need to specify its full name:

~~~
module load matlab/2020a
~~~
{: .bash}

To see the list of modules currently loaded, you can type

~~~
module list
~~~
{: .bash}

If the Matlab module was loaded correctly, you should see it in the module list. In order to start command-line Matlab, you can type

~~~
matlab
~~~
{: .bash}

To exit Matlab, please type `exit`. To unload a module, you an use `module unload matlab/2020a` command. To unload all the modules, please type

~~~
module purge
~~~
{: .bash}

Now, if you do `module list`, the list should be empty. Now, let's start R. To see which versions of R are available, type

~~~
module avail r
~~~
{: .bash}

This will give you a list of all modules which have the letter "r" in them (`module avail` is not very sophisticated). Let's see what happens when you load the R 4.0.2 module:

~~~
module load r/4.0.2-gcc/8.3.1
module list
~~~
{: .bash}

~~~
Currently Loaded Modules:
  1) tcl/8.6.8-gcc/8.3.1   2) openjdk/11.0.2-gcc/8.3.1   3) libxml2/2.9.10-gcc/8.3.1   4) libpng/1.6.37-gcc/8.3.1   5) r/4.0.2-gcc/8.3.1
~~~
{: .output}

R depends on other software to run, so we have configured the R module in a way that when you load it, it automatically loads other modules that it depends on.

