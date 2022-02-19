# Documentation

`META-GP` platform documentation `v0.1.0`

Developed for `Linux` operating systems, packages are also tested on `MacOS`.

- [Source code conventions and contribution guidelines](#source-code)
  - [Git usage](#git)
  - [Code contribution](#contribution)
  - [Continuous integration](#continuous-integration)
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

2. Issues are used to track development

![issues](https://user-images.githubusercontent.com/12873366/154821287-0aaa4faf-931c-4136-b845-0e2e16f6c7ba.png)

Task check boxes can be helpful sometimes

![tasks](https://user-images.githubusercontent.com/12873366/154821326-7930479d-8ec9-4b05-b7fd-868d91666b17.png)


3. Use the issue for reference in commits, e.g.

```
git add .
git commit -m "add new feature [#7]"
```

![refissue](https://user-images.githubusercontent.com/12873366/154821338-f34b0e85-94e8-4db4-a848-2a72edd25965.png)


4. Push to respective branch features are developed on, e.g.

```
git push origin dev
```

5. Pull requests should refer to the issues and outline the changes to be integrated with `main`, you can tag developers for code review and delete the branch, if you are using a feature specific branch.

> ⚠️ Generally do not delete the `dev` branch as done for test purposes in this image

![pull](https://user-images.githubusercontent.com/12873366/154821391-df314c92-cca5-475a-a086-a9ea45f89d21.png)


6. Test-driven development if possible - `pytest` for Python projects, `cargo` standard tests for Rust. Understandably there is currently a need for balance between outputs and test-driven development (which takes time), but we should strive to follow best practices, even if that means going back to write test suites for already developed code bases

### Continuous integration


Continuous integration with testing on `Mac` and `Linux` operating systems is enabled for most repositories (Python/Rust). It is currently on pushes or pull requests to the `test` branch, and will be enabled on the `main` branch once repositories are used or published for research and production.


### Python repositories

Dependencies:

`Miniconda` with default channel configuration and `mamba` installed into `base` environment [instructions for `Linux`]

```bash
curl -sL "https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh" > conda.sh
bash conda.sh  # follow instructions and setup for user shell
rm conda.sh && source ~/.bashrc  # if init is added to bash shell

# important, in this order
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
# mamba installer
conda install -n base mamba 
```

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

### Rust repositories

Dependencies:

* `rustc` / `cargo`

