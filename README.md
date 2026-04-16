# Comparative Whole-Genome Analysis of Drug Resistance Mutations in *Mycobacterium tuberculosis*

### A Multi-Gene Bioinformatics Approach

!\[Python](https://img.shields.io/badge/Python-3.x-blue)
!\[Platform](https://img.shields.io/badge/Platform-Google%20Colab-orange)
!\[Tools](https://img.shields.io/badge/Tools-BWA--MEM%20%7C%20SAMtools%20%7C%20bcftools-green)
!\[Status](https://img.shields.io/badge/Status-75%25%20Complete-yellow)
!\[License](https://img.shields.io/badge/License-MIT-lightgrey)

\---

## About the Project

This project implements a fully computational bioinformatics pipeline for analyzing drug resistance mutations in *Mycobacterium tuberculosis* (MTB) — the bacterium that causes Tuberculosis. Instead of slow and expensive lab-based drug susceptibility testing, this pipeline uses publicly available whole genome sequences from NCBI and compares them against the standard H37Rv reference genome to identify resistance-causing mutations computationally.

**No wet lab. No machine learning. No clinical phenotype labels. Just FASTA files and open-source tools.**

\---

## What This Project Does

* Downloads 7 *M. tuberculosis* strain genomes from NCBI
* Aligns each strain to the H37Rv reference genome using BWA-MEM
* Calls genome-wide variants using SAMtools and bcftools
* Extracts mutations from 5 clinically validated drug resistance genes
* Produces a co-resistance heatmap showing which strains resist which drugs
* Flags strains with MDR-confirmed resistance but no detectable pncA mutation — addressing the documented 43% unexplained pyrazinamide resistance gap in literature

\---

## Key Findings

|Result|Detail|
|-|-|
|Total mutations detected|20 across 7 strains and 5 genes|
|Most resistant strain|CCDC5180 — mutations in 4 genes simultaneously|
|Dominant genes|rpoB and katG — each mutated in 5/7 strains|
|embB result|0/7 strains — no ethambutol resistance detected|
|pncA FLAG strains|F11 and RGTB327 — MDR confirmed but pncA unexplained|
|Negative control|CDC1551 — zero mutations — pipeline validated|

\---

## Strains Analyzed

|Strain|Accession|Lineage|Origin|Drug Profile|
|-|-|-|-|-|
|H37Rv|NC\_000962.3|L4|—|Reference Genome|
|CCDC5079|NC\_017523.1|L2 Beijing|China|Drug Sensitive|
|CCDC5180|NC\_017522.1|L2 Beijing|China|MDR — all 4 drugs|
|F11|NC\_009565.1|L4|South Africa|pncA FLAG|
|KZN4207|NC\_016768.1|L4|South Africa|XDR-TB|
|KZN1435|NC\_012943.1|L4|South Africa|MDR-TB|
|RGTB327|NC\_017026.1|L3 CAS Delhi|India region|pncA FLAG|
|CDC1551|AE000516.2|L4|USA|Drug Sensitive — Negative Control|

\---

## Resistance Genes Screened

|Gene|Drug|H37Rv Coordinates|
|-|-|-|
|rpoB|Rifampicin|759,807 — 763,325|
|katG|Isoniazid|2,153,889 — 2,156,111|
|inhA|Isoniazid (alternate)|1,673,280 — 1,674,698|
|embB|Ethambutol|4,243,755 — 4,245,776|
|pncA|Pyrazinamide|2,288,681 — 2,289,241|

\---

## Tools and Technologies

|Tool|Purpose|
|-|-|
|BWA-MEM 0.7.17|Align strain genomes to H37Rv reference|
|SAMtools|SAM → BAM conversion, sorting, indexing|
|bcftools|Genome-wide variant calling|
|Python 3|Mutation extraction and analysis|
|Pandas|Mutation table construction|
|Seaborn / Matplotlib|Co-resistance heatmap visualization|
|Google Colab|Cloud-based Linux execution environment|

\---

## Pipeline Overview

```
Step 01 → Download H37Rv reference genome from NCBI
Step 02 → Index reference (bwa index + samtools faidx)
Step 03 → Download 7 strain FASTA files from NCBI
Step 04 → Align each strain to H37Rv (BWA-MEM)
Step 05 → SAM → BAM → Sort → Index (SAMtools)
Step 06 → Genome-wide variant calling (bcftools mpileup + call)
Step 07 → Extract mutations in rpoB, katG, inhA, embB, pncA
Step 08 → Build mutation table (Python + Pandas)
Step 09 → Generate co-resistance heatmap (Seaborn)
Step 10 → Flag pncA unexplained resistance cases
```

\---

## Repository Structure

```
TB-Drug-Resistance-Analysis/
│
├── BIOINFORMATICS\_PROJECT.ipynb   ← Main Colab notebook (all 10 cells)
│
├── results/
│   ├── mutation\_table.csv         ← 20 detected mutations
│   └── coresistance\_heatmap.png   ← Heatmap visualization
│
├── README.md                      ← This file
```

\---

## Variant Calling Results

|Strain|Genome-Wide Variants|
|-|-|
|CCDC5079|3,881|
|CCDC5180|3,482|
|CDC1551|2,986|
|KZN1435|2,707|
|KZN4207|2,451|
|F11|1,954|
|RGTB327|10,502|

> RGTB327 has the highest count (10,502) because it is Lineage 3 — genetically most distant from the Lineage 4 H37Rv reference. This is lineage divergence, not higher drug resistance.

\---

## pncA Unexplained Resistance

```
\[FLAG] F11
       rpoB + katG mutated → MDR confirmed
       BUT no pncA mutation detected
       → Pyrazinamide resistance is UNEXPLAINED

\[FLAG] RGTB327
       rpoB + katG mutated → MDR confirmed
       BUT no pncA mutation detected
       → Pyrazinamide resistance is UNEXPLAINED
```

This finding directly surfaces the documented gap that 43% of pyrazinamide-resistant strains globally have no known pncA mutation — confirmed across Papers 7 and 11 in our literature review.

\---

## Known Limitations

* Pipeline covers only 5 first-line drug resistance genes. XDR-TB resistance genes (gyrA, rrs) are not included — which is why KZN4207 shows zero mutations despite being XDR-TB.
* The standard H37Rv reference NC\_000962.3 is missing \~6.4kb of sequence in 10 regions (Paper 12, Chitale et al. 2023).
* H37Rv is a Lineage 4 strain — Lineage 2 and 3 strains may have slightly lower alignment quality (Paper 15).
* Mutation name annotation (S450L, S315T etc.) not yet implemented — positions are reported as raw genomic coordinates.

\---

## Planned for Next Phase

* \[ ] Mutation name annotation — map positions to standard names (S450L, S315T, M306V)
* \[ ] Gene frequency bar chart
* \[ ] Automatic resistance classification (MDR / XDR / Drug Sensitive) per strain
* \[ ] Extended gene panel — add gyrA and rrs for XDR-TB coverage
* \[ ] Phylogenetic tree of all 7 strains

\---

## Literature

This project is based on analysis of 15 peer-reviewed papers published between 2023 and 2026. Key references:

* Pal \& Mohanty (2025) — *Bioinformatics Advances*
* CRyPTIC Consortium (2025) — *Nature Communications*
* He et al. (2024) — *PMC* — WHO catalogue benchmarking
* Auganova et al. (2023) — *Microbiology Resource Announcements* — closest methodological reference
* Chitale et al. (2023) — *Nature Communications* — H37Rv reference update

Full reference list is available in the project report.

\---

## Academic Context

**Institution:** VIT Chennai
**Programme:** Integrated M.Tech — Computer Science \& Engineering (Business Analytics)
**Course:** Bioinformatics Project
**Academic Year:** 2025–2026
**Review:** DA-2 — Review 2 (75% Implementation)

\---

## License

This project is open source under the MIT License. All genome data used is publicly available from NCBI with no restrictions on academic use.

\---

*If you use this pipeline or find it useful, feel free to star the repository.*

