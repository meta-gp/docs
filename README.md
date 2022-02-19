# Documentation

`META-GP` platform documentation:

1. [Source code conventions and contribution guidelines](#source-code)
2. API access documentation for production
3. Standard operation protocols (SOP) for production

## Source Code

### Python 

Dependencies:

* `conda` with `mamba` installed
* pre-commit `black` formatting 

```
mamba create -n <project-env> -c conda-forge poetry python=3.10
conda activate <project-env>
poetry install 
pre-commit install
```

When adding internal (private) repositories to `poetry` use `ssh` pattern with the `main` branch, otherwise fails to detect `master`:

```
poetry add git+ssh://git@github.com/meta-gp/<repo>.git#main
```

### Rust

Dependencies:

* `rustc` / `cargo`

Continuous integration with tests is enabled for most repositories (Python/Rust) o nthe `test` branch, and will be enabled on the `main` branch once repositories are used or published for research and production.

### Git

You need to habe `ssh` keys setup for your account to pull and contribute code to the private repositories. Cloning repositories should therefore look like:

```
git clone git@github.com/meta-gp/<repo>
```

Some projects may have private repository dependencies, for example `mgp-sim` has `mgp-db` as a dependency; this requires `ssh` setup as above for installing dependencies, e.g. with `poetry`.

### Contributions

1. Do not push to `main` branch directly, always work with pull requests on development branches (usually `dev`)

```
git checkout dev
```

2. Issues are used to track changes to source code, use the issue for reference in commits, e.g.

```
git commit -m "add new feature [#7]"
git push origin dev
```

3. Pull requests should refer to the issues and outline the changes to be integrated with main
5. Test-driven development if possible - `pytest` for Python projects, `cargo` standard tests for Rust


