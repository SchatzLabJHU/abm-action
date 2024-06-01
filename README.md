# GitHub Action: ABM Benchmarks

This GitHub Action allows you to run [Automated Benchmarking in Galaxy (ABM)](https://github.com/galaxyproject/gxabm) on different Galaxy servers. ABM is a Python Bioblend script for automating benchmarking tasks in Galaxy, an open-source web-based platform for data-intensive biomedical research.

## Features

- Run ABM benchmarks on multiple Galaxy servers (usegalaxy.org, usegalaxy.org.au, usegalaxy.eu)
- Support for custom Galaxy servers
- Define benchmarks and experiments using YAML configuration files
- Import workflows and histories for benchmarking
- Summarize and analyze benchmark results

## Inputs

The following inputs are available for this action:

| Input | Description |
| --- | --- |
| `org-api-key` | API key for usegalaxy.org |
| `org-au-api-key` | API key for usegalaxy.org.au |
| `eu-api-key` | API key for usegalaxy.eu |
| `custom-server-url` | URL of a custom Galaxy server |
| `custom-server-api-key` | API key for the custom Galaxy server |
| `histories` | List of history URLs in the format "name: url" (one per line) |
| `histories-path` | Path to the file containing the list of history URLs |
| `workflows` | List of workflow URLs in the format "name: url" (one per line) |
| `workflows-path` | Path to the file containing the list of workflow URLs |
| `benchmarks-file` | Contents of the `benchmarks/testwf.yaml` file |
| `benchmarks-file-path` | File path of the `benchmarks/testwf.yaml` file in the repository |
| `experiments-file` | Contents of the `experiments/testwf.yaml` file |
| `experiments-file-path` | File path of the `experiments/testwf.yaml` file in the repository |

**Note:**
- At least one API key (`org-api-key`, `org-au-api-key`, `eu-api-key`, or `custom-server-api-key`) must be provided.
- Either `histories` or `histories-path` must be provided.
- Either `workflows` or `workflows-path` must be provided.
- Either `benchmarks-file` or `benchmarks-file-path` must be provided. Either `experiments-file` or `experiments-file-path` must be provided.

## Usage

### Example 1: Using File Contents Directly

```yaml
- name: Run ABM Benchmarks
  uses: your-repo/abm-benchmarks-action@main
  with:
    org-api-key: ${{ secrets.ORG_API_KEY }}
    histories: |
      variant-2g: https://benchmarking-inputs.s3.amazonaws.com/Variant/Variant-calling-inputs---2GB.rocrate.zip
    workflows: |
      variant: https://raw.githubusercontent.com/galaxyproject/iwc/main/workflows/variant-calling/generic-variant-calling-wgs-pe/Generic-variation-analysis-on-WGS-PE-data.ga
    benchmarks-file: |
      - output_history_base_name: Variant-Calling
        workflow_id: Generic variation analysis on WGS PE data
        runs:
        - history_name: 2GB
          inputs:
          - name: Paired Collection
            collection: Subsample of reads from SRR24043307
          - name: GenBank genome
            dataset_id: GRCh38.p14.gbff.gz
          - name: Name for genome database
            value: h38
    experiments-file: |
      name: Variant-Analyses
      runs: 3
      benchmark_confs:
        - benchmarks/vc.yml
      cloud:
        - europe
        - australia
      galaxy:
        namespace: galaxy
        chart: anvil/galaxykubeman
```

### Example 2: Using File Paths

```yaml
- name: Run ABM Benchmarks
  uses: your-repo/abm-benchmarks-action@main
  with:
    org-api-key: ${{ secrets.ORG_API_KEY }}
    histories-path: histories.yml
    workflows-path: workflows.yml
    benchmarks-file-path: benchmarks/vc.yml
    experiments-file-path: experiments/variant-analyses.yml
```

In the second example, the histories.yml, workflows.yml, benchmarks/vc.yml, and experiments/variant-analyses.yml files should exist in your repository with the appropriate contents.

