# Documentation

`META-GP` platform documentation `v0.1.0`

Developed for `Linux` operating systems, packages are also tested on `MacOS`.

- [Development Setup](#development-setup)
- [Conventions and contribution guidelines](#source-code)
  - [Git usage](#git)
  - [Code contributions](#code-contributions)
  - [Tests and CI](#tests-and-ci)
  - [Python repositories](#python-repositories)
  - [Rust repositories](#rust-repositories)
- API access documentation for production
- Standard operation protocols (SOP) for production

# Development Setup

The 'platform' consists of two main components:

1. The Nextflow pipeline and associated tools for executing and parsing results from the pipeline output.
2. The API, database and Docker server infrastructure and associated command-line client to interact with API

General Requirements:

1. Linux (`Ubuntu 18.04`) servers for API with a `main` server and a `hive` staging server, both have `/work` mounted data volume (e.g. `NFS` shared drive on the `Nectar` cluster), `Docker` operational and main user added to docker group. For local development setup the system is considered `main` and requires another remote `hive` staging server for file uploads with the current configuration. See more how to setup the backend in [meta-gp/mgp-system](https://github.com/meta-gp/mgp-system).
2. Pipeline: 32GB RAM, 8 threads, ~ 500 GB storage for the databases, ~ 2 TB scratch storage for development runs, test files (downloadable in `mgp-tools` client), `conda` environments for each pipeline module - see [meta-gp/modules/configs](https://github.com/meta-gp/modules/blob/main/configs/core.config) in development (set paths in config for pipeline to local installation, profiles can be added to the config file for the default `conda` path in the `-profile home_conda` the envs are at `/data/opt/conda/envs`). `Docker` containers are set for each module otherwise, but have not been tested for pipeline execution yet.

## Pipeline setup

DBs and host reference genomes for the current development stage have to be downloaded from remote storage and uncompressed, which is conveniently wrapped in `mgp-tools` - binaries are available for direct download:

```bash
DEV_STAGE="pre_alpha"
wget 
```

Database and reference files for current stage:

```

```


Test files for the current stage:

```

```

Then the pipeline stage (`-entry`) can be executed, for example using the default profile with `conda` environments setup, and a scratch directory for the workload:

```bash
nextflow run meta-gp/modules/workflows/pre_alpha.nf -c meta-gp/modules/configs/core.config \
-profile home_conda -entry pre_alpha -w /scratch/dev  \
--host_aligner strobealign --viral_aligner strobealign --bacterial_aligner strobealign \
--illumina_pe 'fastq/*_{R1,R2}.fastq' \
--host_alignment_index db/chm13v2.0.fa \
--viral_alignment_index db/virosaurus98_sr.mmi \
--viral_fasta db/virosaurus98_sr.fasta \
--bacterial_mash_index db/bacteria_complete.msh \
--bacterial_fasta db/bacteria_complete.fasta \
--bacterial_mash_min_shared_hashes 2 
```

Note here we are passing `fasta` files to the alignment index for host and viral alignments, due to `strobealign` not using pre-computed index files like `minimap2` e.g.

```bash
nextflow run meta-gp/modules/workflows/pre_alpha.nf -c meta-gp/modules/configs/core.config \
-profile home_conda -entry pre_alpha -w /scratch/dev  \
--host_aligner strobealign --viral_aligner strobealign --bacterial_aligner strobealign \
--illumina_pe 'fastq/*_{R1,R2}.fastq' \
--host_alignment_index db/chm13v2_sr.mmi \
--viral_alignment_index db/virosaurus98_sr.fasta \
--viral_fasta db/virosaurus98_sr.fasta \
--bacterial_mash_index db/bacteria_complete.msh \
--bacterial_fasta db/bacteria_complete.fasta \
--bacterial_mash_min_shared_hashes 2 
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

## Python repositories

Project environments with `poetry` package manager and pre-commit settings [setup in project repositories]

```bash
mamba create -n <project-env> poetry python=3.10
conda activate <project-env>
poetry install  # inside cloned project repository

pre-commit install  # inside cloned project repository
```

When adding internal (private) repositories to `poetry` use `ssh` pattern with the `main` branch, otherwise fails to detect `master`:

```
poetry add git+ssh://git@github.com/meta-gp/<repo>.git#main
```

Code annotation is currently in [Google style format](https://google.github.io/styleguide/pyguide.html) e.g. in `mgp_tools` (but generally inconsistent, feel free to open issue on this.

## Rust repositories

Dependencies:

* `rustc` / `cargo`

