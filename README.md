# SwarmUS-doc

This repo stores all the user documentation for the SwarmUS project.

# Using this repo

The documentation website is built using [MkDocs](https://www.mkdocs.org/) with the [mkdocs-material](https://squidfunk.github.io/mkdocs-material/) theme.

## Setting up you local environment

Prerequisites:
* [Python 3.x](https://www.python.org/downloads/)
* [Pip](https://pypi.org/project/pip/)

Install the required tools and dependencies. This will install MkDocs and the Material theme.

```sh
pip install -r requirements.txt
```

## Serving locally

To launch the website locally, run

```sh
mkdocs serve
```

## Building and Deploying

Deploying to GitHub Pages is done by using this command:

```sh
mkdocs gh-deploy
```