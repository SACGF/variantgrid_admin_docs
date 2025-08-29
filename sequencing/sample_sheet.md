### SampleSheet.csv

Illumina SequencingRuns have a file called "SampleSheet.csv" which lists the sample names, barcodes and other info.

VG stores this as a *SampleSheet* model, with *SequencingSample* representing each rows. This is eventually linked through FastQ/Bams etc to VCF samples

Many years ago we noticed the SampleSheet was the main handover point between wet and dry lab staff, so have been adding extra columns to annotate our samples (such as kits, patient record codes etc)      

VariantGrid allows storing key/values for a *SequencingSample* in *SequencingSampleData*

Because the columns are highly specific to each lab/disk setup, the core code raises a *sequencing_run_sample_sheet_created_signal* signal, which lab/deployment specific code can listen for and run  

### CurrentSampleSheet

People often modify sample sheets, to correct sample names etc. We reload SampleSheets (re-sent via API or reloaded in scan due to modification date/hash) 

SequencingRuns can have many SampleSheets but only one CurrentSampleSheet - which represents the one on the disk, that was most recently used to generate downstream files 

When a SampleSheet is assigned to a sequencing run (1st time, or later modifications) the signal *sequencing_run_current_sample_sheet_changed* is raised

### SA Pathology Special Columns

**Panel** , **PanelVersion** - These are used to create an *EnrichmentKit* if it doesn't exist, and assign it to SequencingSample. If all samples are assigned to the same kit, the SequencingRun is also assigned to that kit. SA Path VG deployment handles via listening for *sequencing_run_sample_sheet_created_signal* and handling via *sapath.signals.sapath_enrichment_kits.sapath_sample_sheet_created_handler*

**SAPOrderNumber** - These are de-identified codes used to represent a pathology test order. We can use this to link up to our LIMS/Medical record system patient information (Helix). SA Path code listens for *sequencing_run_current_sample_sheet_changed_handler* signal, handles via *sapath.signals.sample_sap_order_matching.sapath_sequencing_run_current_sample_sheet_changed_handler*  
