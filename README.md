# Defense Industry Knowledge Graph

A graph-based analysis of the organizations and ownership structures behind military equipment observed across contemporary and historical armed conflicts.

The project builds a reproducible pipeline from **Wikidata** to a multi-layer network connecting conflicts, military equipment, manufacturers, parent organizations and ultimate owners. It combines **SPARQL-based knowledge extraction**, iterative ownership-chain reconstruction and **network analysis** to study how concentrated, interconnected and recurrent defense-industry actors are across different conflicts.

## Research Focus

Rather than analysing conflicts only through countries or weapon systems, this project asks a different question:

> **Which industrial and ownership structures sit behind the military equipment associated with different conflicts, and how do those structures overlap across conflicts?**

The analysis focuses on three complementary levels:

- **Conflict → weapon → manufacturer** relationships extracted from Wikidata;
- **Manufacturer → owner / parent organization** chains reconstructed recursively;
- the resulting **cross-conflict ownership network**, analysed through graph and concentration metrics.

## Data Scope

The extraction pipeline covers **9 conflicts**:

- Russo-Ukrainian War
- Gaza war
- Syrian civil war
- 2006 Lebanon War
- Nagorno-Karabakh conflict
- Iraq War
- Kosovo War
- War in Afghanistan
- Iran-Iraq War

The dataset is generated programmatically from the **Wikidata Query Service**; no external source dataset is required to run the initial extraction stage.

The current extraction produces **835 conflict-level observations**, including **677 cleaned weapon-manufacturer links** and **196 unique manufacturers** across the nine conflicts.

## Methodology

### 1. Knowledge extraction

`M2_data_extraction.ipynb`

For each conflict, SPARQL queries retrieve entities and relations describing:

- conflict participants;
- military equipment associated with those participants;
- weapon types;
- manufacturers;
- manufacturer countries;
- countries of origin.

Queries are executed against the Wikidata SPARQL endpoint with retry and rate-limit handling. Individual conflict datasets are then merged into a common representation for downstream analysis.

### 2. Ownership-chain reconstruction

`M3_ownership_reconstruction.ipynb`

Starting from the set of unique manufacturers, the project expands the corporate graph iteratively through Wikidata relations such as:

- `P127` — owned by;
- `P361` — part of;
- `P749` — parent organization;
- subsidiary / owner relations;
- headquarters-country fallback relations when needed.

The expansion is performed up to **five levels**, while semantic terminal entities such as sovereign states, countries and individuals are removed from the exploration frontier.

The current reconstruction contains **822 ownership edges** and produces an enriched node table containing entity labels, entity types and country information.

### 3. Network analysis

`M4_network_analysis.ipynb`

The reconstructed data is transformed into graph representations used to examine both structural importance and cross-conflict overlap. The analysis includes:

- weighted degree;
- PageRank;
- betweenness centrality;
- in-degree;
- community assignment;
- owner-conflict incidence analysis;
- Jaccard similarity between conflicts;
- ownership concentration using the **Herfindahl-Hirschman Index (HHI)**;
- top-owner and top-three-owner concentration shares.

Two ownership perspectives are retained in the concentration analysis: a broader **country-level** view and a more restrictive **conglomerate/control-oriented** view.

## Selected Findings

The graph reveals substantial variation in ownership concentration and industrial overlap across conflicts.

- The **United States** is the highest-ranked entity in the exported owner-centrality table by conflict coverage and weighted degree, appearing across 8 of the 9 analysed conflicts.
- Conflict similarity is highly heterogeneous. For example, the owner sets associated with **Gaza and the 2006 Lebanon War** show a Jaccard similarity of approximately **0.92**, while **Iraq and Kosovo** reach approximately **0.81** under the exported similarity representation.
- Ownership concentration changes significantly depending on the analytical level. At the country level, some conflicts exhibit comparatively high HHI values, while the conglomerate-level representation generally distributes links across a larger set of organizations.

These results should be interpreted as properties of the reconstructed Wikidata graph rather than as a complete representation of the global defense industry. Coverage depends on the entities and relationships available in Wikidata.

## Repository Structure

```text
.
├── M2_data_extraction.ipynb
├── M3_ownership_reconstruction.ipynb
├── M4_network_analysis.ipynb
├── data/
│   ├── raw/                 # generated conflict-level extractions
│   └── merged/              # generated merged dataset
├── manufacturers_unique.csv
├── weapon_manufacturer_clean.csv
├── ownership_nodes.csv
├── ownership_edges.csv
├── owner_conflict_matrix.csv
├── owner_centrality.csv
├── conflict_jaccard_similarity.csv
├── hhi_per_conflict.csv
└── KG_ResearchProject_Report_Paola_Pinna(FINAL).pdf
```

Some generated CSV outputs are included in the repository to make the final network-analysis results directly inspectable.

## Reproducing the Analysis

Run the notebooks in sequence:

```text
M2_data_extraction.ipynb
        ↓
M3_ownership_reconstruction.ipynb
        ↓
M4_network_analysis.ipynb
```

The first notebook queries Wikidata and generates the conflict datasets. The second reconstructs the ownership graph, and the third performs the network-level and cross-conflict analyses.

Main Python libraries used across the pipeline include:

- `pandas` for tabular processing;
- `requests` for Wikidata SPARQL queries;
- `NetworkX` for graph construction and analysis;
- standard Python utilities for batching, parsing and iterative traversal.

Because the source is a live knowledge graph, rerunning the extraction at a later date may produce slightly different results as Wikidata evolves.

## Project Report

A detailed discussion of the research design, methodology and interpretation is available in the [full project report](./KG_ResearchProject_Report_Paola_Pinna%28FINAL%29.pdf).

## Author

**Paola Pinna**  
MSc Artificial Intelligence — Maastricht University
