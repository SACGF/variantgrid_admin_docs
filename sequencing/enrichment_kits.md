An EnrichmentKit is a lab method to enrich a sample for the DNA regions you are interested in.
For instance an exome or custom gene capture kit, or amplicons.



### Setup

EnrichmentKits are automatically created based on directories flowcells are detected in via scanning sequencing dirs,
and entries in the [sample_sheet](SampleSheet.csv)

You need to upload:

* A Bed containing capture regions
* A GeneList containing symbols captured

Setup canonical transcripts (if this differs from existing canonical transcripts)

python3.8 manage.py create_gene_coverage_canonical_transcripts

In Django admin, go to **seqauto** -> **EnrichmentKits** then select the kit.

Select the appropriate details for your kit (eg bed/genelist etc you uploaded) and click save.