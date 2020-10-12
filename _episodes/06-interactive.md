---
title: "Running an interactive job on Palmetto"
teaching: 15
exercises: 0
questions:
- "How can I find things in files?"
objectives:
- "Use `grep` to select lines from text files that match simple patterns."
keypoints:
- "`grep` selects lines in files that match patterns."
- "`--help` is a flag supported by many bash commands, and programs that can be run from within Bash, to display more information on how to use these commands or programs."
---

Now, we arrive at the most important part of today's workshop: getting on the compute nodes. Compute nodes are the real power of Palmetto. Let's see which of the compute nodes are availabe at the moment:

~~~
whatsfree
~~~
{: .bash}

We can see that the cluster is quite busy, but there is a fair amount of ompute nodes that are available for us. Now, let's request one compute node. Please type the following (or paste from the website into your SSH terminal):

~~~
qsub -I -l select=1:ncpus=4:mem=10gb,walltime=2:00:00
~~~
{: .bash}

It is very important not to make typos, use spaces and upper/lowerases exactly as shown, and use the proper punctuation (note the `:` between `ncpus` and `mem`, and the `,` before walltime). If you make a mistake, nothing wrong will happen, but the scheduler won't understand your request.

Now, let's carefully go through the request:

- `qsub` means that we are asking the scheduler to grant us access to a compute node;
- `-I` means it's an interactive job (we'll talk about it in a bit);
- `-l` is the list of resource requirements we are asking for;
- `select=1` means we are asking for one compute node;
- `ncpus=4` means that we only need four CPUs on the node (since all Palmetto compute nodes have at least 8 CPUs, we might share the compute node with other users, but it's OK because users who use the same node do not interfere with each other);
- `mem=10gb` means that we are asking for 10 Gb of RAM (you shouldn't ask for less than 8 Gb); again, memory is specific to the user, and not shared between different users who use the same node);
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

Please note two inportant things. First, our prompt changes from `login001` no `nodeXXXX`, where `XXXX` is some four-digit number. This is the number of the node that we got. The second one is the job ID, which is 631120. We can see the information about the compute node by using the `pbsnodes` command:

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

You can see that the node has 8 CPUs, no GPUs, belongs to phase 1a, and at the moment runs 8 jobs. One of these jobs is mine. When I submitted `qsub` request, the scheduler told me that my job ID is XXXXX. The `pbsnodes` command gives us the list of jobs that are currently running on the compute node, and, happily, I see my job on that list. It apears four times, because I have requested four CPUs.

To exit the compute node, type:

~~~
exit
~~~
{: .bash}

This will bring you back to the login node. See how your prompt has changed to `login001`. It is important to notice that you have to be on a login node to request a compute node. One you are on the compute node, and you want to go to another compute node, you have to exit first.

For some jobs, you might want to get a GPU, or perhaps two GPUs. For suh requests, the qsub command needs to specify the number of GPUs (one or two) and the type of GPUs (which you can get from `cat /etc/hardware-table`). For example: 

~~~
qsub -I -l select=1:ncpus=4:mem=10gb:ngpus=1:gpu_model=k40,walltime=2:00:00
~~~
{: .bash}

Another useful `qsub` option is `interconnect`. Interconnect is the way hpw the nodes are connected to each other. Remember that we have 1g Ethernet nodes and Infiniband nodes. Infinibans nodes may use FDR or HDR type of interonnect. You can specify the interconnect type, for example:

- `qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=1g,walltime=2:00:00`
- `qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=FDR,walltime=2:00:00`
- `qsub -I -l select=1:ncpus=4:mem=10gb:interconnect=HDR,walltime=2:00:00`

If the scheduler receives a request it cannot satisfy, it will complain and not assign you to a compute node (you will stay on the login node). For example, if you ask for 40 CPUs and `interconnect=1g`.

It is possible to ask for several compute nodes at a time, for example `select=4` will give you 4 compute nodes. Some programs, such as LAMMPS or NAMD, work a lot faster if you ask for several nodes. This is an advanced topic and we will not discuss it here, but you can find some examples on our website.


whatsfree
qsub w/o gpu
qsub w gpu
interonnect
select >1 node (MPI)

python --version
matlab --version

module avail
module load matlab
matlab --version
module list
module load r
module list --> other modules are loaded
R
module switch
module unload
module purge