quickstart
**********

Installation
============

The image can be downloaded onto the Docker service, or saved to disk as a 
Singularity image.

Docker
------

.. code-block:: sh

   docker pull dcanlabs/abcd-hcp-pipeline

Singularity
-----------

.. code-block:: sh

   singularity pull docker://dcanlabs/abcd-hcp-pipeline
   singularity build abcd-hcp-pipeline.img docker://dcanlabs/abcd-hcp-pipeline

Acquiring a FreeSurfer License
------------------------------

You will need to request a 
`FreeSurfer License <https://surfer.nmr.mgh.harvard.edu/registration.html>`_ 
for running this pipeline.

Run a Subject
-------------

suppose I have a bids dataset:

/path/to/my/data
             ./sub-12345
             ./sub-33333

and I want to output the pipeline:

/path/to/my/outputs

and I've acquired my FreeSurfer License file:

/path/to/my/license.txt

.. code-block:: sh

    docker run \
        -v /path/to/my/data:/bids_input \
        -v /path/to/my/outputs:/output \
        -v /path/to/my/license.txt:/license \
        abcd-hcp-pipeline:latest /bids_input /output --freesurfer-license=/license

that's it!

