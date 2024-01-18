# Targeted Evaluation and Assessment of Quality (TEAQ)
A software package developed for performing targeted extraction of quality peptides or precursors to enhance the validation of biomarker discovery from large-scale proteomics datasets. It facilitates the selection of proteotypic peptides and precursors that meet rigorous standards for reliability, reproducibility, linearity and precision from DIA datasets.

## Table of Contents
1. [Introduction](#introduction)
2. [TEAQ Workflow](#teaq-workflow)
3. [Installation](#installation)
4. [Usage](#Usage)
5. [Input File Description](#input-file-description)
6. [Input Parameters](#input-parameters)
7. [Output Excel Reports](#outputs-and-explanations)
8. [Cite](#Cite)
9. [Contributions](#contributions)
10. [Release Notes](#release-notes)

### TEAQ Workflow

Manasa: Insert Workflow Image here

TEAQ can be used to identify and extract the following set of peptides and precursors from your dataset:

#### Quantifiable Peptides or Precursors:

These peptides or precursors are extracted from loading curve datasets, where they meet observation, coefficient of variation and minimum point linearity requirements provided by the user. The following steps are followed to extract quantifiable peptides or precursors:

1) Loading Curve Dataset, Sample Mapping File and provided parameters are validated and error handling is performed. (Refer to [Input File Description](#input-file-description) and [Input Parameters](#input-parameters) for more details about input requirements)
2) Isoform RollUp and Drop Shared Protein: Protein isoform intensities are rolled up and a representative canonical protein is assigned, plus any shared proteins are dropped.
3) LLOD Calculation: Intensity value observation across samples in each concentration group is calculated along with CV%. The lowest concentration that meets the provided observation and CV% thresholds is selected as the lower limit of detection (LLOD) for each peptide or precursor in the dataset.
4) LLOQ Calculation: The lowest concentration where the peptide or precursor meets LLOD requirements, a provided minimum point linearity, r-squared and target deviation thresholds is assigned as the lower limit of quantification (LLOQ). Peptides or precursors which can be quantified and have a assigned LLOQ value are selected as Quantifiable Peptides or Precursors
5) Output Excel Reports containing 'Quantifiable Peptides or Precursors' along with calculated numbers are generated. (Refer to [Output Excel Reports](#outputs-and-explanations) for more details)

#### Signature Peptides or Precursors:

These peptides or precursors are extracted from cohort datasets, where they meet stringent thresholds such as intensity observation percentage, minimum allowed miscleavages, unique to provided fasta database and correlation. The user can also provide a peptide or precursor list (quantifiable peptides or precursors if extracted using TEAQ) to further filter the data. The following steps are followed to extract signature  peptides or precursors:

1) Cohort Dataset, List of Peptides or Precursors, and provided parameters are validated and error handling is performed.  (Refer to [Input File Description](#input-file-description) and [Input Parameters](#input-parameters) for more details about input requirements)
2) Intensity value observation is calculated across the dataset. Any peptides or precursors not meeting the provided minimum observation threshold are filtered out.
2) If user-defined list of peptides or precursors (we recommend using quantifiable peptides or precursors extracted from a loading curve dataset using TEAQ) is provided, the dataset is filtered for these set of peptides or precursors
3) Number of miscleavages is calculated for each peptide in the dataset based on the digestion enzyme provided. Any peptides not meeting the minimum allowed miscleavages or with incorrect cleave sites are filtered out.
4) Using the provided input fasta file, peptides with sequences unique to only one protein are kept and the rest are filtered out.
5) If precursor-level data is provided, any precursors with charge 1 are filtered out if there exists the same precursor with a higher charge. (Note: This filteration is only performed when precursor-level data is provided. When only peptide-level input is given, this filter is not enabled)
6) Using the filtered list of peptides or precursors, correlation is performed and correlated peptides or precursors meeting the correlation coefficient threshold are identified as signature peptides or precursors.
7) Output Excel Reports containing 'Signature Peptides or Precursors' along with calculated numbers are generated. (Refer to [Output Excel Reports](#outputs-and-explanations) for more details)

## Installation

TEAQ can be downloaded from this Github page directly or through command-line using `git`. 

To download it directly, click on the blue code button at the top right and click on "Download ZIP". 

To download it via command-line, clone the repository from GitHub:

`git clone https://github.com/csmc-vaneykjlab/TEAQ.git`

## Usage
```
TEAQ.exe [-h] --outdirectory OUTDIRECTORY --reportname REPORTNAME --level LEVEL
[--loading_curve LOADING_CURVE] [--samplemapping SAMPLEMAPPING]
[--cohortdataset COHORTDATASET] [--peptide_list PEPTIDE_LIST]
[--precursor_list PRECURSOR_LIST] [--fasta_file FASTA_FILE]
[--observation_threshold_per_conc OBSERVATION_THRESHOLD_PER_CONC] [--cv_perc_threshold CV_PERC_THRESHOLD]
[--min_conc_for_linearresponse MIN_CONC_FOR_LINEARRESPONSE] [--r_squared_threshold R_SQUARED_THRESHOLD]
[--target_deviation_threshold TARGET_DEVIATION_THRESHOLD]
[--observation_threshold_across_dataset OBSERVATION_THRESHOLD_ACROSS_DATASET]
[--correlation_coefficient_threshold CORRELATION_COEFFICIENT_THRESHOLD]
[--num_allowed_miscleavages NUM_ALLOWED_MISCLEAVAGES] [--enzyme ENZYME]
```

### Running TEAQ via Command-Line

After downloading the executable, open command prompt on your Windows system: 
- Press Windows Key + R to open the Run dialog.
- Type cmd and press Enter.
- Use the cd command to change the directory to where the downloaded TEAQ executable is located. 
  `cd path/to/directory`
- Run the executable and provide input files and arguments (refer to the example commands below)

##### For Identifying Quantifiable Peptides/Precursors:  

```
TEAQ.exe --outdirectory OUTDIRECTORY --reportname REPORTNAME --level LEVEL
[--loading_curve LOADING_CURVE] [--samplemapping SAMPLEMAPPING]
[--observation_threshold_per_conc OBSERVATION_THRESHOLD_PER_CONC] [--cv_perc_threshold CV_PERC_THRESHOLD]
[--min_conc_for_linearresponse MIN_CONC_FOR_LINEARRESPONSE] [--r_squared_threshold R_SQUARED_THRESHOLD]
[--target_deviation_threshold TARGET_DEVIATION_THRESHOLD]
```

Example Command: 

```
TEAQ.exe --outdirectory ./outputs --reportname ExampleTest1 --level Precursor --loading_curve example_inputs/loadingcurve_dataset.txt --samplemapping example_inputs/samplemapping_file.txt --observation_threshold_per_conc 0.6 --cv_perc_threshold 20 --min_conc_for_linearresponse 3 --r_squared_threshold 0.8 --target_deviation_threshold 0.2
```

##### For Identifying Signature Peptides/Precursors

```
TEAQ.exe [-h] --outdirectory OUTDIRECTORY --reportname REPORTNAME --level LEVEL
[--cohortdataset COHORTDATASET] [--precursor_list PRECURSOR_LIST]
[--fasta_file FASTA_FILE] [--observation_threshold_across_dataset OBSERVATION_THRESHOLD_ACROSS_DATASET]
[--correlation_coefficient_threshold CORRELATION_COEFFICIENT_THRESHOLD]
[--num_allowed_miscleavages NUM_ALLOWED_MISCLEAVAGES] [--enzyme ENZYME]
```

Example Command: 

```
TEAQ.exe --outdirectory ./outputs --reportname ExampleTest1 --level Precursor --cohortdataset example_inputs/cohortdataset.txt --precursor_list example_inputs/quantifiable_precursors_list.txt --fasta_file example_inputs/202106_human_iRT_canonical_isoform_UP000005640_reviewed.fasta --observation_threshold_across_dataset 0.2 --correlation_coefficient_threshold 0.7 --num_allowed_miscleavages 0 --enzyme Trypsin
```

## Input File Description

### Inputs for Quantifiable Peptides or Precursors

#### Loading Curve Dataset

Peptide or Precursor-level Intensity Search result in .txt wide format with columns as samples.

Peptide-Level Loading Curve Dataset:

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names      |
| Peptide           | A column containing Peptide Ids      |
| < list of sample names >             | a list of sample names as headers followed by their intensity values     |

See Example:

Precursor-Level Loading Curve Dataset:

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names      |
| Peptide           | A column containing Peptide Ids      |
| Precursor           | A column containing Precursor Ids      |
| < list of sample names >             | a list of sample names as headers followed by their intensity values     |

See Example:

#### Sample Mapping File

A file containing concentration labels for all samples present in the provided loading curve dataset in .txt wide format.

| Column                | Description |
|--------------------------|------------|
| Sample Name           | A column containing sample names      |
| Label            | Concentration value the sample belongs to (only numbers allowed) |

See Example:

### Inputs for Signature Peptides or Precursors

#### Cohort Dataset

Peptide or Precursor-level Intensity Search result in .txt wide format with columns as samples.

Peptide-Level Loading Curve Dataset:

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names      |
| Peptide           | A column containing Peptide Ids      |
| < list of sample names >             | a list of sample names as headers followed by their intensity values     |

See Example:

Precursor-Level Loading Curve Dataset:

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names      |
| Peptide           | A column containing Peptide Ids      |
| Precursor           | A column containing Precursor Ids      |
| < list of sample names >             | a list of sample names as headers followed by their intensity values     |

See Example:

#### Peptide or Precursor List

List of peptides or precursors to select within the cohort dataset. We recommend using quantifiable peptides or precursors identified from a loading curve dataset (if available) using TEAQ.

Peptide List:

| Column                | Description |
|--------------------------|------------|
| Peptide           | A column containing Peptide Ids      |

See Example:

Precursor List:

| Column                | Description |
|--------------------------|------------|
| Precursor           | A column containing Precursor Ids      |

See Example:

## Input parameters

| Parameter                | Short Form | Description                                      | Default Value |
|--------------------------|------------|--------------------------------------------------|---------------|
| --outdirectory           | -o         | (Required) Output directory path                            | None          |
| --reportname             | -r         | (Required) Report name for Excel reports           | None          |
| --loading_curve        | -l         | Path to Loading Curve Peptide/Precursor Intensity Tab Separated File | False          |
| --samplemapping      | -s        | Path to Loading Curve Sample to Concentration Mapping File                                | False         |
| --cohortdataset      | -c        | Path to Cohort Dataset Peptide/Precursor Intensity Tab Separated File                                | False         |
| --peptide_list  | -peplt        | Path to file containing list of peptides to filter from cohort dataset                            | False         |
| --precursor_list  | -prelt        | Path to file containing list of precursors to filter from cohort dataset                            | False         |
| --fasta_file | -f        | Path to FASTA file             | False         |
| --level          | -p        | Provided Loading Curve or Cohort Dataset Intensity Values Level: Peptide or Precursor                   | None          |
| --observation_threshold_per_conc          | -obs       | Observation Threshold for Loading Curve Intensity Values within each Concentration               | 0.6          |
| --cv_perc_threshold        | -cv      | CV% Threshold for Loading Curve Intensity Values within each Concentration                 | 20          |
| --min_conc_for_linearresponse          | -n         | Minimum number of Concentration data points that need to be linear in the Loading Curve               | 3          |
| --r_squared_threshold           | -r2     | R-Squared Threshold for Linear Response | 0.8          |
| --target_deviation_threshold      | -t         | Target Deviation Threshold for Linear Response                | 0.2         |
| --observation_threshold_across_dataset      | -ob         | Observation Threshold for Cohort Dataset Intensity Values across the dataset                | 0.2         |
| --correlation_coefficient_threshold    | -corr         | Correlation Coefficient Threshold for Signature Peptide/Precursor Correlation              | 0.7         |
| --num_allowed_miscleavages                 | -m         | Number of Allowed Miscleavages in Signature Peptides/Precursors                                | 0          |
| --enzyme  | -c         | User input enzyme        | False         |

## Output Excel Reports

### Quantifiable Peptides or Precursors Report

An excel report with the following sheets is generated using TEAQ:

1) Quantifiable Peptides/Precursors:

This dataset is filtered to only contain identified quantifiable peptides or precursors.

| Column                | Description |
|--------------------------|------------|
| RolledUp_Protein           | A column containing canonical Protein Ids/Names (from Isoform Roll Up Step)     |
| Peptide           | A column containing Peptide Ids      |
| Precursor         | A column containing Precursor Ids     |
| < list of sample names >             | a list of sample names as headers followed by their rolled up intensity values     |

2) LoadingCurveDataset:

This dataset contains the input loading curve dataset along with rolled up protein names, shared protein status, LLOD and LLOQ calculations.

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names     |
| RolledUp_Protein  | A column containing canonical Protein Ids/Names (from Isoform Roll Up Step)     |
| Shared Status     | A column which labels the shared proteins which have been filtered out as 'Dropped' |
| Peptide           | A column containing Peptide Ids      |
| Precursor         | A column containing Precursor Ids     |
| LLOD              | A column containing Lower Limit of Detection Concentration Label |
| LLOD              | A column containing Lower Limit of Quantifiable Concentration Label |
| < list of sample names >             | a list of sample names as headers followed by their input intensity values     |

3) LoadingCurve Numbers:

A table containing protein, peptide and precursor numbers from input loading curve dataset.

| Column                | Description |
|--------------------------|------------|
| index           | A column containing Total Proteins, Total Peptides, Total Precursors, Total Avg Proteins, Total Avg Peptides, and Total Avg Precursors labels     |
| Total           | A column containing numbers from across the loading curve dataset      |
| < list of concentration labels >             | a list of concentration labels as headers followed by the corresponding numbers     |

4) RolledUp_DropShared Numbers:

A table containing protein, peptide and precursor numbers from the rolled up intensity loading curve dataset with shared proteins filtered out.   

| Column                | Description |
|--------------------------|------------|
| index           | A column containing Total Proteins, Total Peptides, Total Precursors, Total Avg Proteins, Total Avg Peptides, and Total Avg Precursors labels     |
| Total           | A column containing numbers from across the rolled up and filtered dataset      |
| < list of concentration labels >             | a list of concentration labels as headers followed by the corresponding numbers     |

5) LLOD Numbers:

A table containing protein, peptide and precursor numbers from loading curve dataset filtered to have peptides or precursors with an identified lower limit of detection.

| Column                | Description |
|--------------------------|------------|
| index           | A column containing Total Proteins, Total Peptides, Total Precursors, Total Avg Proteins, Total Avg Peptides, and Total Avg Precursors labels     |
| Total           | A column containing numbers from across the LLOD filtered dataset      |
| < list of concentration labels >             | a list of concentration labels as headers followed by the corresponding numbers     |

5) LLOQ Numbers:

A table containing protein, peptide and precursor numbers from loading curve dataset filtered to have peptides or precursors with an identified lower limit of quantification.

| Column                | Description |
|--------------------------|------------|
| index           | A column containing Total Proteins, Total Peptides, Total Precursors, Total Avg Proteins, Total Avg Peptides, and Total Avg Precursors labels     |
| Total           | A column containing numbers from across the LLOQ filtered dataset      |
| < list of concentration labels >             | a list of concentration labels as headers followed by the corresponding numbers     |

See Example:

### Signature Peptides or Precursors Report

An excel report with the following sheets is generated using TEAQ:

1) Signature Peptides/Precursors:

This dataset is filtered to only contain identified signature peptides or precursors.

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names    |
| Peptide           | A column containing Peptide Ids      |
| Precursor         | A column containing Precursor Ids     |
| < list of sample names >             | a list of sample names as headers followed by their intensity values     |

2) CohortDataset:

This dataset contains the input cohort dataset along with columns that show filter criteria such as observation percentage values, number of miscleavages, unique to fasta columns.

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names     |
| Peptide           | A column containing Peptide Ids      |
| Precursor         | A column containing Precursor Ids     |
| Observation Across Dataset              | A column containing observation percentage value across the all the samples |
| Miscleavages              | A column containing number of identified miscleavages in the peptide |
| Unique to Fasta Status    | A column containing unique or not unique label |
| Filter Status | A column containing 'Present in List' label if the peptide/precursor is present in provided input list |
| Signature Precursor Status | A column containing 'Signature Peptide/Precursor' label if the peptide/precursor is identified as signature |
| < list of sample names >             | a list of sample names as headers followed by their input intensity values     |

3) SignatureCorrelation:

A table containing identified signature peptides or precursors along with the percentage of number of peptide or precursors with correlation coefficents above the provided threshold across all identified peptides or precursors in corresponding protein id.

| Column                | Description |
|--------------------------|------------|
| Protein           | A column containing Protein Ids/Names    |
| Precursor         | A column containing Precursor Ids     |
| %Correlation>threshold          | A column containing  the percentage of number of peptide or precursors with correlation coefficents above the provided threshold across all identified peptides or precursors in corresponding protein id     |

4) Cohort Numbers:

| Column                | Description |
|--------------------------|------------|
| index           | A column containing Protein, Peptide, Precursors labels     |
| Total           | A column containing numbers from across the input dataset      |
| Observation = threshold Across Dataset           | A column containing numbers from across the observation threshold filtered dataset      |
| Allowed Miscleavages = 0           | A column containing numbers from across the allowed miscleavages filtered dataset      |
| Unique to Fasta           | A column containing numbers from across the unique to fasta filtered dataset      |
| Peptide/Precursor List Filter | A column containing numbers from across the provided list filtered dataset    |
| Signature Peptides/Precursors | A column containing numbers from across the signature peptides/precursors filtered dataset    |

See Example:

## Cite

## Support

## release notes
