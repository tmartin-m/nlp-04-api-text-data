# nlp-04-api-text-data

[![Python 3.14+](https://img.shields.io/badge/python-3.14%2B-blue?logo=python)](#)
[![MIT](https://img.shields.io/badge/license-see%20LICENSE-yellow.svg)](./LICENSE)

> > Structured EVTL pipeline for reliable ingestion and transformation of JSON data from web APIs.

Web Mining and Applied NLP require reliable acquisition and processing of structured and semi-structured text data.
This project implements a reproducible pipeline for working with JSON data from web APIs.

The pipeline follows an EVTL architecture:

- Extract data from an external API
- Validate structure and content before use
- Transform JSON into a structured representation
- Load results into a persistent, analyzable format

The emphasis is on correctness, inspectability, and repeatability:
every stage has explicit inputs, outputs, and logging,
and intermediate artifacts are preserved for verification.

## This Project

This project demonstrates how to work with JSON data retrieved from web APIs using a structured EVTL pipeline.

The workflow:

- Acquire JSON data from an external source
- Inspect and validate its structure
- Transform it into a tabular representation
- Persist results for downstream analysis

Each stage is implemented as a modular component with explicit inputs and outputs.

## Key Files

You'll work with these files as you update authorship and experiment:

## Key Files

These files define the EVTL pipeline and the components you will update for your project.

- **src/nlp/pipeline_api_json.py** - Main pipeline orchestrator (no changes required)
- **src/nlp/config_case.py** - Configuration for API access and paths (<mark>**copy and edit**</mark> for your project)
- **src/nlp/stage01_extract.py** - Extract stage: retrieves data from the API (no changes required)
- **src/nlp/stage02_validate_case.py** - Validate stage: inspects and verifies JSON structure (<mark>**copy and edit**</mark>)
- **src/nlp/stage03_transform_case.py** - Transform stage: converts JSON into structured data (<mark>**copy and edit**</mark>)
- **src/nlp/stage04_load.py** - Load stage: writes output to persistent storage (no changes required)
- **pyproject.toml** - Project metadata and dependencies (<mark>**update**</mark> authorship, links, and dependencies)
- **zensical.toml** - Documentation configuration (<mark>**update**</mark> authorship and links)

## First: Follow These Instructions

Follow the [step-by-step workflow guide](https://denisecase.github.io/pro-analytics-02/workflow-b-apply-example-project/) to complete:

1. Phase 1. **Start & Run**
2. Phase 2. **Change Authorship**
3. Phase 3. **Read & Understand**

## Success

After running the script successfully, you will see:

```shell
========================
Pipeline executed successfully!
========================
```

The following artifacts will be created:

- project.log - confirming successful run
- data/raw/case_raw.json - dump of the fetched JSON
- data/processed/case_processed.csv - final loaded result

## Command Reference

The commands below are used in the workflow guide above.
They are provided here for convenience.

Follow the guide for the **full instructions**.

<details>
<summary>Show command reference</summary>

### In a machine terminal (open in your `Repos` folder)

After you get a copy of this repo in your own GitHub account,
open a machine terminal in your `Repos` folder:

```shell
# Replace username with YOUR GitHub username.
git clone https://github.com/tmartin-m/nlp-04-api-text-data
cd nlp-04-api-text-data
code .
```

### In a VS Code terminal

```shell
uv self update
uv python pin 3.14
uv sync --extra dev --extra docs --upgrade

uvx pre-commit install
git add -A
uvx pre-commit run --all-files

# repeat if changes were made
git add -A
uvx pre-commit run --all-files

# Later, we install spacy data model and
# en_core_web_sm = english, core, web, small
# It's big: spacy+data ~200+ MB w/ model installed
#           ~350–450 MB for .venv is normal for NLP
# uv run python -m spacy download en_core_web_sm

# First, run the module
# IMPORTANT: Close each figure after viewing so execution continues
uv run python -m nlp.pipeline_api_json

uv run ruff format .
uv run ruff check . --fix
uv run zensical build

git add -A
git commit -m "update"
git push -u origin main
```

</details>

## Notes

- Use the **UP ARROW** and **DOWN ARROW** in the terminal to scroll through past commands.
- Use `CTRL+f` to find (and replace) text within a file.

## Example Artifact (Output)

```text
START PIPELINE
ROOT_PATH = .
DATA_PATH = data
RAW_PATH = data\raw
PROCESSED_PATH = data\processed
========================
STAGE 01: EXTRACT starting...
========================
SOURCE PATH = https://jsonplaceholder.typicode.com/posts
SINK PATH = data\raw\case_raw.json
========================
STAGE 02: VALIDATE starting...
========================
JSON STRUCTURE INSPECTION:
Top-level type: list
Keys in first record: ['userId', 'id', 'title', 'body']
Field types:
userId: int
id: int
title: str
body: str
Validation passed.
Sink: validated JSON object
========================
STAGE 03: TRANSFORM starting...
========================
Transformation complete.
DataFrame preview:
shape: (5, 6)
...preview of dataframe...
Sink: Polars DataFrame created
========================
STAGE 04: LOAD starting...
========================
SINK PATH = data\processed\case_processed.csv
========================
Pipeline executed successfully!
========================
```

## Enhancements

In production systems, validation is often automated using tools
such as **Great Expectations** or **Soda**.

Within the EVTL architecture, **VALIDATE** is a key stage
with a clear source, process, and sink:

- **Source**: JSON data extracted from the API
- **Process**: checking structure, confirming assumptions, and identifying data quality issues
- **Sink**: validated JSON passed to the TRANSFORM stage

This stage ensures the data is in a **consistent and reliable form**
before transformation begins,
so later steps can run without errors or unexpected results.

In this project, validation is implemented directly,
so all checks are visible, repeatable, and easy to review as part
of the pipeline.

## Phase 4. Make a Technical Modification

I updated the stage03_transform file to add a derived column that calculated the ratio of the length of the length of the body to the length of the title. The update was successful after fixing a small syntax issue.

The titles range in length from 12 characters to 79 characters, averaging around 40 characters. Wile the shortest body length is 102 characters and longest is 225 characters leaving an average of 102 characters. The smallest rates is about 1.72, while the largest is 17.17 and averaging around 4.81.

There is very little correlation between the title length and body length, but there is a negative correlation between title length and the body to title ratio. With such a wide range in the ratio it might also be beneficial to flag some of the outliers on both ends of the spectrum.

## Phase 5. Apply the Skills to a New Problem

I started by updating the api url in the config file to https://jsonplaceholder.typicode.com/albums and updating the file names in the pipeline to reflect the new copies before test running the pipeline to ensure the raw data was produced successfully.

After the successful run I updated the stage03_transform file to add custom derived columns to calculate the title length, average title length by user id, and the minimum/maximum title lengths for each user id. I stopped to ensure a successful pipeline run after adding the average title length column before adding the minimum and maximum to aid in trouble shooting if errors occured. I ran into several syntax errors and some larger areas due to frogetting the alias at first but was able to produces a successful pipeline run with all 3 columns.

From the processed file I was able to see that Title length varied pretty drastically from user to user. The averages range from 26 characters to 52 characters. Title lengths also varied heavily within each's specific users set. Like User 6 titles ranges from 74 characters to 29 characters while user 7 ranges from 48 characters to 11 characters.
