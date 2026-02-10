# asm-to-reference-alignment

[![DOI](https://zenodo.org/badge/414304026.svg)](https://zenodo.org/badge/latestdoi/414304026)

A Snakemake workflow for aligning genome assemblies to a reference using [minimap2](https://github.com/lh3/minimap2). Given a set of assemblies and one or more reference genomes, this pipeline produces sorted BAM files, PAF alignments, BED summary statistics, UCSC chain files, and [SafFire](https://mrvollger.github.io/SafFire/)-compatible outputs for visualization. It also supports scaffolding with RagTag, ideogram plotting, and variant calling with dipcall.

## Setup

Install [pixi](https://pixi.sh) and then:
```bash
pixi install
```

## Quick start

```bash
# dry run
pixi run snakemake --configfile config/HPRCv2.yaml -n

# run
pixi run snakemake --configfile config/HPRCv2.yaml
```

## Configuration

Create a YAML config file and a tab-separated assembly table. See `config/` for examples.

### Config file

```yaml
ref:
  T2T-CHM13v2.0: /path/to/chm13v2.0.fa
  GRCh38: /path/to/hg38.analysisSet.fa
tbl: config/HPRCv2.asm.tbl
aln_threads: 16
mm2_opts: "-x asm5 --secondary=no -s 25000 -K 8G"
second_aln: "no"
break_paf: 1000
```

| Key | Description |
|-----|-------------|
| `ref` | Named reference genome(s). Multiple references are supported and run independently. |
| `tbl` | Path to the assembly table (see below). |
| `aln_threads` | Threads for minimap2 alignment. |
| `mm2_opts` | minimap2 options. Use `-x asm5` for closely related assemblies, `-x asm20` for more divergent ones. |
| `second_aln` | Set to a second reference FASTA to mask and realign (`"no"` to skip). |
| `break_paf` | Break PAF alignments on indels larger than this (bp). |

### Assembly table

A TSV with columns `sample` and `asm`. Each sample lists two comma-separated haplotype assemblies:

```
sample	asm
HG002	/path/to/HG002.pat.fa.gz,/path/to/HG002.mat.fa.gz
HG00438	/path/to/HG00438.hap1.fa.gz,/path/to/HG00438.hap2.fa.gz
```

## Targets

```bash
# default: reference alignments + SafFire outputs
pixi run snakemake --configfile config/HPRCv2.yaml

# ideogram plots
pixi run snakemake --configfile config/HPRCv2.yaml ideogram

# variant calling with dipcall
pixi run snakemake --configfile config/HPRCv2.yaml dipcall
```

## Test case

```bash
pixi run test
```

This runs the workflow on a small bundled test dataset in `.test/`.

## Development

```bash
# format Snakemake files
pixi run fmt
```

## Outputs

Results are written to `results/{ref_name}/` with the following structure:

| Directory | Contents |
|-----------|----------|
| `bam/` | Sorted, indexed BAM files per haplotype and merged diploid BAMs |
| `paf/` | PAF alignments |
| `paf_trim_and_break/` | PAF broken on large indels |
| `bed/` | Alignment statistics in BED format |
| `pdf/` | Ideogram visualizations (karyoplots) |
| `chain/` | UCSC chain files |
| `SafFire/` | BED files for [SafFire](https://mrvollger.github.io/SafFire/) visualization |
| `scaffold/` | RagTag scaffolded assemblies |
| `vcf/` | Variant calls (dipcall) |

---

## Gene conversion detection (Vollger et al., 2023)

This workflow also includes a gene conversion detection module used in [Vollger et al., 2023](https://doi.org/10.5281/zenodo.6792653). This is a specialized analysis that most users can ignore.

```bash
pixi run snakemake --configfile config/config_asm20.yaml gene_conversion
```

### Additional config options for gene conversion

| Key | Description |
|-----|-------------|
| `bed` | BED file of target regions on the reference (e.g., segmental duplications). |
| `min_aln_len` | Minimum alignment length after breaking to consider (default: 1000000). |
| `window` | Window size for realignment (default: 1000). |
| `slide` | Slide size for realignment (default: 100). |

### Config files from the paper

- Human assemblies: `config/config_asm20.yaml`, `config/table.asm.tbl`
- Clint PTR assembly: `config/clint.yaml`, `config/clint.asm.tbl`

Input assemblies are available on [Zenodo](https://doi.org/10.5281/zenodo.6792653).
