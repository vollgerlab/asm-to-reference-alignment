# Reference alignment workflow
[![DOI](https://zenodo.org/badge/414304026.svg)](https://zenodo.org/badge/latestdoi/414304026)

This repository is a snakemake workflow for aligning many genome assemblies to a reference genome using my preferred parameters, tools, and outputs.

This workflow is also convenient for making inputs for my visualization tool [SafFire](https://mrvollger.github.io/SafFire/).

## Setup

Install [pixi](https://pixi.sh) and then:
```bash
pixi install
```

## Try the test case

```bash
pixi run test
```

## An example run

```bash
pixi run snakemake --configfile config/config.yaml
```

## An example run with ideograms

```bash
pixi run snakemake --configfile config/config.yaml ideogram
```

## Format workflow files

```bash
pixi run fmt
```

### Notes on use of the pipeline in Vollger et al., 2023
Running alignment and gene conversion identification pipeline:
```bash
pixi run snakemake \
    --configfile config/config_asm20.yaml \
    gene_conversion
```
Information on where to download the input assemblies can be found on [Zenodo](https://doi.org/10.5281/zenodo.6792653).

Config files for human assemblies:
```
config/config_asm20.yaml
config/table.asm.tbl
```
Config files for the Clint PTR assembly:
```
config/clint.yaml
config/clint.asm.tbl
```
