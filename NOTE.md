# Note on benchmark setup

## 7-day 4x5 timing

AWS environment: `ami-06f4d4afd350f6e4c`. See http://cloud.geos-chem.org for usage.

- On EC2, at least run the default 20-min simulation once before running real benchmark. This ensures that input data get pulled to EBS.
- Turning off all output fields (comment out everything in HISTORY.rc). This is critical for getting a stable & good performance on Pleiades's shared file system (https://www.nas.nasa.gov/hecc/support/kb/lustre-best-practices_226.html).

## Hyperthreading or not

Tests show that turning on Hyperthreading increases GEOS-Chem performance (OpenMP-only) by 10%~20%. Thus, for all the standard tests, hyperthreading is enabled. See *_nohyper.log for runs with hyperthreading disabled. 

Hyperthreading is enabled on EC2 by default. On Pleiades, request node by something like `qsub -I -lselect=1:ncpus=24:ompthreads=48:model=has,walltime=3:00:00 -q normal`. Simply changing `OMP_NUM_THREADS` seem to have the same effect.

Additional notes:
- Hyperthreading on AWS:
https://aws.amazon.com/blogs/compute/disabling-intel-hyper-threading-technology-on-amazon-linux/
- Hyperthreading on Pleiades:
https://www.nas.nasa.gov/hecc/support/kb/porting-with-openmp_103.html
https://www.nas.nasa.gov/hecc/support/kb/hyperthreading_488.html

## Container performance

AWS container environment: `ami-040f96ea8f5a0973e`

Docker image used: https://hub.docker.com/r/geoschem/gc_model

No visible performance difference introduced by containers.

## 1-month 2x2.5 timing

AWS environment: `ami-06f4d4afd350f6e4c`. Follow https://cloud-gc.readthedocs.io/en/latest/chapter02_beginner-tutorial/research-workflow.html to set up 2x2.5 simulation.

Save advected species at daily frequency. In HISTORY.rc, set:

    SpeciesConc.frequency:      00000001 000000
    SpeciesConc.duration:       00000001 000000
    SpeciesConc.mode:           'time-averaged'
    SpeciesConc.fields:         'SpeciesConc_?ADV?             ', 'GIGCchem',
