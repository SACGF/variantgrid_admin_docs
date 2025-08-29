## Sequencing Scan Troubleshooting

### Scans not running

Check timestamps on the historical runs grid, when was it last run?

Check seqauto_single_worker on server status page.

On the serveR:

* Check the logs
* Check if processes are running:

    # Check if worker is running
    ps aux | grep seqauto_single_worker
    # Check if celery beat is running
    ps aux | grep beat

**Suggested Fixes**: Start and stop workers. If these fail, check system logs.

If the workers fail, it is likely due to code/settings which will also fail if you open django shell

### Scans Failing

The first step is to work out what's happening. Look at the details page for the most recently failed SeqAutoRun

Hopefully it should give you a stack trace and the run that failed.

#### General tips for failed runs

* Remember you can always disable a particular run using .variantgrid_skip_flowcell - this will allow you to keep scans going while you fix the particular run
* If a sequencing scan fails during "create models" stage, it's possible the state of the record being processed is bad, for instance the GOIs or patients not properly linked to samples as described in [SampleSheet](sample_sheet.md) It's probably a good idea to delete the SequencingRun (through webapp) before restarting the next scan  
* If a scan fails, maybe give it another go and it'll work this time

#### Weird one-off database integrity errors, file not found errors etc

We scan files, then load existing ones, then process them for ~30 mins.

If one of the database files (eg someone deletes a SequencingRun) or files is deleted or moved during processing, things will fail.

Example:

    seqauto.models.models_seqauto.SequencingSample.DoesNotExist: SequencingSample matching query does not exist.

Example:

    django.db.utils.IntegrityError: insert or update on table "seqauto_unalignedreads" violates foreign key constraint "seqauto_unalignedrea_fastq_r1_id_bd7bd92c_fk_seqauto_f"
    DETAIL:  Key (fastq_r1_id)=(476466) is not present in table "seqauto_fastq".

Example:

    DETAIL:  Key (sequencing_run_id)=(X) is not present in table "seqauto_sequencingrun".

Example:

    FileNotFoundError: [Errno 2] No such file or directory: 'X'

**Fix**: Run the scan again. For many strange errors, try re-running and hopefully it will have fixed itself.

#### Scan files permission error 

Example: 

    Type: <class 'subprocess.CalledProcessError'>, Value: Command '['/opt/variantgrid/seqauto/scripts/tau/find_flowcells.sh', '/opt/variantgrid/media_root/scan_resources/seqauto_run_5493']' returned non-zero exit status 1

This is almost always due to a new directory appearing with permission access problems. Unfortunately I don't keep the error logs here, so you will need to copy/paste the command run and execute it on the shell

    /opt/variantgrid/seqauto/scripts/tau/find_flowcells.sh /opt/variantgrid/media_root/scan_resources/seqauto_run_5493

If you look at the output of the command, it should tell you which directory/file it had a permission error on

**Fix**: Alter permissions to ensure the file and all directories leading up to it are readable  

#### Create models permission error

      File "/data/variantgrid/seqauto/models/models_seqauto.py", line 139, in get_file_last_modified
        return path.stat().st_mtime
      File "/usr/lib/python3.8/pathlib.py", line 1198, in stat
        return self._accessor.stat(self)

    PermissionError: [Errno 1] Operation not permitted: 'X/4_QC/sequencing_stats/RunParameters.xml'

This is due to a *previously* accessible file becoming not visible due to permissions. At least we record the path this time

**Fix**: Alter permissions to ensure the file and directory are readable  

#### Create models missing files:

Example:

      File "/data/variantgrid/seqauto/sequencing_files/create_resource_models.py", line 387, in process_sequencing_run
        instrument_name, experiment_name = get_run_parameters(run_parameters_dir)
      File "/data/variantgrid/seqauto/illumina/run_parameters.py", line 35, in get_run_parameters
        raise ValueError(msg)
    ValueError: Couldn't find RunParameters.xml or runParameters.xml in SequencingRun/4_QC/sequencing_stats

**Fix**: Copy in file. If you don't have it, disable SequencingRun dir via ".variantgrid_skip_flowcell"  

#### SampleSheet.csv parsing error


      File "/data/variantgrid/seqauto/illumina/samplesheet.py", line 120, in convert_sheet_to_df
        df = pd.read_csv(csv_file, index_col=None, comment='#', skip_blank_lines=True, skipinitialspace=True, dtype=str)
    pandas.errors.ParserError: Error tokenizing data. C error: Expected 31 fields in line 18, saw 32

This is likely due to someone editing a SampleSheet.csv by hand

**Fix**: Fix SampleSheet.csv, either by hand or open it in a spreadsheet, save and export as CSV. If can't fix disable SequencingRun dir via ".variantgrid_skip_flowcell"

###  Code fails during create models

Example:

      File "/data/variantgrid/seqauto/sequencing_files/create_resource_models.py", line 66, in create_samplesheet_samples
        raise KeyError(key) from err
    KeyError: 'SampleID'

This was due to older SampleSheet.csv code not handling diff version of sheet that had "Sample_ID"

Example:

    File "/data/variantgrid/seqauto/illumina/samplesheet.py", line 163, in convert_sheet_to_df
    if index2 := get_first_col(["Index2", 'index2']):
    File "/usr/local/lib/python3.8/dist-packages/pandas/core/generic.py", line 1326, in __nonzero__
    raise ValueError(
    ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().
    
    seqauto.sequencing_files.create_resource_models.SeqAutoRunError: Error processing "Sequencing Run X"

**Fix**: Fix code. Deploy upgrade, restart services.

If developer is away:

* Raise an issue about it, so they can be informed, and verify fix when they return
* This is likely new code, so check git logs, can possibly revert change to previous version.
* If it's something non-critical you can just delete the lines about getting and storing the barcode. Or catch the exception and continue on.

That way we can continue to have services