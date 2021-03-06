# modified PAPA (mPAPA)
mPAPA is a modified version of the **P**rion **A**ggregation **P**rediction **A**lgorithm (PAPA), originally reported by [Toombs *et al.* (2010)](https://www.ncbi.nlm.nih.gov/pubmed/19884345).

mPAPA predicts aggregation propensity of protein sequences based on empirically-derived prion propensity scores, coupled with predictions of intrinsically unstructured protein regions using the FoldIndex algorithm. If you use mPAPA, please cite [Cascarina and Ross (2020) *BMC Genomics*](https://pubmed.ncbi.nlm.nih.gov/31914925-natural-and-pathogenic-protein-sequence-variation-affecting-prion-like-domains-within-and-across-human-proteomes/?from_term=sean+cascarina&from_sort=date&from_pos=1 "Link to Research Article") and [Toombs *et al.* (2010)](https://www.ncbi.nlm.nih.gov/pubmed/19884345).

## Usage
### Run mPAPA with Default Parameters
mPAPA is a Python3 script designed to be run as a command-line application. To run mPAPA on your sequences of interest, download the mPAPA script and save to a location containing a FASTA file with your protein sequences of interest. Navigate to that location via command line, and run mPAPA with the following command (will use default parameters):

> \>python mPAPA.py -o your_results_file.tsv your_FASTA_sequences_file

NOTE: Make sure to include the file extension in the command above for your file containing FASTA-formatted sequences. FASTA files will often have the file extension ".fsa" or ".FASTA", but are sometimes also provided as plain-text files (.txt), which should still work with mPAPA. As specified above, mPAPA is designed to output your results in a **t**ab-**s**eparated **v**alues (.tsv) file. This file type was chosen for two main reasons: 1) .tsv files can be easily parsed in downstream computational processing and avoids using comma-delimiters which are often present in FASTA headers, and 2) .tsv files can be opened by Microsoft Excel for the typical user. However, if Microsoft Excel is not set as the default program to open .tsv files, the file must be opened from *within* Excel (i.e. first open Excel, then open the results file from within Excel). Alternatively, you can first change your system settings to open .tsv files with Excel by default.

### Additional Tunable Parameters
mPAPA can also be run in "verbose" mode, which outputs the mPAPA scores and FoldIndex scores for every position in each protein sequence analyzed. A typical command would be:

> \>python mPAPA.py --verbose -o your_results_file.tsv your_FASTA_sequences_file


By default, mPAPA only assigns prion propensity scores to positions that are also predicted to be intrinsically disordered, which is calculated using the FoldIndex algorithm. Predicted ordered regions are assigned a value of "NaN" in verbose mode, and proteins that do not possess an ordered region are arbitrarily assigned a value of -1. However, if you wish to ignore FoldIndex when calculating prion propensities, a typical command-line run could be:

> \>python mPAPA.py --ignore_fold_index -o your_results_file.tsv your_FASTA_sequences_file


By default, mPAPA uses a sliding window size of 41 amino acids. Therefore, for each position, the mPAPA score is calculated as the average mPAPA score across 41 consecutive 41-amino acid windows. Alternative window sizes can be specified at run-time. For example, to use a 50-amino acid window, the command would be:

> \>python mPAPA.py --window_size 50 -o your_results_file.tsv your_FASTA_sequences_file


Finally, a new feature in mPAPA is the merging of overlapping high-scoring domains within a single protein. By default, only overlapping windows scoring above the recommended mPAPA=0.05 threshold are merged. However, this threshold can also be altered at run-time for more relaxed or stringent definitions of high-scoring domains. For example, to use a more stringent threshold of 0.08, a typical command would be:

> \>python mPAPA.py --threshold 0.08 -o your_results_file.tsv your_FASTA_sequences_file


Commands can also be combined, as in the following example:

> \>python mPAPA.py --verbose --ignore_fold_index --window_size 50 --threshold 0.08 -o your_results_file.tsv your_FASTA_sequences_file

## Major Modifications
The major changes incorporated into mPAPA include:

1) Within each protein, all overlapping windows scoring above the mPAPA threshold (0.05) are now merged into a single PrLD. The main advantage to this approach is that predicted PrLD boundaries are explicitly defined, allowing for mapping of post-translational modifications, short linear motifs, or other sites of interest within the full PrLD window. This permits PrLDs to be of variable length, and allows for the identification of multiple distinct PrLDs within the same protein.

2) mPAPA scores are now assigned to the last residue of the first window which, combined with #1 above, facilitates mapping and interpretation of sites of interest with site-specific mPAPA scores across a protein sequence. Specifically, this results in mPAPA scores being assigned to the central residue of each full window (which encapsulates a series of sliding windows), so positions with high mPAPA scores can be more intuitively correlated with protein regions/sites of interest. This change also assigns mPAPA scores to every position in the protein, which was not possible with the original PAPA algorithm.

3) FoldIndex (used to estimate intrinsic disorder propensity) calculations have been simplified. In the original PAPA algorithm, the FoldIndex score at each position is calculated by taking the average FoldIndex scores for a series of sliding windows (41 windows by default), then averaging the scores across the series of windows. In mPAPA, FoldIndex for each position is calculated simply as the FoldIndex across the full window encapsulating the series of sliding windows. This change has the dual advantage of increasing mPAPA speed and improving handling of neighboring, highly charged regions (the FoldIndex algorithm includes a net charge term, so averaging across windows can lead to unexpected behavior for a small fraction of proteins with neighboring charged domains).

4) The output file format for a mPAPA run has been improved, and includes additional information such as explicit PrLD boundaries and amino acid sequences of the defined PrLDs.

5) mPAPA has been modified to be compatible with Python3 (the original PAPA algorithm is only compatible with Python2).

## Minor Bug Fixes
1) Assignment of the highest-scoring position in the original PAPA algorithm was offset by 1 amino acid relative to the PAPA scores. This has been fixed when score assignment position was changed in mPAPA (see #1 in Major Updates above).
2) Stop codons were improperly handled in the original PAPA algorithm, which very slightly affected values for positions at the C-termini.
3) Definition of the window size used in the original PAPA algorithm was offset by 1 for a subset of possible user-specified window sizes. This was also fixed when score assignment position was changed in mPAPA (see #1 in Major Updates above).

#### License info
mPAPA is subject to the terms of the GPLv3 license according to license terms of the original PAPA algorithm.

