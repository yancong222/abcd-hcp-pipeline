# ABCD-HCP BIDS fMRI Pipeline

[![DOI](https://zenodo.org/badge/171551109.svg)](https://zenodo.org/badge/latestdoi/171551109)

This software provides an interface for processing [BIDS-formatted MRI datasets](https://bids-specification.readthedocs.io/en/stable) using the [DCAN-HCP pipeline](https://github.com/DCAN-Labs/DCAN-HCP) and supporting modules including [DCANBOLDProcessing](https://github.com/DCAN-Labs/dcan_bold_processing) and [DCAN Executive Summary](https://github.com/DCAN-Labs/ExecutiveSummary).

Docker images are now available at the [DCAN Docker Hub repo](https://hub.docker.com/r/dcanumn/abcd-hcp-pipeline).

Docker images for older versions (<= 0.0.4) available at the [old repo](https://hub.docker.com/r/dcanlabs/abcd-hcp-pipeline).

## Installation

### FreeSurfer License

This software includes a FreeSurfer 5.3.0 installation. A FreeSurfer License, available separately, is required to run. 

[Follow this link for a FreeSurfer License](https://surfer.nmr.mgh.harvard.edu/fswiki/License)

### Using Docker

Before running, you will need to load the image onto your Docker service by running the following command:

```{bash}
docker pull dcanumn/abcd-hcp-pipeline
```

If you receive a "no space left on device" error during this pull process,
you may need to clean up any old/dangling images and containers from the docker registry, and possibly increase the amount of space allocated to Docker.

### Using Singularity

You can either pull the image from the Docker repository, or build it from the repository for the image to be saved in the working directory.

```{bash}
singularity pull docker://dcanumn/abcd-hcp-pipeline

singularity build abcd-hcp-pipeline.sif docker://dcanumn/abcd-hcp-pipeline
```

These are essentially the same, but in the latter case you have control over the name of the .sif file.

### Install and run from source (not recommended)

Installing and running the pipeline from source is not recommended, but is an option if Docker and Singularity are not available on your system. This requires installing the dependencies specified in the pipeline Dockerfile, including the contents of the [DCAN internal-tools](https://github.com/DCAN-Labs/internal-tools) and [DCAN external-software](https://github.com/DCAN-Labs/external-software) Dockerfiles.

Environment variables must be set in accordance with `/app/SetupEnv.sh`; then the pipeline may be run with `python3 /app/run.py <args>` 

## Usage:

### Options:

```{bash}
The Developmental Cognition and Neuroimaging (DCAN) Labs fMRI Pipeline [1].
This BIDS application initiates a functional MRI processing pipeline built
upon the Human Connectome Project's minimal processing pipelines [2].  The
application requires only a dataset conformed to the BIDS specification, and
little-to-no additional configuration on the part of the user. BIDS format
and applications are explained in detail at http://bids.neuroimaging.io/

positional arguments:
  bids_dir              Path to the input BIDS dataset root directory. Read
                        more about the BIDS standard in the link in the
                        description. It is recommended to use Dcm2Bids to
                        convert from participant dicoms into BIDS format.
  output_dir            Path to the output directory for all intermediate and
                        output files from the pipeline, which is also where
                        logs are stored.

optional arguments:
  -h, --help            show this help message and exit
  --version, -v         show program's version number and exit
  --participant-label ID [ID ...]
                        Optional list of participant IDs to run. Default is
                        all IDs found under the BIDS input directory. The
                        participant label does not include the "sub-" prefix
  --session-id SESSION_ID [SESSION_ID ...]
                        filter input dataset by session id. Default is all ids
                        found under the subject input directory(s). A session
                        id does not include "ses-"
  --freesurfer-license LICENSE_FILE
                        If using docker or singularity, you will need to
                        acquire and provide your own FreeSurfer license. The
                        license can be acquired by filling out this form:
                        https://surfer.nmr.mgh.harvard.edu/registration.html
  --all-sessions        Collapses all sessions into one when running a
                        subject.
  --ncpus NCPUS         Number of cores to use for concurrent processing and
                        algorithmic speedups. Warning: causes ANTs and
                        FreeSurfer to produce non-deterministic results.
  --stage STAGE         Specify a subset of stages to run.
                        Can be used to rerun some or all of the pipeline after
                        completing once, or resume an incomplete runthrough.
                        If a single stage name is given, the pipeline with be 
                        started at that stage. If a string with a ":" is given, 
                        a stage name before the ":" will tell the pipeline where to 
                        start and a stage name after the ":" will tell it where 
                        to stop. If no ":" is found, the pipeline will start 
                        with the stage specified and run through 
                        ExecutiveSummary (or CustomClean/ABCDTask, if specified).
                        Valid stage names: 
                        PreFreeSurfer, FreeSurfer, PostFreeSurfer, FMRIVolume, 
                        FMRISurface, DCANBOLDProcessing, ExecutiveSummary, CustomClean'
  --bandstop LOWER UPPER
                        Parameters for motion regressor band-stop filter [3]. It
                        is recommended for the boundaries to match the inter-
                        quartile range for participant group respiratory rate
                        (breaths per minute), or to match BIDS physio data
                        directly [3]. These parameters are highly recommended
                        for data acquired with a frequency of greater than 1
                        Hz (TR less than 1 second). UPPER cannot exceed the
                        Nyquist folding frequency in bpm ( 0.5 * (60 / TR) ). 
                        Default is no filter.
                        
  --abcd-task           (DEPRECATED. For task analysis of this pipeline's output,
                        refer to DCAN abcd-tfmri-pipeline at
                        https://github.com/DCAN-Labs/abcd-bids-tfmri-pipeline)
  
                        Runs ABCD task data through task fMRI analysis, adding
                        this stage to the end. 

  --custom-clean JSON   Runs DCAN cleaning script after the pipeline
                        completes successfully to delete pipeline outputs 
                        base on the file structure specified in the custom-
                        clean JSON. Required for the custom clean stage.
  --study-template HEAD BRAIN
                        Template head and brain images for intermediate
                        nonlinear registration and masking, effective where
                        population differs greatly from average adult, e.g. in
                        elderly populations with large ventricles.
  --ignore {func,dwi}   Ignore a modality in processing. Option can be
                        repeated.

Runtime options:
  Special changes to runtime behaviors. Debugging features.

  --check-outputs-only  Checks for the existence of outputs for each stage
                        then exit. Useful for debugging.
  --print-commands-only
                        Print run commands for each stage to shell then exit.
  --ignore-expected-outputs
                        Continues pipeline even if some expected outputs are
                        missing. Note that optional outputs, e.g. intermediate files
                        from T2w preprocessing, are not included in the 
                        "expected output". Refer to the included 
                        /app/pipeline_expected_outputs.json for the list
                        of expected outputs per stage.

References
----------
[1] Sturgeon, D., Perrone, A., Earl, E., & Snider, K. 
DCAN_Labs/abcd-hcp-pipeline. DOI: 10.5281/zenodo.2587209. (check on 
zenodo.org for a version-specific DOI/citation)
[2] Glasser, MF. et al. The minimal preprocessing pipelines for the Human
Connectome Project. Neuroimage. 2013 Oct 15;80:105-24.
10.1016/j.neuroimage.2013.04.127
[3] Fair, D. et al. Correction of respiratory artifacts in MRI head motion
estimates. Biorxiv. 2018 June 7. doi: https://doi.org/10.1101/337360
[4] Dale, A.M., Fischl, B., Sereno, M.I., 1999. Cortical surface-based
analysis. I. Segmentation and surface reconstruction. Neuroimage 9, 179-194.
[5] M. Jenkinson, C.F. Beckmann, T.E. Behrens, M.W. Woolrich, S.M. Smith. FSL.
NeuroImage, 62:782-90, 2012
[6] Avants, BB et al. The Insight ToolKit image registration framework. Front
Neuroinform. 2014 Apr 28;8:44. doi: 10.3389/fninf.2014.00044. eCollection 2014.
```

### Example: minimal run command (Docker)
To call using Docker:

```{bash}
docker run --rm \
    -v /path/to/bids_dataset:/bids_input:ro \
    -v /path/to/outputs:/output \
    -v /path/to/freesurfer/license:/license \
    dcanumn/abcd-hcp-pipeline /bids_input /output --freesurfer-license=/license.txt [OPTIONS]
```
Note that the mount flag `-v` follows `docker run`, as it is a Docker option, whereas `--freesurfer-license` follows `dcanumn/abcd-hcp-pipeline`, as it is an option passed into the pipeline itself.

### Example: minimal run command (Singularity)
To call using Singularity:

```{bash}
env -i singularity run \
    -B /path/to/bids_dataset:/bids_input \
    -B /path/to/outputs:/output \
    -B /path/to/freesurfer/license.txt:/opt/freesurfer/license.txt \
    ./abcd-hcp-pipeline.sif /bids_input /output --freesurfer-license=/opt/freesurfer/license.txt [OPTIONS]
```

(Notice that the license is now mounted directly into the freesurfer folder, 
and the call to singularity is prefaced by "env -i")

### Advanced Example: run with bandstop filter, study-templates, multiple CPUs

In the case of multiband (fast TR) data, it is recommended to employ a band-stop filter to mitigate artifactually high motion numbers.  The band-stop filter used on motion regressors prior to frame-wise displacement calculation has parameters which should be chosen based on subject respiratory rate.

In the example command below we specify a bandstop filter, in breaths per minute (bpm), to be applied in the DCANBOLDProcessing stage.
(See reference [3] above for information on bandstop filtering of respiratory motion artifacts in fMRI.) 
The interval `18.582 25.726` is the interquartile range (25th to 75th percentile) of the subject demographic.
Respiratory rate data may be included in the BIDS dataset, or can be estimated post hoc by utilities such as
[DCAN movement_regressors_power_plots](https://github.com/DCAN-Labs/movement_regressors_power_plots).

Additionally, we specify study template head and brain images which are used as an intermediate registration target
when registering the subject's T1w to the standard-space template. For these study templates we will mount an additional path into the Docker container `-v /path/to/template/folder:/atlases`, containing the files `study_head.nii.gz` and `study_brain.nii.gz`.
Then, we add these templates in as the study template head and brain `--study-template /atlases/study_head.nii.gz /atlases/study_brain.nii.gz` using the path as mounted into the Docker container `/atlases`.

We have also requested 4 cores for faster processing: `--ncpus 4`


```{bash}
docker run --rm \
    -v  /path/to/bids_dataset:/bids_input:ro \
    -v /path/to/outputs:/output \
    -v /path/to/freesurfer/LICENSE:/license:ro \
    -v /path/to/template/folder:/atlases \
    dcanumn/abcd-hcp-pipeline /bids_input /output \
        --freesurfer-license /license \
        --bandstop 18.582 25.726 \
        --study-template /atlases/study_head.nii.gz /atlases/study_brain.nii.gz \
        --ncpus 4
```

### Pipeline options for specific datasets

`--study-template`: For elderly or neurodegenerative populations, adding a "study template" tends to improve results. This is generally constructed using ANTs to build an average template of your subjects. This template is then used as an intermediate warp stage, assisting in nonlinear registration of subjects with large ventricles.

`--abcd-task` is not compatible with a BIDS folder structure, e.g. [DCAN file-mapper](https://github.com/DCAN-Labs/file-mapper) should **not** be used to map the pipeline output into a BIDS derivative if this option is to be used. 

Additionally, `--abcd-task` is deprecated and [DCAN abcd-tfmri-pipeline](https://github.com/DCAN-Labs/abcd-bids-tfmri-pipeline) is now our recommended tool
for task analysis of this pipeline's output.


### Supported and unsupported BIDS metadata fields

This pipeline requires input be a [BIDS-formatted MRI dataset](https://bids-specification.readthedocs.io/en/stable).

Be aware that the pipeline only recognizes a subset of the fields included in the BIDS specification for MRI and modality-agnostic files. Input with unrecognized BIDS fields may cause errors or other unexpected behavior. 

Recognized fields include the following:

general: `sub-`, `ses-`

in `anat` directory: `_T1w`, `_T2w` 

in `fmap` directory: `dir-_`, `_epi` 

in `func` directory: `task-`, `run-`, `_bold`  

**not** recognized: 

`echo-`, `acq-`, `desc-`, `ce-` `_dwi`, others...

Consult https://bids.neuroimaging.io/ for more information and for tools which assist with converting data into BIDS format.
Our favorite is [Dcm2Bids](https://github.com/cbedetti/Dcm2Bids).

### Fieldmap support for FSL topup / distortion correction

For distortion correction of anatomical and functional data using [FSL topup](https://fsl.fmrib.ox.ac.uk/fsl/fslwiki/topup), the input `fmap` directory must contain BIDS-compliant "PEPolar" spin echo fieldmap images as specified [here](https://bids-specification.readthedocs.io/en/stable/04-modality-specific-files/01-magnetic-resonance-imaging-data.html#fieldmap-data). If present, topup correction is automatically enabled.

The spatial dimensions and voxel size of the spin echo fieldmaps must be the same as the corresponding runs in the subject's `func` directory as resampling is not implemented. 

For specified use of spin echo field maps, i.e. mapping a pair to each individual functional run, it is necessary to insert the `IntendedFor` field into the BIDS input sidecar JSONs, which specifies which functional run(s) a field map is intended for.  This field is explained in greater detail within the BIDS specification.

This software will resolve to using spin echo field maps if they are present, then gradient field maps, then None, consistent with best observed performances. Note that there are no errors or warnings if multiple modalities are present.

### Functional runs with different acquisition parameters

To avoid errors, acquisition parameters (e.g. voxel dimensions, TR, phase encoding direction) should be identical across functional runs within a BIDS session. An exception is the number of frames, which does not need to be the same for all runs.

We recommend that sets of runs with differing acquisition parameters be processed as separate BIDS sessions. Also be aware the pipeline does not recognize BIDS fields such as `acq-` and `desc-` so those should not be used to differentiate runs with different acquisition parameters. 

### Pipeline outputs

The outputs are organized in the following structure:

```
output_dir/
|__ sub-id
    |__ ses-session
        |__ files
        |   |__DCANBOLDProc<ver>
        |   |__summary_DCANBOLDProc<ver>
        |   |  |__executivesummary
        |   |__ MNINonLinear
        |   |   |__ fsaverage_LR32k
        |   |   |__ Results
        |   |__ T1w
        |   |   |__ id
        |   |__[T2w]   
        |   |__ task-<taskname>
        |__ logs
```

#### files

- `summary_DCANBOLDProc<ver>/executivesummary`: The .html file within can be opened for quality inspection of pipeline results.
- `MNINonLinear`: Contains the final space results of anatomy in 164k resolution.
- `MNINonLinear/fsaverage_LR32k`: Final space anatomy in 32k resolution, where functional data is ultimately projected.
- `MNINonLinear/Results`: Final space functional data in the CIFTI 91282 'grayordinates' format. Runs within each BIDS task are concatenated.
- `T1w`: Contains native space anatomical data as well as intermediate preprocessing files.
- `T1w/id`: The participant ID folder within T1w is the FreeSurfer subject folder.
- `T2w`: T2w native space data, if T2w for subject was provided 
- `task-taskname`: These folders contain intermediate functional preprocessing files from the fMRIVolume stage.

#### logs

`logs` contains the log files for each stage. In the case of an error, consult these files *in addition to* the standard error and standard output of the app itself (by default this is printed to the command line).

`status.json` codes:

- unchecked: 999
- succeeded: 1
- incomplete: 2
- failed: 3
- not_started: 4

### Notes: CPU and disk usage

The pipeline may take over 24 hours if run on a single core.  It is recommended to use at least 4 cores and allow for at least 12GB of memory total (so at least 3GB per core) to be safe.  For sessions containing multiple runs fMRI processing can be done in parallel, so using a number of cores which evenly divides your number of runs is optimal.

Temporary/Scratch space:  All intermediate processing is done in the designated output folder. Be sure this location has sufficient disk space and read/write performance for your processing jobs.


### Other notes

Development on DWI processing and diffusion fieldmap support has been discontinued. For processing diffusion data, consider [QSIPrep](https://qsiprep.readthedocs.io/en/latest/) or other alternatives.

The ideal motion filtering parameters for `--bandstop` have not been robustly tested across repetition times or populations outside of adolescents, so the proper range should be decided upon carefully. Consult reference [3] in the usage for more information.

Additional issues are documented on the [GitHub issues page](https://github.com/DCAN-Labs/abcd-hcp-pipeline/issues). Users are also encouraged to use the issues page to submit their own bug reports, feature requests and other feedback.


