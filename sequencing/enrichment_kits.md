An EnrichmentKit is a lab method to enrich a sample for the DNA regions you are interested in.
For instance an exome or custom gene capture kit, or amplicons.

### EnrichmentKit Setup

EnrichmentKits are automatically created based on directories flowcells are detected in via scanning sequencing dirs,
and entries in the [sample_sheet](SampleSheet.csv)

You need to upload:

* A Bed containing capture regions
* A GeneList containing symbols captured

If the canonical transcripts file contains only genes in the kit, you can turn it into a gene list via:   

    grep -v "^#" enrichment_kit.GeneTable.tsv | cut -f 1 > /tmp/gene_list.txt

Setup canonical transcripts (if this differs from existing canonical transcripts)

    python3.8 manage.py import_canonical_transcript --genome-build=GRCh38 --annotation-consortium=RefSeq enrichment_kit.GeneTable.tsv

In Django admin, go to **seqauto** -> **EnrichmentKits** then select the kit.

Select the appropriate details for your kit (eg bed/genelist etc you uploaded) and click save.