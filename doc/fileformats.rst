File formats
============

We've tried to use standard file formats where possible in CNVkit. However, in a
few cases we have needed to extend the standard BED format to accommodate
additional information.

All of the non-standard file formats used by CNVkit are tab-separated plain text
and can be loaded in a spreadsheet program, R or other statistical analysis
software for manual analysis, if desired.

BED
---

See: http://genome.ucsc.edu/FAQ/FAQformat.html#format1

Note that BED genomic coordinates are 0-indexed, like C or Python code, but
unlike the 1-indexed GATK/Picard interval list format. (The first nucleotide
of a 1000-basepair sequence has position 0, the last nucleotide has position 999,
and the entire region is indicated by the range 0-1000.)


Target and antitarget bin-level coverages (.cnn)
------------------------------------------------

Coverage of binned regions is saved in a tabular format similar to BED but with
additional columns. Each row in the file indicates an on-target or off-target
(antitarget, background) bin. Genomic coordinates are 0-indexed, like BED.

Column names are shown as the first line of the file:

* Chromosome or reference sequence name (``chromosome``)
* Start position (``start``)
* End position (``end``)
* Gene name (``gene``)
* Log2 mean coverage depth (``log2``)

Essentially the same tabular file format is used for coverages (.cnn), ratios
(.cnr) and segments (.cns) emitted by CNVkit.


Copy number reference profile (.cnn)
------------------------------------

In addition to the columns present in the "target" and "antitarget" .cnn files,
the reference .cnn file has the columns:

* GC content of the sequence region (``gc``)
* RepeatMasker-masked proportion of the sequence region (``rmask``)
* Statistical spread or dispersion (``spread``)

The log2 coverage depth is the weighted average of coverage depths, excluding
extreme outliers, observed at the corresponding bin in each the sample .cnn
files used to construct the reference. The spread is a similarly weighted
estimate of the standard deviation of normalized coverages in the bin.

To manually review potentially problematic genes in the built reference, you can
sort the file by the "spread" column; bins with higher values are the noisy
ones.

It is important to keep the copy number reference file consistent for the
duration of a project, reusing the same reference for bias correction of all
tumor samples in a cohort.
If your library preparation protocol changes, it's usually best to build a new
reference file and use the new file to analyze the samples prepared under the
new protocol.


Bin-level log2 ratios (.cnr)
----------------------------

In addition to the BED-like ``chromosome``, ``start``, ``end`` and ``gene``
columns present in .cnn files, the .cnr file has the columns:

* Log2 ratio (``log2``)
* Proportional weight to be used for segmentation (``weight``)

The weight value is the inverse of the variance (i.e. square of ``spread`` in
the reference) of normalized log2 coverage values seen among all normal samples
at that bin. This value is used to weight the bin log2 ratio values during
segmentation.

Also, when a genomic region is plotted with CNVkit's "scatter" command, the size
of the plotted datapoints is proportional to the weight of each point used in
segmentation -- a relatively small point indicates a less reliable bin.


Segmented log2 ratios (.cns)
----------------------------

In addition to the ``chromosome``, ``start``, ``end``, ``gene`` and ``log2``
columns present in .cnr files, the .cns file format has the additional column
``probes``, indicating the number of bins covered by the segment.

The ``gene`` column does not contain actual gene names. Rather, the sign of the
segment's copy ratio value is indicated by 'G' if greater than zero or 'L' if
less than zero.

