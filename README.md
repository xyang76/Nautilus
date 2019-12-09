# Nautilu OMP_TASK_DATAFLOW design outline:
### Core part:
[Mod]	<include/nautilus/list.h>  	line 66: add a [void* value] for list_head, so we can get/set a value from list_node. <br/>
### GOMP part:
[New]	<include/rt/openmp/gomp/ompstruct.h>    	add definitions for omp_thread, omp_task, omp_team, omp_depend_entry etc.<br/>
[New]	<include/rt/openmp/gomp/task.h> 		add some functions support omp_task.<br/>
[New]	<include/rt/openmp/gomp/team.h> 		add some functions support omp_team. <br/>
[Mod]	<src/rt/openmp/gomp/gomp.c> 			modified old gomp implementation, add task, team and barriers. <br/>
[New]	<src/rt/openmp/gomp/team.c> 			a omp_team should manage shared lock, resources, tasks, dependencies. <br/>
[New]	<src/rt/openmp/gomp/task.c> 			a omp_task should manage its own dependencies, state, methods.<br/>
### GOMP test part:
[New]   <src/test/openmp/omptask/task_base.c>		test basic task executing. shell command: 'taskbase' or 'taskbase index' <br/>
[New]   <src/test/openmp/omptask/task_dependency.c>	test depend clause for data flow. shell command: 'taskdepend' or 'taskdepend index' <br/>
[New]   <src/test/openmp/omptask/task_if.c>		test if clause. shell command: 'taskif' or 'taskif index' <br/>

# To run omp-task
### Make and run qemu
>> make menuconfig  <br/>
choose  Runtimes->OpenMP RT->OpenMP RT(GNU-compatible (GOMP) [going away]) <br/>
choose  Debug OpenMP RT <br/>
choose  Include tests/benchmarks for OpenMP RT <br/>
 <br/>
Then make isoimage <br/>
>> make isoimage <br/>

Then run qemu. E.g.,  <br/>
>> qemu-system-x86_64 -cdrom nautilus.iso \
                      -m 512 \
                      -numa node,nodeid=0,cpus=0-1 \
                      -numa node,nodeid=1,cpus=2-3 \
                      -smp 4,sockets=2,cores=2,threads=1


### Commands:
$ taskbase [2] : two base cases for #pragma omp task <br/>
$ taskdepend [8] : three base dependencies tests(true, anti, out), one no dependency test, and 4 complex dependency tests. <br/>
$ taskif [3] : two base cases for if clause, and a if&depend test. (However, this test have a bug that haven't fixed)  <br/>
 <br/>
### Run test example:
$ taskdepend             -> Run all dependency tests.  <br/>
$ taskbase 1             -> Run the first task base test.    <br/>
