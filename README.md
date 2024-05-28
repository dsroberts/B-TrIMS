## Notes for NCI (Gadi)
**November 2019**
### Quickstart
```
# Instructions for Gadi

#
# Load module for netcdf
#
module load intel-compiler/2019.3.199
module load netcdf/4.7.1

#
# Clone the git repository
#
git clone https://github.com/jpevans/B-TrIMS.git
cd B-TrIMS

#
# Compile with OpenMP
#
# gfortran equivalent:
# gfortran -ffree-line-length-512 -fopenmp -O3 -c B-TrIMS.f90
ifort -03 -fopenmp -c B-TrIMS.f90

#
# Link the executable
#
# gfortran equivalent
# gfortran -fopenmp -O3 -L/apps/netcdf/4.7.1/lib -lnetcdff B-TrIMS.o -o main
ifort -O3 -fopenmp -L/apps/netcdf/4.7.1/lib -lnetcdff B-TrIMS.o -o main

#
# Run B-TrIMS from 11 Jan 1980 to 13 Jan 1980, storing the results in subdirectory ./outputs
#
chmod u+x main
mkdir outputs

#
# PBS jobscript for Gadi
#
cat - <<EOF >191119-btrims.sh
#!/bin/bash
#PBS -q normal
#PBS -l walltime=10:00:00
#PBS -l ncpus=48
#PBS -l mem=64GB
#PBS -l storage=gdata/hh5 
cd $PBS_O_WORKDIR
ulimit -s unlimited
./main 11 01 1980 13 01 1980 ./outputs

EOF

#
# Submit the job to Gadi normal queue
#
qsub 191119-btrims.sh

#
# Example resource usage via qstat
#
Every 60.0s: qstat -f 149802                                     gadi-login-04.gadi.nci.org.au: Wed Nov 20 10:19:04 2019

Job Id: 149802.gadi-pbs
    Job_Name = gadicascade.sh
    Job_Owner = sy0928@gadi-login-04.gadi.nci.org.au
    resources_used.cpupercent = 4252
    resources_used.cput = 23:11:41
    resources_used.jobfs = 0b
    resources_used.mem = 15460196kb
    resources_used.ncpus = 48
    resources_used.vmem = 15460196kb
    resources_used.walltime = 00:32:29
    job_state = R
    queue = normal-exec
```
### Data sources
```
$ ls -lh /g/data/hh5/tmp/w28/jpe561/back_traj/
total 4.7M
drwxrwsr-x+ 2 jpe561 hh5 4.0K Oct  5  2018 watershed
drwxrwsr-x+ 2 jpe561 hh5 3.6M Mar 15  2019 wrfhrly
drwxrwsr-x+ 2 jpe561 hh5 1.1M Dec 21  2018 wrfout
```

** Required: a brief introduction to what is the format and contents of these files

### Verify that scaling with OpenMP gives consistent output
#### Visualise with Panoply
First, download and [install Java](https://www.java.com)

Then download and extract [Panoply](https://www.giss.nasa.gov/tools/panoply/download)
