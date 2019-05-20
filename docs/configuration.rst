configuration
*************

Passing in Files
================

When running docker or singularity, the container which runs the pipeline has 
a separate `filesystem`.  This is why we have `-v` flags passed into docker 
for each input folder.

When you provide the flag:

.. code-block:: sh

    docker run -v /path/to/my/files:/files

the docker image now sees contents of the folder `/path/to/my/files` inside of 
`/files`.  Hence for pipeline inputs, you will need to use this path.

Suppose you are providing a custom atlas image and brain for procesing elderly 
or large ventricle patients:

/path/to/my/atlases
              ./atlas_head.nii.gz
              ./atlas_brain.nii.gz

we can now do this (omitted input/output mounts for brevity):

.. code-block:: sh

    docker run -v /path/to/my/atlases:/atlas \
        abcd-hcp-pipeline /bids_input /output \
            --study-template /atlases/atlas_head.nii.gz /atlases/atlas_brain.nii.gz



