# Classification Admin Pages

#### Activity

This page shows changes that have been performed on a classification (or classifications) evidence and flags.
The oldest changes are shown at the top and the newest at the bottom.

#### HGVS Issues

This page shows Alleles with open flags that require human action/validation.

A typical flag is "Mismatched c.HGVS" (37/38) - when you liftover between genome builds at the coordinate level, sometimes the c.HGVS coordinate can change (due to mRNA transcripts aligning differently to the reference sequences, causing eg exon boundary changes). We flag this for a human to double check everything is ok.

### Django Admin tasks