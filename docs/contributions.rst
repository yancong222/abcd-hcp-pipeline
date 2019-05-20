Contribution Guide
******************

Git Flow
========

We prefer that feature additions and bugfixes `are added using the Git Flow workflow. <https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow>`_

- All Pull Requests should then target the `develop` branch, *not* `master`.
- Commits to Master are tagged as a specific release version.
- Develop branch contains bleeding edge code merged in from features.

Updates to DCAN Software/HCP Bash Scripts
=========================================

The python code of the `abcd-hcp-pipeline` repository merely wraps the HCP workflow and constrains it to work with BIDS. Internal image/signal processing logic is contained in other DCAN software dependencies.

In order to update the specifics of the image processing pipeline or the executive summary, functional connectivity preprocessing, you must modify other DCAN repositories.

*Then*, we will tag this repository and modify the Dockerfile to pull from a different tag.

For testing, we recommend *mounting over the existing code in the docker image*:

```
docker run -v /path/to/my/HCP:/opt/pipeline -v ...
```

That way, you don't need to rebuild the image to test minor changes to bash scripts. 

Let's go over the software which could be modified:

DCAN HCP scripts
---------------

function: Modified HCP Pipelines

internal docker location: `/opt/pipeline`

GitHub location: https://github.com/DCAN-Labs/DCAN-HCP

DCAN Bold Processing
--------------------

function: Functional Connectivity Preprocessing following Power 2014.

internal docker location: `/opt/dcan-tools/dcan_bold_proc`

GitHub location: https://github.com/DCAN-Labs/dcan_bold_processing

Executive Summary
-----------------

function: Generates Output QC report and webpage.

internal docker location: `/opt/dcan-tools/executivesummary`

GitHub location: https://github.com/DCAN-Labs/ExecutiveSummary

Custom Clean
------------

function: Remove extraneous files

internal docker location: `/opt/dcan-tools/customclean`

GitHub location: https://github.com/DCAN-Labs/CustomClean


