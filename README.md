# GeoFetchr

[![PyPI - Version](https://img.shields.io/pypi/v/geofetchr.svg)](https://pypi.org/project/geofetchr)
[![PyPI - Python Version](https://img.shields.io/pypi/pyversions/geofetchr.svg)](https://pypi.org/project/geofetchr)

---

GeoFetchr is a Python package that helps you easily search, filter, fetch metadata, and download datasets from the NCBI Gene Expression Omnibus (GEO) database. It provides functions to quickly explore GEO datasets by **keywords**, **organism**, **assay type**, **accession IDs**, and to **download GEO Family files**.

# Why use GeoFetchr

- This package can search GEO data for datasets based on provided keywords and display the top organisms associated with those keywords and the counts of GDS, GPL, and GSE results.
- It can group the search results by top organisms and display the number of datasets for each category (GDS, GPL, GSE) within the selected organisms.
- It can provide details for each dataset, including the dataset link, PubMed links (if available), and the assay type.
- It can filter the grouped results by assay type and list the datasets matching the selected assay type.
- It supports multi-keyword searching and can display results for multiple organisms and assay types.
- It can provide detail metadata for a given GEO ID (GSE, GDS, or GPL), including title, accession, status, submission date, summary, overall design, type, contributors, sample IDs, 
  contact information, supplementary files, platform details, and related projects (BioProject, SRA).
- It can download GEO Family files for a given GEO ID, including series matrix, family XML, family soft, and raw data files.

---

## Table of Contents

- [Installation](#installation)
- [Usage Examples](#usage-examples)
  - [1. Keyword Search](#1-keyword-search)
  - [2. Group Results by Organism](#2-group-results-by-organism)
  - [3. Filter by Assay Type](#3-filter-by-assay-type)
  - [4. View Metadata by GEO ID](#4-view-metadata-by-geo-id)
  - [5. Download GEO Family Data](#5-download-geo-family-data)
- [Requirements](#requirements)
- [License](#license)

---

## Installation

```bash
pip install geofetchr
```

---

## Usage Examples

**Try out this demo! Enter your input, and the code will automatically call the package functions to generate a organized, well-formatted output.**

### 1. Keyword Search
Search GEO datasets by keyword(s), and display organism counts and dataset counts.


```python
import geofetchr

def main():
    global all_results
    while True:
        keywords_input = input("\nEnter keywords (comma-separated, or type 'exit' to quit): ").strip()
        if keywords_input.lower() == 'exit':
            print("Exiting program.")
            return

        keywords = [k.strip() for k in keywords_input.split(",")]
        print("\nFetching all GEO data...")
        all_results = geofetchr.search_geo_data(keywords)

        print("\n🔹 Top Organisms:")
        for organism, count in all_results["ORGANISM_COUNTS"].items():
            print(f"{organism} ({count})")

        print("\n🔹 Dataset Counts:")
        print(f"Total GDS results: {len(all_results['GDS'])}")
        print(f"Total GPL results: {len(all_results['GPL'])}")
        print(f"Total GSE results: {len(all_results['GSE'])}")

if __name__ == "__main__":
    main()
```

Sample output:
```
🔹 Top Organisms:
Homo sapiens (1673)
Mus musculus (274)
Rattus; synthetic construct (1)

🔹 Dataset Counts:
Total GDS results: 64
Total GPL results: 18
Total GSE results: 2564
```

---

### 2. Group Results by Organism
Filter datasets for selected organisms, and view dataset link, PubMed references, and assay type.

```python
import geofetchr

def main():
    global grouped_results
    while True:
        selected_organisms_input = input(
            "\nEnter organism(s) to filter (comma-separated) or 'exit' to quit: ").strip().lower()
        if selected_organisms_input == 'exit':
            print("Exiting program.")
            return False

        selected_organisms = [o.strip() for o in selected_organisms_input.split(",")]
        grouped_results = geofetchr.group_results_by_organism(all_results, selected_organisms)

        for organism, data in grouped_results.items():
            print(f"\n🔹 Organism: {organism}")
            for category, entries in data.items():
                print(f"{category}: {len(entries)} results")
                for entry, link, pubmed_links, assay_type in entries:
                    pubmed_str = ", ".join(pubmed_links)
                    print(f"{entry} - Dataset: {link} - PubMed: {pubmed_str}\nAssay Type: {assay_type}\n")

if __name__ == "__main__":
    main()
```

Sample output:
```
🔹 Organism: danio rerio
GDS: 0 results
GPL: 0 results
GSE: 46 results

GSE279773 - Dataset: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE279773 - PubMed: https://pubmed.ncbi.nlm.nih.gov/39832654
Assay Type: Expression profiling by array

GSE162148 - Dataset: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE162148 - PubMed: https://pubmed.ncbi.nlm.nih.gov/34140474 
Assay Type: Expression profiling by high throughput sequencing, Genome binding/occupancy profiling by high throughput sequencing, Non-coding RNA profiling by high throughput sequencing
```

---

### 3. Filter by Assay Type
Show available assay types and datasets grouped by assay type.

```python
import geofetchr

def main():
    while True:
        action = input("\nOptions:  'assay' to filter by assay type or 'exit' to quit: ").strip().lower()
        if action == 'exit':
            print("Exiting program.")
            return False
        elif action == 'assay':
            geofetchr.filter_by_assay_type_across_all(grouped_results)
        else:
            print("Invalid option. Try again.")

if __name__ == "__main__":
    main()
```

Sample output:
```
🔹 Available Assay Types:
Expression profiling by high throughput sequencing: 29 datasets
Expression profiling by array: 6 datasets
Expression profiling by array, Non-coding RNA profiling by array: 1 datasets

🔹 Datasets with Assay Type 'Expression profiling by high throughput sequencing':
GSE281891 (rattus norvegicus) - Dataset: https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE281891
PubMed: No PubMed Link
```

---

### 4. View Metadata by GEO ID
Retrieve detailed metadata for any GEO accession (GSE, GDS, GPL).

```python
import geofetchr

def main():
    while True:
        action = input("\nOptions:  'id' to view metadata or 'exit' to quit: ").strip().lower()
        if action == 'exit':
            print("Exiting program.")
            return False
        elif action == 'id':
            geofetchr.view_metadata_by_id()
        else:
            print("Invalid option. Try again.")

if __name__ == "__main__":
    main()
```

Sample output:
```
🔹 Metadata for GSE108484:
title: ['Transcriptome analysis of Chrdl1-treated RGCs']
geo_accession: ['GSE108484']
status: ['Public on Oct 29 2018']
submission_date: ['Dec 23 2017']
last_update_date: ['Mar 28 2022']
pubmed_id: ['30344043']
summary: ['Chrdl1 treatment promotes formation of synapses and GluA2-AMPAR recruitment in Retinal ganglion cell (RGC) cultures. Analysis of the transcriptome of RGCs with or without Chrdl1 treatment let us determine potential alterations in the expression of genes related to BMP signaling, or genes involved in excitatory synaptogenesis and AMPAR trafficking.']
overall_design: ['RNA was isolated from RGC cultures treated for 12 hours with 1ug/ml Chrdl1, and compared to RNA isolated from buffer-treated (vehicle) RGCs as a control.']
type: ['Expression profiling by high throughput sequencing']
contributor: ['Elena,,Blanco-Suarez', 'Maxim,N,Shokhirev', 'Nicola,,Allen']
sample_id: ['GSM2901408', 'GSM2901409', 'GSM2901410', 'GSM2901411', 'GSM2901412', 'GSM2901413']
contact_name: ['April,Elizabeth,Williams']
contact_email: ['apriljack06@gmail.com, awilliams@salk.edu']
contact_phone: ['7345461645']
contact_department: ['IGC']
contact_institute: ['Salk Institute for Biological Studies']
contact_address: ['10010 N Torrey Pines Rd']
contact_city: ['San Diego']
contact_state: ['California']
contact_zip/postal_code: ['92037']
contact_country: ['USA']
supplementary_file: ['ftp://ftp.ncbi.nlm.nih.gov/geo/series/GSE108nnn/GSE108484/suppl/GSE108484_fpkm_rat.txt.gz']
platform_id: ['GPL18694']
platform_taxid: ['10116']
sample_taxid: ['10116']
relation: ['BioProject: https://www.ncbi.nlm.nih.gov/bioproject/PRJNA427397', 'SRA: https://www.ncbi.nlm.nih.gov/sra?term=SRP127490']

```

---

### 5. Download GEO Family Data
Download and store the full GEO Family (GSE) dataset for further processing and visualization.

```python
import geofetchr

def main():
    while True:
        action = input("\nOptions:  'id' to download or 'exit' to quit: ").strip().lower()
        if action == 'exit':
            print("Exiting program.")
            return False
        elif action == 'id':
            gse_number = input("\nEnter a GEO ID (e.g., GSE12345, GDS67890, GPL13579): ")
            geofetchr.download_geo_family(gse_number)
        else:
            print("Invalid option. Try again.")

if __name__ == "__main__":
    main()
```

Sample output:
```
🔹 Downloading GEO Family file for GSE108484...
Saved as: GSE108484_family.soft.gz
```

---

## Requirements

- Python >=3.10
- GEOparse>=2.0.4
- pandas>=2.3.1
- requests>=2.32.5

---

## License

`geofetchr` is distributed under the terms of the [MIT](https://spdx.org/licenses/MIT.html) license.
