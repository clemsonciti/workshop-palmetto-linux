---
title: "The structure of the Palmetto Cluster"
questions:
- "What is the structure of the Palmetto Cluster?"
teaching: 15
exercises: 0
objectives:
- compute and login nodes, hardware table, `whatsfree`
keypoints:
- "Palmetto contains more than 2000 interconnected compute nodes"
- "a phase is a group of compute nodes that have the same architecture (CPUs, RAM, GPUs)"
- "a specialized login node runs the SSH server"
---

The computers that make up the Palmetto cluster are called *nodes*. Most of the nodes on Palmetto are *compute nodes*, 
that can perform fast calculations on large amounts of data. There is also a special node called the *login node*; it runs the server, 
which works like the interface 
between the cluster 
and the outside world. The people with Palmetto accounts can log into the server by running a client (such as `ssh`) on their local machines. 
Our client program passes our login credentials to this server, and if we are allowed to log in, the server runs a shell for us. 
Any commands that we enter into this shell are executed not by our own machines, but by the login node.

<img src="../fig/palmetto-structure.png" alt="Structure of the Palmetto Cluster" style="height:350px">

Another special node is the *scheduler*; Palmetto users can get from the
login node to the compute nodes by submitting a request to the scheduler, and the scheduler will assign them to the most appropriate compute node.
Palmetto also has a few so-called "service" nodes, which serve special purposes like transferring code and data to and from the cluster, and hosting web applications.

The Skylight nodes are integrated into Palmetto. To see the specifications of the Skylight compute nodes, let's type

To see the hardware specifications of the compute nodes, please type

~~~
cat /etc/hardware-skylight
~~~
{: .bash}

This will print out a text file with the hardware info. Please make sure you type exactly as shown; Linux is case-sensitive, space-sensitive, 
and typo-sensitive. The output will look something like this:

~~~
SKYLIGHT HARDWARE TABLE      Last updated:  Feb 11 2021

QUEUE    COUNT  CORES   RAM      GPUs           /local_scratch

skystd    22     20     125 GB    0               800 GB
skylm      3     28     503 GB    0               800 GB
skygpu     6     20      62 GB    4 x GTX1080     800 GB
skygpu     2     20     125 GB    2 x P100        800 GB
~~~
{: .output}

The Skylight nodes are grouped into three *queues*: `skystd` (Skylight standard), `slylm` (Skylight large memory), and `skygpu` (Skylight GPU). The number of nodes accessible through each queue is respectively, 22, 3, and 8. The first queue is the "standard" nodes which are good for most applications. Each node has 20 cores, or CPUs, or processors.  This means that you can run 20 processes in parallel. If the software can organize its operations into 20 parallel processes, it will run considerably faster (and a lot fo software is really good at this, Matlab and LAMMPS being just two examples). The standard nodes have 125 Gb of RAM. If your software needs more RAM than that, you should use the nodes in the large memory queue; they have 503 Gb of RAM (they also have more cores). However, there are only three such nodes, so they might be in high demand. Finally, some software really benefits from using GPU (graphical processing unit, which is basically a video card). In addition to running video, GPUs can be utilized for really fast and efficient computations. Six nodes in the GPU queue have GTX-1080 cards (four per node), and the remaining two have P100 cards (two per node); the latter is a much more powerful GPU than the former. 


We have more than 2,000 compute nodes. They are grouped into phases; all nodes within a phase have the same hardware specifications. 
The compute nodes in Phase 0 
have very large amount of RAM, up to 1.5 Tb. The nodes in phases 1 to 6 are connected to each other with 1g Ethernet connection; they have at least 8
CPUs and at least 15 Gb of RAM. Nodes in phases 7 and up are connected with *InfiniBand connection*, which is much faster than Ethernet. They are, on average, more powerful than the 1g nodes: they have at least 16 CPUs and at least 62 Gb of RAM. Most of them also have GPUs (videocards); they are typically not used for video processing, but rather for some computation-heavy procedures such as machine learning applications. About 600 compute nodes on Palmetto have GPUs. The InfiniBand nodes are more popular than the 1g nodes, so we have stricter limits on their use: one can use the 1g nodes for up to 168 hours at a time, whereas one can use an InfiniBand node for up to 72 hours.

To see which nodes are available at the moment, you can type 

~~~
whatsfree
~~~
{: .bash}

You will see something like this:

~~~

Fri Feb 12 2021 14:34:08

TOTAL NODES: 2143     NODES FREE: 510     NODES OFFLINE: 26     NODES RESERVED: 0


BIGMEM nodes
PHASE 0    TOTAL =  16  FREE =   9  OFFLINE =   0  BIGMEM nodes: (6) 24cores/500GB, (5) 32cores/750GB, (3) 80cores/1.5TB, (1) 40cores/1.5TB, (1) 40cores/1TB

C1 CLUSTER (older nodes with interconnect=1g)
PHASE 1a   TOTAL = 117  FREE =   0  OFFLINE =   0  TYPE = Dell   R610    Intel Xeon  E5520,      8 cores,  31GB, 1g
PHASE 1b   TOTAL =  43  FREE =   0  OFFLINE =   0  TYPE = Dell   R610    Intel Xeon  E5645,     12 cores,  94GB, 1g
PHASE 2a   TOTAL =  39  FREE =   0  OFFLINE =   0  TYPE = Dell   R620    Intel Xeon  E5-2660    16 cores, 251GB, 1g
PHASE 2b   TOTAL = 160  FREE =   0  OFFLINE =   1  TYPE = Dell   PE1950  Intel Xeon  E5410,      8 cores,  15GB, 1g
PHASE 3    TOTAL = 203  FREE =   0  OFFLINE =   0  TYPE = Sun    X2200   AMD Opteron 2356,       8 cores,  15GB, 1g
PHASE 4    TOTAL = 318  FREE = 253  OFFLINE =   0  TYPE = IBM    DX340   Intel Xeon  E5410,      8 cores,  15GB, 1g
PHASE 5a   TOTAL = 280  FREE = 149  OFFLINE =   2  TYPE = Sun    X6250   Intel Xeon  L5420,      8 cores,  30GB, 1g
PHASE 5b   TOTAL =   9  FREE =   0  OFFLINE =   0  TYPE = Sun    X4150   Intel Xeon  E5410,      8 cores,  15GB, 1g
PHASE 5c   TOTAL =  34  FREE =  34  OFFLINE =   0  TYPE = Dell   R510    Intel Xeon  E5460,      8 cores,  23GB, 1g
PHASE 5d   TOTAL =  23  FREE =   0  OFFLINE =   0  TYPE = Dell   R520    Intel Xeon  E5-2450    12 cores,  46GB, 1g
PHASE 6    TOTAL =  65  FREE =   0  OFFLINE =   0  TYPE = HP     DL165   AMD Opteron 6176,      24 cores,  46GB, 1g

C2 CLUSTER (newer nodes with interconnect=FDR)
PHASE 7a   TOTAL =  42  FREE =   0  OFFLINE =   0  TYPE = HP     SL230   Intel Xeon  E5-2665,   16 cores,  62GB, FDR, 10ge
PHASE 7b   TOTAL =  12  FREE =   0  OFFLINE =   0  TYPE = HP     SL250s  Intel Xeon  E5-2665,   16 cores,  62GB, FDR, 10ge, M2075
PHASE 8a   TOTAL =  71  FREE =   1  OFFLINE =   0  TYPE = HP     SL250s  Intel Xeon  E5-2665,   16 cores,  62GB, FDR, 10ge, K20
PHASE 8b   TOTAL =  57  FREE =   0  OFFLINE =   0  TYPE = HP     SL250s  Intel Xeon  E5-2665,   16 cores,  62GB, FDR, 10ge, K20
PHASE 8c   TOTAL =  88  FREE =   0  OFFLINE =  17  TYPE = Dell   PEC6220 Intel Xeon  E5-2665,   16 cores,  62GB, FDR, 10ge
PHASE 9    TOTAL =  72  FREE =   0  OFFLINE =   1  TYPE = HP     SL250s  Intel Xeon  E5-2665,   16 cores, 125GB, FDR, 10ge, K20
PHASE 10   TOTAL =  80  FREE =   1  OFFLINE =   0  TYPE = HP     SL250s  Intel Xeon  E5-2670v2, 20 cores, 125GB, FDR, 10ge, K20
PHASE 11a  TOTAL =  40  FREE =   0  OFFLINE =   0  TYPE = HP     SL250s  Intel Xeon  E5-2670v2, 20 cores, 125GB, FDR, 10ge, K40
PHASE 11b  TOTAL =   4  FREE =   3  OFFLINE =   0  TYPE = HP     SL250s  Intel Xeon  E5-2670v2, 20 cores, 125GB, FDR, 10ge, Phi
PHASE 12   TOTAL =  30  FREE =   0  OFFLINE =   0  TYPE = Lenovo MX360M5 Intel Xeon  E5-2680v3, 24 cores, 125GB, FDR, 10ge, K40
PHASE 13   TOTAL =  24  FREE =   0  OFFLINE =   0  TYPE = Dell   C4130   Intel Xeon  E5-2680v3, 24 cores, 125GB, FDR, 10ge, K40
PHASE 14   TOTAL =  12  FREE =   0  OFFLINE =   0  TYPE = HP     XL190r  Intel Xeon  E5-2680v3, 24 cores, 125GB, FDR, 10ge, K40
PHASE 15   TOTAL =  32  FREE =   1  OFFLINE =   0  TYPE = Dell   C4130   Intel Xeon  E5-2680v3, 24 cores, 125GB, FDR, 10ge, K40
PHASE 16   TOTAL =  40  FREE =   0  OFFLINE =   1  TYPE = Dell   C4130   Intel Xeon  E5-2680v4, 28 cores, 125GB, FDR, 10ge, P100
PHASE 17   TOTAL =  20  FREE =   0  OFFLINE =   0  TYPE = Dell   C4130   Intel Xeon  E5-2680v4, 28 cores, 125GB, FDR, 10ge, P100

C2 CLUSTER (newest nodes with interconnect=HDR)
PHASE 18a  TOTAL =   2  FREE =   0  OFFLINE =   0  TYPE = Dell   C4140   Intel Xeon  6148G,     40 cores, 372GB, HDR, 10ge, V100nv
PHASE 18b  TOTAL =  65  FREE =   0  OFFLINE =   0  TYPE = Dell   R740    Intel Xeon  6148G,     40 cores, 372GB, HDR, 25ge, V100
PHASE 18c  TOTAL =  10  FREE =   0  OFFLINE =   0  TYPE = Dell   R740    Intel Xeon  6148G,     40 cores, 748GB, HDR, 25ge, V100
PHASE 19a  TOTAL =  26  FREE =   0  OFFLINE =   1  TYPE = Dell   R740    Intel Xeon  6248G,     40 cores, 372GB, HDR, 25ge, V100
PHASE 19b  TOTAL =   4  FREE =   0  OFFLINE =   0  TYPE = HPE    XL170   Intel Xeon  6252G,     48 cores, 372GB, 10ge
PHASE 19c  TOTAL =   2  FREE =   2  OFFLINE =   0  RESERVED
PHASE 20   TOTAL =  22  FREE =   0  OFFLINE =   1  TYPE = Dell   R740    Intel Xeon  6238R,     56 cores, 372GB, HDR, 25ge, V100S

C2 CLUSTER (virtual GPU nodes)
PHASE 21   TOTAL =   8  FREE =   8  OFFLINE =   0  TYPE = Virtual GPU    Intel Xeon  6248G,      4 cores,  32GB, 25ge, V100

PHASE 22   TOTAL =  18  FREE =  17  OFFLINE =   1  RESERVED
PHASE 23   TOTAL =  22  FREE =  21  OFFLINE =   1  RESERVED

DGX NODES
PHASE 24a  TOTAL =   2  FREE =   2  OFFLINE =   0  TYPE = NVIDIA DGXA100 AMD   EPYC  7742,      128 cores, 990GB, HDR, 25ge, A100

SKYLIGHT CLUSTER (Mercury Consortium)
PHASE 25a  TOTAL =  22  FREE =   6  OFFLINE =   0  TYPE = ACT            Intel Xeon  E5-2640v4,  20 cores, 125GB, FDR, 1ge
PHASE 25b  TOTAL =   3  FREE =   1  OFFLINE =   0  TYPE = ACT            Intel Xeon  E5-2680v4,  28 cores, 503GB, FDR, 1ge
PHASE 25c  TOTAL =   6  FREE =   0  OFFLINE =   0  TYPE = ACT            Intel Xeon  E5-2640v4,  20 cores,  62GB, FDR, 1ge, GTX1080
PHASE 25d  TOTAL =   2  FREE =   2  OFFLINE =   0  TYPE = ACT            Intel Xeon  E5-2640v4,  20 cores, 128GB, FDR, 1ge, P100

NOTE: PBS resource requests must be LOWER CASE.
      Your job will land on the oldest phase that satisfies your PBS resource requests.
      Also run "checkqueuecfg" to find out the queue limits on number of running jobs permitted per user in each queue.
~~~

This table shows the amount of *completely free* nodes per each phase; a node which has, for example, 8 cores, but only 4 of them are used, would not be counted as "free". So this table is a conservative estimate. Note that there are a lot more free nodes in the 1g phases, compared to the InfiniBand phases. It is a good idea to run `whatsfree` when you log into Palmetto, to get a picture of how busy the cluster is. This picture can change pretty drastically depending on the time of the day and the day of the week.

