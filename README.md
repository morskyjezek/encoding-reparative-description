# Encoding Reparative Description

This repository began with code related to the "Encoding Reparative Description" project, which began in 2023 with a team of researchers at the [University of Michigan School of Information](https://www.si.umich.edu/), the [Bentley Historical Library](https://bentley.umich.edu/), and the University of Michigan's [Humanities Collaboratory](https://sites.lsa.umich.edu/collaboratory/). Over time, it was updated to work directly with the ArchivesSpace API rather than EADs to create a Key Word in Contect (KWIC) report and associated visulalizations to support management of repartive descrition efforts at scale.

As of November 2025, we are cleaning up our repository and rewriting the code. Current functionality includes: 

1. With a Python notebook, use an optional list of relevant ArchivesSpace resources to parse finding aid "front matter" to create a "parsed results" dataset.
2. With a Python notebook, look in parsed results for terms--provided separately--to create a "matched results" dataset.
3. With R, create a KWIC report.

## Usage

### 1. Parse Results

Run the "parse_resources.ipynb" Python notebook first. It takes an optional input of Resource IDs, described below, along with a configuration file described in the notebook, and uses them to parse ArchivesSpace via the API. It produces parsed results (a CSV file) used in later operations, and puts them in a "data" folder--see "parsed_results_*_nativeAmerican.csv" and "parsed_results_*_phillipines.csv" as examples.

**Filename:** parse_resource.ipynb
**Input:** Optionally, you may also provide a text file with relevant ArchivesSpace Resouce IDs, one per line, e.g.:

```
229
8482
4460
256
8480
...
```

If the user does not provide a text file with a list of Resource IDs, the tool will parse ALL ArchivesSpace resources. Since this is so resource intensive, the tool defaults to expecting a text file.

**Output:** A "parsed_results" CSV file in the "data" folder used in "match_terms.ipynb."

### 2. Match Terms

Run the "match_terms.ipynb" Python notebook next. It takes a "parsed_results" CSV file and a list of terms to look for matches in ArchivesSpace data. It produces matched results (a CSV file) used in later operations, and puts them in the same "data" folder--see "matched_results_*_nativeAmerican.csv" and "matched_results_*_phillipines.csv" as examples.

**Filename:** parse_resource.ipynb
**Input:** This script takes as an input the output of "parse_results.ipynb" above. The required term list must be a `.txt` file with one term per line, e.g.:

```
Civilization
Civilized
Uncivilized
Burial
Burials
...
```

See "terms-nativeAmerican.txt," "terms-phillipines.txt," and "terms-MENA.txt" as examples.

**Output:** A "matched_results" CSV file, also in the "data" folder, used to create a KWIC report.

### 3. KWIC Report

Forthcoming...
