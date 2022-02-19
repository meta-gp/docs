# Documentation

`META-GP` platform documentation:

1. Source code conventions and contribution guidelines
2. API access documentation for production
3. Standard operation protocols (SOP) for production

## Project environments

### Python 

Dependencies:

* `conda` with `mamba` installed
* pre-commit `black` formatting 

```
mamba create -n <project-env> -c conda-forge poetry python=3.10
poetry install 
pre-commit install
```

### Rust

Dependencies:

* `rustc` / `cargo`

## Contribution

Continuous integration with tests is enabled for most repositories (Python/Rust) o nthe `test` branch, and will be enabled on the `main` branch once 

### Git

You need to habe `SSH` keys setup for your account to pull and contribute code to the private repositories. Cloning repositories should therefore look like:

```
git clone git@github.com/meta-gp/<repo>
```

### Code base

1. Do not push to `main` branch directly, always work with pull requests on development branches (usually `dev`)
2. Issues are used to track changes to source code, use the issue for reference in commits
3. Pull requests should refer to the issues and outline the changes to be integrated with main
4. Test-driven development if possible - `pytest` for Python projects, `cargo` standard tests for Rust
