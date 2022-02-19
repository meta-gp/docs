# Documentation

`META-GP` platform documentation `v0.1.0`



- [Source code conventions and contribution guidelines](#source-code)
  - [Git usage](#git)
  - [Code contribution](#contribution)
  - [Python repositories](#python-repositories)
  - [Rust repositories](#rust-repositories)
- API access documentation for production
- Standard operation protocols (SOP) for production

## Source Code

### Git

`SSH` keys setup for accounts to pull and contribute code to the private repositories. Cloning repositories should look like:

```
git clone git@github.com/meta-gp/<repo>
```

Some projects may have private repository dependencies, for example `mgp-sim` has `mgp-db` as a dependency; this requires `SSH` setup as above for installing dependencies, for example with `poetry` (see below).


### Contribution

1. Do not push to `main` branch directly, always work with pull requests on development branches (usually `dev`)

```
git checkout dev
```

or open a new feature branch

```
git checkout -b <feature>
```

and push to respective branch

```
git push origin dev
```

2. Issues are used to track changes to source code, use the issue for reference in commits, e.g.

```
git commit -m "add new feature [#7]"
```

3. Pull requests should refer to the issues and outline the changes to be integrated with main, you can tag developers for code review 
5. Test-driven development if possible - `pytest` for Python projects, `cargo` standard tests for Rust. Understandably there is currently a need for balance between outputs and test-driven development (which takes time), but we should strive to follow best practices, even if that means going back to write test suites for already developed code bases


### Python repositories

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

### Rust repositories

Dependencies:

* `rustc` / `cargo`

Continuous integration with tests is enabled for most repositories (Python/Rust) o nthe `test` branch, and will be enabled on the `main` branch once repositories are used or published for research and production.

