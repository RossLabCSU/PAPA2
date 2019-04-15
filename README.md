# PAPA2
PAPA2 is the 2nd generation version of the **P**rion **A**ggregation **P**rediction **A**lgorithm (PAPA), originally reported by Toombs *et al.* (2010) in [https://www.ncbi.nlm.nih.gov/pubmed/19884345].

PAPA2 predicts aggregation propensity of protein sequences based on empirically-derived prion propensity scores, coupled with predictions of intrinsically unstructured protein regions using the FoldIndex algorithm. The major changes (compared to the original PAPA algorithm) include:

1) Within each protein, all overlapping windows scoring above the PAPA threshold (0.05) are now merged into a single PrLD. The main advantage to this approach is that predicted PrLD boundaries are explicitly defined, allowing for mapping of post-translational modifications, short linear motifs, or other sites of interest within the full PrLD window. This also allows for the identification of multiple distinct PrLDs within the same protein.

2) PAPA2 scores are now assigned to the last residue of the first window which, combined with #1 above, facilitates mapping and interpretation of sites of interest with site-specific PAPA2 scores across a protein sequence.

3) FoldIndex (which is used to estimate intrinsic disorder propensity) is now calculated 
