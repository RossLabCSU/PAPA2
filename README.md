# PAPA2
PAPA2 is the 2nd generation version of the **P**rion **A**ggregation **P**rediction **A**lgorithm (PAPA), originally reported by [Toombs *et al.* (2010)](https://www.ncbi.nlm.nih.gov/pubmed/19884345).

PAPA2 predicts aggregation propensity of protein sequences based on empirically-derived prion propensity scores, coupled with predictions of intrinsically unstructured protein regions using the FoldIndex algorithm.

## Usage
### Run PAPA2 with Default Parameters
PAPA2 is a Python3 script designed to be run as a command-line application. To run PAPA2 on your sequences of interest, download the PAPA2 script and save to a location containing a FASTA file with your protein sequences of interest. Navigate to that location via command line, and run PAPA2 with the following command (will use default parameters):

>python PAPA2.py -o your_results_file.tsv your_FASTA_sequences_file

NOTE: Make sure to include the file extension in the command above for your file containing FASTA-formatted sequences. FASTA files will often have the file extension ".fsa" or ".FASTA", but are sometimes also provided as plain-text files (.txt), which should still work with PAPA2. As specified above, PAPA2 is designed to output your results in a **t**ab-**s**eparated **v**alues (.tsv) file. This file type was chosen for two main reasons: 1) .tsv files can be easily parsed in downstream computational processing and avoids using comma-delimiters which are often present in FASTA headers, and 2) .tsv files can be opened by Microsoft Excel for the typical user. However, if Microsoft Excel is not set as the default program to open .tsv files, the file must be opened from *within* Excel (i.e. first open Excel, then open the results file from within Excel). Alternatively, you can first change your system settings to open .tsv files with Excel by default.

### Additional, Tunable Parameters
PAPA2 can also be run in "verbose" mode, which outputs the PAPA2 scores and FoldIndex scores for every position in each protein sequence analyzed. A typical command would be:

>python PAPA2.py --verbose -o your_results_file.tsv your_FASTA_sequences_file


By default, PAPA2 only assigns prion propensity scores to positions that are also predicted to be intrinsically disordered, which is calculated using the FoldIndex algorithm. Predicted ordered regions are assigned a value of "NaN" in verbose mode, and proteins that do not possess an ordered region are arbitrarily assigned a value of -1. However, if you wish to ignore FoldIndex when calculating prion propensities, a typical command-line run could be:

>python PAPA2.py --ignore_fold_index -o your_results_file.tsv your_FASTA_sequences_file


By default, PAPA2 uses a sliding window size of 41 amino acids. Therefore, for each position, the PAPA2 score is calculated as the average PAPA2 score across 41 consecutive 41-amino acid windows. Alternative window sizes can be specified at run-time. For example, to use a 50-amino acid window, the command would be:

>python PAPA2.py --window_size 50 -o your_results_file.tsv your_FASTA_sequences_file


Finally, a new feature in PAPA2 is the merging of overlapping high-scoring domains within a single protein. By default, only overlapping windows scoring above the recommended PAPA2=0.05 threshold are merged. However, this threshold can also be altered at run-time for more relaxed or stringent definitions of high-scoring domains. For example, to use a more stringent threshold of 0.08, a typical command would be:

>python PAPA2.py --threshold 0.08 -o your_results_file.tsv your_FASTA_sequences_file


Commands can also be combined, as in the following example:

>python PAPA2.py --verbose --ignore_fold_index --window_size 50 --threshold 0.08 -o your_results_file.tsv your_FASTA_sequences_file

## Major Updates
The major changes (compared to the original PAPA algorithm) include:

1) Within each protein, all overlapping windows scoring above the PAPA2 threshold (0.05) are now merged into a single PrLD. The main advantage to this approach is that predicted PrLD boundaries are explicitly defined, allowing for mapping of post-translational modifications, short linear motifs, or other sites of interest within the full PrLD window. This permits PrLDs to be of variable length, and allows for the identification of multiple distinct PrLDs within the same protein.

2) PAPA2 scores are now assigned to the last residue of the first window which, combined with #1 above, facilitates mapping and interpretation of sites of interest with site-specific PAPA2 scores across a protein sequence. Specifically, this results in PAPA2 scores being assigned to the central residue of each full window (which encapsulates a series of sliding windows), so positions with high PAPA2 scores can be more intuitively correlated with protein regions/sites of interest. This change also assigns PAPA2 scores to every position in the protein, which was not possible with the original PAPA algorithm.

3) FoldIndex (used to estimate intrinsic disorder propensity) calculations have been simplified. In the original PAPA algorithm, the FoldIndex score at each position is calculated by taking the average FoldIndex scores for a series of sliding windows (41 windows by default), then averaging the scores across the series of windows. In PAPA2, FoldIndex for each position is calculated simply as the FoldIndex across the full window encapsulating the series of sliding windows. This change has the dual advantage of increasing PAPA2 speed and improving handling of neighboring, highly charged regions (the FoldIndex algorithm includes a net charge term, so averaging across windows can lead to unexpected behavior for a small fraction of proteins with neighboring charged domains).

4) The output file format for a PAPA2 run has been improved, and includes additional information such as explicit PrLD boundaries and amino acid sequences of the defined PrLDs.

5) PAPA2 has been updated to be compatible with Python3 (the original PAPA algorithm is only compatible with Python2).

### Minor Bug Fixes
1) Assignment of the highest-scoring position in the original PAPA algorithm was offset by 1 amino acid relative to the PAPA scores. This has been fixed when score assignment position was changed in PAPA2 (see #1 in Major Updates above).
2) Stop codons were improperly handled in the original PAPA algorithm, which very slightly affected values for positions at the C-termini.
3) Definition of the window size used in the original PAPA algorithm was offset by 1 for a subset of possible user-specified window sizes. This was also fixed when score assignment position was changed in PAPA2 (see #1 in Major Updates above).
