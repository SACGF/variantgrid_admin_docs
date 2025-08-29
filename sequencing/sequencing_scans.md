# Sequencing Scans

To access scan info via web site, go via top menu option "Sequencing", then click "Manage disk scans" link

This page lists historical scans, most recent first, including status (Scanning Files/Finished/Error) and time stamps so you can track how long things take. They have the following states: 

* **Created** - Scanning run created, but not yet run  
* **Scanning Files** - Run "find" scripts to produce a list of the sequencing data we are looking for. 
* **Create Models** - Process scanned list of files to create database records. This produces what "should" be there (eg expected FastQ files given SampleSheet.csv) and sets the "data_state" according to whether the file was found in the scan 
* **Scripts and Jobs** - (Currently Disabled) Create + run scripts to produce missing data. Was only ever used to generate Illumina QC. Disabled as VMs mount sequencing data as read only filesystem   
* **Finished** - Completed successfully
* **Error** - Something went wrong. For more details, click "View SeqAuto Run" link in the left most column of grid. See Troubleshooting section below

If a job died unexpectedly (server or worker shutdown, CTR+C on command line) then the status will be wrong, and the status when it died.

To check if a scan is running, see the [Server Status](../admin/server_status.md) page

## What gets searched

To find the various file types, we run a bunch of [scripts](https://github.com/SACGF/variantgrid/tree/vg3_sapath_prod/seqauto/scripts/tau) that produce a text file listing the filenames

The scan starts at /tau/data/clinical_hg38. If a top level directory here contains ".variantgrid_skip_flowcell" - all subdirectories are skipped

If a flowcell has ".variantgrid_skip_flowcell" in it, it is skipped (and all FastQ/BAM/VCF etc inside it)

## Task / Queues

Scans can be triggered manually by clicking the "Scan Disk for Sequencing Data" button, or run on a schedule (see below)

In both cases, a job is added to the *seqauto_single_worker* queue. There is only 1 worker for this queue, to ensure scans don't interfere with each other.

If an auto scan is running, and you hit the "Scan" button, it'll add the scan onto the queue so a 2nd scan will run once the auto scan is complete

## Task / Queues

Scanning is done as a [Celery](https://docs.celeryq.dev/en/stable/) task ```seqauto.tasks.scan_run_jobs.scan_run_jobs```

You can see whether this is running and has any jobs on the [Server Status](../admin/server_status.md) page

If you change the Python code or settings, (eg you fix a bug, or make a new deploy) you need to restart the worker: 

    service celeryd_seqauto_single_worker start
    service celeryd_seqauto_single_worker stop
    service celeryd_seqauto_single_worker restart

## Scheduled Tasks

A scan job is added to the queue on a regular schedule via [Celery Beat](https://docs.celeryq.dev/en/latest/userguide/periodic-tasks.html)

Celery beat config for sequencing scans is in [variantgrid/celery.py](https://github.com/SACGF/variantgrid/blob/master/variantgrid/celery.py)

    if all([settings.SEQAUTO_ENABLED, settings.SEQAUTO_SCAN_DISKS, settings.UPLOAD_ENABLED]):
        scan_run_jobs = 'seqauto.tasks.scan_run_jobs.scan_run_jobs'
        app.conf.beat_schedule.update({
            'seqauto-scan-06': {
                'task': scan_run_jobs,
                'schedule': crontab(hour=6, minute=0),
            },
            'seqauto-scan-19': {
                'task': scan_run_jobs,
                'schedule': crontab(hour=19, minute=0),
            },
        })

This will add the scan job to the queue at 6am and 7pm. Celery beat is running as a service, it also runs LIMs integration, System status reporting jobs. If you change the config, you will need to restart the service. Do so via eg: 

    service celeryd_beat start
    service celeryd_beat stop
    service celeryd_beat restart

## Command line

You can also manually execute on the VM, in VG dir as variantgrid user via:

    python3 manage.py scan_run_jobs 

This also has the ```--reuse-prev-scan-id``` option to re-use a previous run's scan, if you want to avoid the "scanning files" step, and go straight to the "create models" step

Scans triggered on the command line are run immediately with logs displayed to console. They are not in a celery task, so don't wait on queue (so are not isolated). They will have no "Task ID" (which is usually celery job)  

## Logs

Logs from the worker are written to:

    tail -f /var/log/variantgrid/seqauto_single_worker*

## Enabling / Disabling scans

As the "variantgrid" user, login to the server, then the server's settings file

    # Login to VM
    su variantgrid
    /opt/variantgrid
    # Make sure you find the server config - eg "variantgrid/settings/env/vg3upgrade.py"
    ls -l variantgrid/settings/env/$(hostname).py  # May have suffix numbers stripped out
    vi variantgrid/settings/env/$(hostname).py
    
Then add/alter the Python variable to be True/False:

    SEQAUTO_SCAN_DISKS = True

To check whether things worked, start a Django shell:

    python3 manage.py shell
    
then inside the shell:

    In [1]: from django.conf import settings                                                                                
    In [2]: settings.SEQAUTO_SCAN_DISKS                                                                                     
    Out[2]: True

This is a good test to make sure you didn't eg make a syntax error in the Python code, which will cause the service to break if you restart them.

You'll need to restart celery beat (see above) for this to take effect

## Troubleshooting

See [sequencing scan trouble shooting](sequencing_scan_troubleshooting.md)



