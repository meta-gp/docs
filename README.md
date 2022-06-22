# Documentation

`META-GP` platform documentation `v0.1.0`

Developed for `Linux` operating systems, packages are also tested on `MacOS`.

- [Development Setup](#development-setup)
  - [Pipeline](#pipeline-setup)
  - [Git usage](#git)
  - [Code contributions](#code-contributions)
  - [Tests and CI](#tests-and-ci)
- API access documentation for production
- Standard operation protocols (SOP) for production

# Development Setup

The 'platform' consists of two main components:

1. The Nextflow pipeline and associated tools for executing and parsing results from the pipeline output.
2. The API, database and Docker server infrastructure and associated command-line client to interact with API

General Requirements:

1. Linux (`Ubuntu` dist) servers for API with a `main` server and a `hive` staging server, both have `/work` mounted data volume (e.g. `NFS` shared drive on the `Nectar` cluster), `Docker` operational and main user added to docker group. For local development setup the system is considered `main` and requires another remote `hive` staging server for file uploads with the current configuration. See more how to setup the backend in [meta-gp/mgp-system](https://github.com/meta-gp/mgp-system).
2. Pipeline: 32GB -48GB RAM (with current RefSeq eukaryot ausbset alignment workflow), 8 threads, ~ 500 GB storage for the databases, ~ 2 TB scratch storage for development runs, test files (downloadable in `mgp-tools` client), `conda` environments for each pipeline module - see [meta-gp/modules/configs](https://github.com/meta-gp/modules/blob/main/configs/core.config) in development (set paths in config for pipeline to local installation, profiles can be added to the config file for the default `conda` path in the `-profile home_conda` the envs are at `/data/opt/conda/envs`). `Docker` containers are set for each module otherwise, but have not been tested for pipeline execution yet.

## Current development branches

Nextflow pipeline stuff

* `modules` - all the nextflow modules and configs by development stage
* [`mgp-tools`]() - Rust command line client for some performance oriented tasks 


API:

* [`mgp-api`]: FastAPI for interaction with the MetaGP network of nodes for data uploads, authentication, database access; also has the containers and `docker-compose files` for the containers to run in dev and production
* [`mgp-net`]: Rust command line client for nodes and pipelines to interact with the MetaGP API, e.g. to deposit results or pull reports


Tools:

* [`vircov`](https://github.com/meta-gp/vircov) fork of reference genome coverage stats and remapping selection tool (distinct regions of coverage for low abundance viral hits), upstream dev branch is `v0.6.0`. Used as `metacov vircov` command line subtask for viral coverage. Additional selection of single reference genomes for re-mapping has to be improved and tests need to be written.
* [`mgp-sim`](https://github.com/meta-gp/mgp-sim) - simulation pipeline, dev branch `dev-v0.2.0`. Recently added additional RefSeq simulation panels with the small Illumina MiSeq read type settings. Next steps are tests of the rather convoluted Python code base and additional Nextflow modules for simulating reads on the backend. Ties in with `mgp-val` repository where the read identity tags (e.g. `taxid`) in the header of the reads simulated are parsed and can be evaluated e.g. to determine levels of host read depletion or microbial loss when testing the depletion module of the main pipeline. This repo currently contains the `MGP-DB` SQLite database and interface for sampling, which should probably be made independent to construct the reference databases and indices for the pre-alpha pipeline (and k-mer profiling tools particularly) - also to annotate the reference sequences for downstream use (e.g. grouping in `Vircov` by `taxid='<taxid>'` reference descriptions.
* [`mgp-val`](https://github.com/meta-gp/mgp-val) - sensitivity / specificity etc. assessment from wetlab or other reference data (in specific `JSON` format) against outputs from the pipeline, currently a draft that strongly ties in with the Steve Miller et al. (2019) CSF SURPI+ validation paper and the viral clinical and sequencing results (which have been parsed


## Pipeline setup

DBs and host reference genomes for the current development stage have to be downloaded from remote storage and uncompressed, which is conveniently wrapped in `mgp-tools` - binaries are available for direct download:

```bash
```

Database and reference files for current stage:

```
TO BE DONE
```


Test files for the current stage:

```
TO BE DONE
```

Then the pipeline stage (`-entry`) can be executed, for example using the default profile with `conda` environments setup, and a scratch directory for the workload. Note here we are using `minimap2` and short-read indices by default. Input `Fastq` files have typical `R1/R2` tails for PE Illumina data and can be `gz` compressed.

```bash
nextflow run meta-gp/modules/workflows/pre_alpha.nf \
-c meta-gp/modules/configs/core.config \
-profile home_conda \
-entry pre_alpha \
-w /scratch/dev  \
--illumina_pe 'fastq/*_{R1,R2}.fastq.gz'
```

Switch to `StrobeAlign` for all domain alignments requires to switch to `Fasta` files for host and viral alignments due to `StrobeAlign` not using pre-computed index files (in `0.7.1`). Here we explicitly state all domain profiling reference files and aligners:

```bash
nextflow run meta-gp/modules/workflows/pre_alpha.nf \
-c meta-gp/modules/configs/core.config \
-profile home_conda \
-entry pre_alpha \
-w /scratch/dev  \
--host_aligner strobealign \
--viral_aligner strobealign \
--bacterial_aligner strobealign \
--illumina_pe 'fastq/*_{R1,R2}.fastq.gz' \
--host_alignment_index db/chm13v2_sr.fasta \
--viral_alignment_index db/virosaurus98_sr.fasta \
--viral_fasta db/virosaurus98_sr.fasta \
--bacterial_mash_index db/bacteria_complete.msh \
--bacterial_fasta db/bacteria_complete.fasta
```

Alignment and mash index names must match the associated `fasta` names from which the indices are computed (checked in pipeline).

## Git usage

`SSH` keys setup for accounts to pull and contribute code to the private repositories. Cloning repositories should look like:

```
git clone git@github.com/meta-gp/<repo>
```

Some projects may have private repository dependencies, for example `mgp-sim` has `mgp-db` as a dependency; this requires `SSH` setup as above for installing dependencies, for example with `poetry` (see below).


## Code contributions

Do not push to `main` branch directly, always work with pull requests on development branches (usually `dev`)

```
git checkout dev
```

or open a new feature branch

```
git checkout -b <feature>
```

Issues are used to track development; task check boxes can be helpful sometimes and are automatically recognised and tracked in the issue descriptions. In Markdown these can be implemented like this:

```markdown
- [x] completed
- [ ] not completed
```


Use issues for reference in commits.

```
git add .
git commit -m "add new feature [#7]"
```

Commits may trigger pre-commit hooks, like the `black` code formatter; add changes and use the same commit after the hook has executed.

```
git add .
git commit -m "add new feature [#7]"
```

Push to respective branch features are developed on.

```
git push origin dev
```

Pull requests should refer to the issues and outline the changes to be integrated with `main`, you can tag developers for code review and delete the branch, if you are using a feature specific branch (not the `dev` branch).

## Tests and CI

Test-driven development should be done if possible - `pytest` for Python projects, standard tests for Rust projects. Understandably there is currently a need for balance between outputs and test-driven development (which takes time).

Continuous integration with testing on `Mac` and `Linux` operating systems is disabled for most repositories (Python/Rust). Some drafts for the action templates are available in the repositories.

