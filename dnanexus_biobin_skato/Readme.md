# DNAnexus app source to run BioBin

Dnanexus (https://www.dnanexus.com/) is a cloud computing platform. This code is a wrapper around the BioBin app so that it can be run on the DNAnexus cloud platform.

Refer the tutorial here https://wiki.dnanexus.com/Developer-Tutorials/Intro-to-Building-Apps to build this source code as applet/app on DNAnexus.

# BioBin

## What does this app do?
This app runs the BioBin software, which is a comprehensive bioinformatics tool for biologically-driven binning and association analysis of rare variants (RVs) in sequence data. It automates multi-level binning or collapsing of RVs into biological features such as genes, pathways, regulatory regions, evolutionary conserved regions (ECRs), and many others. BioBin improves on other variant binning algorithms through the use of prior biological information, which can highlight the potential cumulative effects of biologically aggregated RVs. BioBin requires the Library of Knowledge Integration (LOKI), which contains diverse biological knowledge from multiple collections of publicly available databases. 

## Where can I find the BioBin software manual?
You can find the detailed BioBin software manual here: http://www.ritchielab.com/software/biobin-download

## What data is required for this app to run?
This app requires a VCF file to run. For more information on which input files to use and on the format of input files, please refer to the software manual above. Depending on the type of analysis that the user would like to run (e.g. limiting the analysis to a list of desired genes or to just LOF variants etc.), there are additional
input files that need to be included:
  - Phenotype file (`*.phe`)
  - Covariate file (`*.cov`)
  - Role file (`*.rol`)
  - Weight file (`*.wgt`)
  - Custom regions file (`*.rgn`) - referred to as "Region file" in the BioBin software manual
  - Region names list (`*.gen`) - list of gene symbols (1 per line) to which the user would like to limit the analysis. This input file is specific to the app; it is not available in the BioBin command-line application
  - BioBin Binary Executable - used only if the user would like to use older versions of the app; should be ignored in most cases
  - BioBin Summary Script - used only if the user would like to use older versions of the script that generates the summary output file; should be ignored in most cases

## Usage of the app
Once you upload all of the input files, go to the settings menu, where all available options are listed. Select the "LOKI Knowledge Database" (the most recent is recommended). If you’d like to do binning followed by an association test, select one of the desired statistical tests: linear regression, logistic regression, wilcoxon test, SKAT-linear (SKAT for continuous phenotypes), or SKAT-logistic (SKAT for continuous phenotypes), or SKAT-logistic (SKAT for categorical phenotypes). In the window with "BioBin arguments", you should include options that you would like to use (specific to the type of analysis that you would like to run). All available options are listed in the manual.

## Examples of analysis type-specific arguments 
Note: these are just examples; these options are not necessarily complete and should be modified according to user’s preferences:
  - Gene binning analysis:
  ```
  --weight-loci Y --bin-pathways N --bin-regions Y  --bin-minimum-size 5 -F 0.01 -G 37
  ```
  - Pathway binning analysis:
  ```
  --weight-loci Y --bin-pathways Y --bin-regions N --bin-interregion N --bin-minimum-size 5 -F 0.01 -G 37
  ```
  - Gene binning analysis limited to only the putative functional variants using a role file:
  ```
  --weight-loci Y --bin-pathways N --bin-regions Y --bin-minimum-size 5 -F 0.01 -G 37 --bin-expand-roles Y --bin-expand-size 1
  ```

If the user would like to perform a permutation analysis to empirically assess the significance of the obtained results, the app (unlike the BioBin command-line application) can also be used to run a permutation analysis, in which the sample IDs in the phenotype file are being permuted. For that, the user needs to specify, in the "Permutation Count" window, how many permutations they would like to perform, and the app will re-run the BioBin analysis for each permuted phenotype
file, keeping track of the p-values obtained from each permuted test. In addition to the regular output files described below, permutation analysis also outputs a `*permute-summary.tsv` file, which contains the empirical p-value (obtained based on individual p-values from the permuted tests) for each phenotype-bin combination tested (see below for details).

## What does this app output?
For a detailed description of the output files listed below, please see the software manual.
  - Bins report file (`*bins.csv`; one per phenotype)
  - Summary file (`*summary.tsv`) - specific to the BioBin app; not available in the BioBin command-line application. This file contains data from the bins reports concatenated across all phenotypes.
  - Locus file (`*locus.csv`)
  - List of unlifted variants (`*unlifted.csv`; outputted only if the VCF file used as an input is based on a different genomic build than LOKI, which is currently based on build hg38).
  - Permutation summary file (`*permute-summary.tsv`); output only when the permutation option is enabled (i.e. when the "Permutation Count" is set to > 0 in the app settings). This output file is specific to the app, it is not available in the BioBin command-line application. This file consists of 3 columns: outcome (i.e. phenotype name), bin (e.g. gene A), empirical p-value (which can be interpreted as the number of times out of X permutation runs that the permutation analysis returned a p-value that is lower or equal to the observed p-value, where the observed p-value is the p-value from the original BioBin run).

## Statistical tests
The app has options to run one of the following statistical tests:
  - linear
  - logistic
  - wilcoxon
  - SKAT-linear
  - SKAT-logistic
  - SKATO-linear 
  - SKATO-logistic
  
Weighing options that can be selected:
  - madsen-browning
  - linear(1,25)
  - no-weight

## How to run SKAT-O

In this analysis we used 'SKATO-logistic' and weight 'madsen-browning'. Both options can be selected in the app UI. Madsen-browning weighing is an inverse allele frequency weighting calculated as (2n+2) / sqrt(n(m+1)(2n−m+1)) where n is the total (non-missing) population, and m is the number of variants at a given locus.
