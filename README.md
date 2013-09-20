# cmpy_bayes #

**Note:** Currently in heavy development.

A collection of Python scripts and a package of utility functions to make
Bayesian inference of unifilar hidden Markov models (uHMMs) and
epsilon-machines with Computation Mechanics Python, aka
[CMPy](http://cmpy.csc.ucdavis.edu/) easier.

## Dependencies ##

* [CMPy](http://cmpy.csc.ucdavis.edu/)

CMPy should be installed with the `--cython` switch to allow for use of the
enumeration library.  Also, note that CMPy has a quite a few
[dependencies](http://cmpy.csc.ucdavis.edu/installation.html) that
are required to use these scripts.

## Installation ##

Get the code from github in the usual manner.  In a suitable directory clone
the repository using:

    git clone git@github.com:cstrelioff/cmpy_bayes.git

Install using:

    sudo python setup.py install

Or, for a local install, when administrator permissions are not available:

    python setup.py install --user

If you have issues with the scripts when using the `--user` switch, consult the
[local installation note](LOCALINSTALL.md).  The scripts are designed to be run
as system tools, allowing for execution from any directory without directly
calling python.

## Scripts ##

All of these scripts should install as system scripts, making use from the
command line easy.  To get help for a script, type

    scriptname --help

### cbayes_create_process_datafile.py ###

Create a data file that works nicely with other cbayes scripts.  Can use
any machine in `cmpy.machines` that **has default parameters**.  This means
the process can be instantiated using:

em = cmpy.machines.ProcessName()

### cbayes_enumerate_AddToDB.py ###


Use the CMPy enumeration method to test all specified uHMM topologies against
the specified datafile.  All valid model topologies (provided data can be
generated by the model) are written to the specified DB (really just a
directory, to keep things simple) for later use.

### cbayes_enumerate_PriorAddToDB.py ###

This script is similar to `cbayes_enumerate_AddToDB.py` except that the focus
is on the prior over models instead of the posterior.  The objective is to
understand what the setting of hyper-parameters for priors at various levels
says about stated a priori assumptions.  The resulting directory can be used
for sampling from the prior over model topologies.

### cbayes_enumerate_Sample.py ###

This script generates samples from the prior, or posterior, over model
topologies using the output from other scripts.  Create pickled `InferEM`
instances using

* `cbayes_enumerate_AddToDB.py`
or
* `cbayes_enumerate_PriorAddToDB.py`

A dictionary of model probabilities can then be created using output from the
above scripts and running

* `cbayes_enumerate_CalcProbs.py`

This script uses these outputs to generate sample epsilon-machines or uHMMs and
writes pickled instances of the machines to a directory in the DB.

### cbayes_enumerate_ProcessSamples.py ###

This script processes the samples generated using `cbayes_enumerate_Sample.py`.
A single file is created with basic information about each sampled machine
including machine id, number of states, number of edges, Cmu, hmu etc. See the 
function `create_sample_summary_file` in
[cmpy_bayes.util_infer_db.py](cmpy_bayes/util_infer_db.py) for details.

### cbayes_bash_enumerate_convergence.py ###

Create a bash script to run a sequence of `cbayes_enumerate_` scripts on a
specified data file.  In this case, the focus is on convergence of inference
by considering subsegments, using the single data file, of increasing length.

### cbayes_slurm_enumerate_convergence.py ###

Create a slurm script to run a sequence of `cbayes_enumerate_` scripts on a
specified data file.  In this case, the focus is on convergence of inference
by considering subsegments, using the single data file, of increasing length.

This version of the convergence analysis is designed for bigger jobs, using
slurm on a cluster.  For small jobs, try the bash version.

