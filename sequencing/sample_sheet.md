### SampleSheet.csv

Illumina SequencingRuns have a file called "SampleSheet.csv" which lists the 

Many years ago we noticed this file became the main handover point between wet and dry lab staff, and asked Illumina
if they would ignore any extra columns in this file, so we can add as many extra columns as we want to annotate our samples.      

#### Special Columns

Enrichment Kits are assigned to rows in the SampleSheet.csv via the *Panel* column. If all samples are assigned to the same kit, the SequencingRun is also assigned to that kit. 

Some panels can change over time (eg it's a custom kit or the manufacturer adds some new genes) - in which case you can use panel versions.

To do this, add a **PanelVersion** field in the SampleSheet.csv
