# Sequencing Data and Automation

You may want to automatically upload VCFs

It is also useful to associate other sequencing data with a sample (Patient info, EnrichmentKit, QC, gene coverage, gene lists etc) 

You can do this via:

* Scanning filesystem - server runs scripts to search network drive for new data
* API - your pipelines can directly send data to VariantGrid

#### Scanning filesystem

To access scan info via web site, go via top menu option "Sequencing", then click "Manage disk scans" link

This page lists historical scans, most recent first, including status (Scanning/Finished/Error) and time stamps so you can track how long things take. 

Scans can be triggered manually (via "Scan Disk for Sequencing Data" button), or on a schedule. Only 1 scan will ever run at a time - so clicking the button or scheduling it is adding it to the queue that is processed 1 at a time.

You can view more information on a run via link "View SeqAuto Run" in the left most column. This is especially useful if a run failed, as you can see any errors or stack traces which may help you diagnose what went wrong. 

TODO: scheduler, celery beat, services, logs, troubleshooting 

#### API

To call the API, you need a user (created via Django Admin) and a [DRF](https://www.django-rest-framework.org/) API token, which is created on the server via admin or command line:

    python3 manage.py drf_create_token username

The API is JSON REST (get/post JSON to URLs) but there is a helper Python package available 

    python3 -m pip install variantgrid_api 

For more details see:

https://github.com/SACGF/variantgrid_api

