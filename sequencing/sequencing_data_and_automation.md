# Sequencing Data and Automation

You may want to automatically upload VCFs

It is also useful to associate other sequencing data with a sample (Patient info, EnrichmentKit, QC, gene coverage, gene lists etc). You can do this via:

* Scanning filesystem - server runs scripts to search network drive for new data
* API - your pipelines can directly send data to VariantGrid

#### Scanning filesystem

Scripts are launched on the server that scan the disks for sequencing files. This can be triggered manually or run on a schedule

We are moving away from this system as it relies on VariantGrid understanding the folder structure of lab's sequencing data, and there is an overhead to constantly scanning disks looking for changes.

See [Sequencing Scans](sequencing_scans.md)

#### Sequencing Data API

VariantGrid has an API that allows other programs (eg pipelines generating data) to remotely send it data and trigger VCF uploads.

Using an API means the client is in full control, and can arrange their data however they like, and VG only needs to know about what is new or has changed.

See [Sequencing Data API](sequencing_data_api.md)
