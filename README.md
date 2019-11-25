# Bodur & Rey (2019) Input Files for Underworld Geodynamic Numerical Models 

   This repository includes the input files of the numerical models in the paper entitled **_"The Impact of Rheological Uncertainty on Dynamic Topography Predictions"._** 
   
https://www.solid-earth-discuss.net/se-2019-73/

   We used **_Underworld_** code (https://github.com/OlympusMonds/EarthByte_Underworld) to numerically model the dynamic topography induced by a spherical density anomaly in the mantle. The parameters used in the numerical models are given in the paper. 

The input files are provided with in-file explanations so that the readers can easily recreate the numerical models, and make their own models by changing the parameters of interest. The files in this repository are arranged by experiment names, similar to those in the papers.

## Running the Numerical Models
After dowloading an experiment file to the local disk or supercomputer and unarchiving the contents (as it comes in .zip format), one needs to change the Underworld directory in _lmrStart.xml_. Please see below: 

	<Underworld_Execution>
  	<Underworld_binary> /short/q97/software/underworld2_hdf5p/libUnderworld/build/bin/Underworld </Underworld_binary>
		<!-- replace the line above with your Underworld code directory -->
		<!--<CPUs>#NUMBER</CPUs>--> 
		<supercomputer_mpi_format>true  </supercomputer_mpi_format>
 	</Underworld_Execution>
   
We used 80 CPUs to run each model. They were run in Raijin Supercomputer, NCI in Canberra, Australia. 

In order to run a model, one needs to submit a job file to the Supercomputer. Here is the job submission file for Raijin (submitjob.pbs):

-------------------

	#!/bin/bash
	#PBS -q express
	#PBS -N MyModel (change this with the name of your model and remove this sentence)
	#PBS -l ncpus=80
	#PBS -l mem=600GB
	#PBS -l walltime=4:00:00 
	#PBS -j oe 
	#PBS -P q97 (change this with your own project number and remove this sentence)
	#PBS -l wd 
	#PBS -M omer.bodur@sydney.edu.au  (change this with your e-mail address and remove this sentence)
	#PBS -m bae 

	module unload intel-fc
	module unload intel-cc
	module unload intel-mkl
	module unload openmpi/1.6.3
	module load intel-cc/14.3.174
	module load intel-fc/14.3.174
	module load intel-mkl/14.3.174
	module load openmpi/1.8
	module load hdf5
	module load metis/5.0.2
	module load parmetis/4.0.2
	module load scotch/6.0.0_esmumps
	module load petsc/3.5.0
	python lmrRunModel.py

-------------------

The lmrRunModel.py is provided in this repository.

To run the model, type and execute the following: 

	qsub submitjob.pbs

The outputs will be created in a folder similar to the one below:

	result_384x96x384_reference_model

The numbers (384x96x384) indicate the number of nodes in each spatial axis.


## Post-Processing

The output files are in HDF5 format and one can visualize the output by opening the following file in Paraview:

	XDMF.temporalFields.xmf

The dynamic topography is found by applying first _CellDatatoPointData_ and then _Calculator_ filters to __XDMF.temporalFields.xmf__. In the _Calculator_ filter options, type the following to the box below the _Result Array Name_ and _Apply_  to calculate the amplitude of dynamic topography.

	((StressField_4-PressureField)/(-9.8*3235))*jHat

Apply _Slice_ filter with Origin= (0, 0 ,0) and Normal: (0, 1, 0). 

Plot the dynamic topography on the surface by applying _PlotOverLine_ across the model (Point1: -1.92e+06, 0, 0 and Point2: 1.92e+06,0,0) 

For any question:

omer.bodur@sydney.edu.au or omer@uow.edu.au

Ömer Faruk Bodur

2019, Sydney.
