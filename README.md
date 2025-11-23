# Encoding Reparative Description

This repository began with code related to the "Encoding Reparative Description" project, which began in 2023 with a team of researchers at the [University of Michigan School of Information](https://www.si.umich.edu/), the [Bentley Historical Library](https://bentley.umich.edu/), and the University of Michigan's [Humanities Collaboratory](https://sites.lsa.umich.edu/collaboratory/). Over time, it was updated to work directly with the ArchivesSpace API rather than EADs to create a Key Word in Contect (KWIC) report and associated visulalizations to support management of repartive descrition efforts at scale.

As of November 2025, we are cleaning up our repository and rewriting the code. Current functionality includes: 

- Using a an optional list of relevant ArchivesSpace resources to parse finding aid "front matter" to create a dataset for the KWIC report.

Additional TODOs should be added as a repo Issue.

## Usage

### Inputs

There are two basic inputs for this tool, a **required** text file with a list of terms (in the "term-list" folder, using "terms-" prefix) and an **optional** ArchivesSpace Resource ID list (in the "resource-ids" folder, using a "resource_ids-" prefix.)

#### Term Lists

The required term list be a `.txt` file with one term per line, e.g.:

```
Civilization
Civilized
Uncivilized
Burial
Burials
...
```

See "terms-nativeAmerican.txt," "terms-phillipines.txt," and "terms-MENA.txt" as examples.

#### List of Resource IDs

Optionally, you may also provide a text file with relevant ArchivesSpace Resouce IDs, one per line, e.g.:

```
229
8482
4460
256
8480
...
```

If the user does not provide a text file with a list of Resource IDs, the tool will parse ALL ArchivesSpace resources. Since this is so resource intensive, the tool defaults to expecting a text file.

### Parse Resources

Run the `parse_resources.ipynb` Python notebook first. It takes the inputs above, along with a configuration file described in the notebook, and uses them to parse ArchivesSpace via the API. It produces data (a CSV file) used in later operations, and puts them in a "data" folder--see `results-nativeAmerican.csv` and `results.phillipines.csv` as examples.

### Create a Terms in Context Report or Visualizations (or both)

#### Key Words in Context Report

Forthcoming...

#### Visualizations

Forthcoming, although past visualizations are retained for reference purposes in the "visualizations" folder.
