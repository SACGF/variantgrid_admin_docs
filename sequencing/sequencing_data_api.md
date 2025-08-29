# Sequencing Data API

The URLs are made via [Django REST Framework](https://www.django-rest-framework.org/) - so work by serializing the VG Django Models

It's a JSON REST API (get/post JavaScript data structures to URLs) so can be called from any language, but we have a helper Python package vg_api available to simplify things (see below)

### URLs

You can access the URLs via web browser, command line tool, or programming language library (such as requests in Python)

Each end point will usually allow you to list all record (this may be very slow), view, update and create individual records. For instance (You need to be on SA Path intranet with a valid login to access these)

[View a the TSO EnrichmentKit](http://frgeneseq02:92/seqauto/api/v1/enrichment_kit/50/)
[View Haem SequencingRun](http://frgeneseq02:92/seqauto/api/v1/sequencing_run/Haem_25_078_250822_A01934_0285_BHCFFVDRX7/)

You can browse the web forms to see how the data will look, and experiment sending up data, though I recommend you use the test server for this.

### API Token

To call the API, you need a user (created via Django Admin) and a [DRF](https://www.django-rest-framework.org/) API token, which is created on the server via admin or command line:

    python3 manage.py drf_create_token username

### variantgrid_api pip package

We've created a Python package to simplify API calls, to install:

    python3 -m pip install variantgrid_api

See [variantgrid_api GitHub project](https://github.com/SACGF/variantgrid_api) for more information