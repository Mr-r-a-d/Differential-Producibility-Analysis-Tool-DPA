# Differential Producibility Analysis Tool DPA

## Overview
The DPA Tool performs comprehensive metabolic network analysis through Flux Balance Analysis (FBA) and Knockout (KO) studies, focusing on metabolite producibility across different conditions. This tool integrates analysis, ranking, and visualization steps using rpy2 for R integration and offers two analysis modes via separate scripts:
- `DPA_Class1.py`: For one-class/unpaired analysis
- `DPA_Class2.py`: For two-class analysis with replicates

## Prerequisites

- Expression data folder (CSV files with 'gene_id' and 'log2FoldChange' column)
- GSMN-TBupdated_original.sfba (Metabolic model file)
- Problem_template_FBA.pfile
- sfba-glpk executable (GLPK solver)

### Environment Setup
```bash
# Create and activate conda environment
conda env create -f dpa_env.yml
conda activate DPA_env
```

## Pipeline Components

### 1. FBA-KO Analysis
*Description:* 
Performs Flux Balance Analysis and Knockout analysis for metabolites in the network

*Steps:*
- Extracts metabolites from network
- Generates KO Problem file from FBA template
- Processes problem files using sfba-glpk
- Calculates producibility metrics and maps expression data
- Generates upregulated and downregulated matrices

*Output format:*
```
Reaction_name | Producibility_Difference | Affects_Producibility | equation | Rule | log2FoldChange | Upregulated_Signal | Downregulated_Signal
```

*Key features:*
- Handles gene expressions with OR/AND relationships
- User-configurable expression mapping preferences
- User-configurable expression Threshold for Up and Down regulation
- Automatic cleanup of mismatched/empty files 
- Parallel processing for improved performance

### 2. RankProd Analysis
*Description:*
Ranks metabolites based on expression values using the RankProd package and extracts top ranking metabolites.

*Analysis modes:*
- One-class (DPA_Class1.py): Treats conditions as replicates, useful for unpaired analysis
- Two-class (DPA_Class2.py): Handles data with biological replicates

*Output format:*
```
Metabolite | Condition1_Exp | Condition2_Exp | Condition2 \ Condition1 | Condition1 / Condition2 | Condition1 < Condition2 rank | Condition1 > Condition2 rank | Condition1 < Condition2 p.value | Condition1 > Condition2 p. value | Condition1 < Condition2 pfp value | Condition1 > Condition2 pfp value | Log(2) Average Cond1/Cond2
```
*Output columns:*
- Metabolite
- Expression values
- Ratio between conditions
- Ranking
- P-values
- False positive probabilities (pfp)
- Average fold change

*User preferences:*
- Selection of stress condition combinations to analyze
- P-value, PFP thresholds and No. of top ranking metabolites for studying significant metabolites

*Note:* In case of replicates specify the expressionfile name with _Rep1, _Rep2, _Rep3, etc. 

### 3. Visualization Analysis
*Description:*
Generates comprehensive visualizations and comparative analyses.

*Visualization:*
- Venn diagrams for each condition compared to others
- Venn diagram for all conditions
- Exports the results in tabular format (csv files)
- UpSet plots for upregulated and downregulated metabolites
- Venn diagrams (for conditions <= 5)
- Hierarchical clustering and bootstrap analysis

## Usage

### Running the Pipeline

Execute the entire pipeline using the single script:
```bash
python.exe DPA_Class1.py
```
### Interactive Prompts
1. **Prioritization Method**
   - Enter 'higher' or 'lower' for expression mapping (default: higher)

2. **Threshold Settings**
   - Set threshold for up/down regulation (default: 0)

3. **RankProd Analysis**
   - Option to analyze specific condition combinations or all combinations

4. **Metabolite Selection**
   - Select metabolites based on: rank, p-value, or PFP value, input threshold value for p-value and PFP
   - Specify number of top metabolites to extract (default: 100) based on rank

## Directory Structure
```
base/
├── Expression/                   # Expression data files
├── JyMet2.3/                     # SurreyFBA executable package
├── Problems_FBA/                 # Problem files for Flux Balance Analysis
├── Problems_KO/                  # Problem files for Knockout analysis
├── FBA_result/                   # FBA analysis outputs
├── KO_result/                    # KO analysis outputs
├── Producibility_Results/        # Producibility and mapping results
├── RankProd_Inputs/              # Rank Product inputs (upregulated/downregulated matrices)
├── Results/                      # Rank Product Analysis results
├── Top_Ranks/                    # Top ranked metabolites
│   ├── Downregulated/
│   └── Upregulated/
└── Infographics/                 # Visualization outputs
    ├── Downregulated/
    └── Upregulated/
```

## Output Files

### Analysis Results
- metabolites_log.txt: List of metabolites in the network
- FBA and KO analysis outputs
- Metabolite analysis CSV files (for each metabolite)
- RankProd matrices and results CSV files
- Top-ranked metabolites by condition comparison CSV files

### Visualizations
- Venn diagrams (.png) for each condition and all conditions combined
- Venn data in CSV format (Condition_Specific and Global_Overlap)
- UpSet plots (.pdf) for upregulated and downregulated metabolites
- Hierarchical clustering with bootstrap analysis (.pdf)

## Troubleshooting
- Ensure correct file formats (CSV with 'gene_id' as first column)
- Verify all prerequisites are present in the base directory
- Verify the package installations provided in (dpa_env.yml)
- (Linux) Check file permissions for sfba-glpk (chmod +x JyMet2.3/bin/Linux/sfba-glpk)
- For Linux and Mac OS get the sfba-glpk executables from "JyMet2.3 > bin" folder
- Make changes in FBA-KO_Producibility_Up-Down-Matrices.py for 'sfba-glpk.exe' to 'sfba-glpk' for Linux or Mac
- Monitor system resources during parallel processing 

## Dependencies
See `dpa_env.yml` for complete environment specifications.

Core dependencies:
- Python
- R
- rpy2
