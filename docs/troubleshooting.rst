troubleshooting
***************

Diagnosis
=========

The Image Crashes Immediately
-----------------------------

Check Your Inputs
^^^^^^^^^^^^^^^^^

It's possible that the BIDS input is missing essential files. Normal
processing requires at minimum a T1-weighted and a functional MR image pair.
as of v0.0.1, the error reporting for input configurations is incomplete.

The second consideration is that there is a typo in the input system path,
check that there is nothing wrong

Mkdir Permission Denied
^^^^^^^^^^^^^^^^^^^^^^^

This error can occur when Docker is run with the --user flag, and usually
means that group permissions on one or more mounted volumes are not compatible
with your unix group. adding the proper group id with --user id:gid can fix
this.

The Image Crashes in the Middle of Processing
---------------------------------------------

Consult the Logs Directory
^^^^^^^^^^^^^^^^^^^^^^^^^^

Logs for each of the stages are located in subdirectories of output/sub-id/logs/

They usually at least indicate roughly where the pipeline failed, if not the
command which it terminated on.

Check Files
^^^^^^^^^^^

Check for the most recently created files, and use fsleyes, freeview, or
wb_view as appropriate to check for errors.
