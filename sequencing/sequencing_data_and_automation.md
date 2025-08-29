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

See [Sequencing Scans](sequencing_scans.md)

#### Sequencing Data API

VariantGrid has an API that allows clients to read and create sequencing data, and trigger VCF uploads.

It's a JSON REST API (get/post JavaScript data structures to URLs) so can be called from any language, but we have a helper Python package vg_api available to simplify things.

You need an administrator to create an API token for access.

See [Sequencing Data API](sequencing_data_api.md)
