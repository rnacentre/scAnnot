**scAnnot User Documentation**
================

<!-- WARNING: THIS FILE WAS AUTOGENERATED! DO NOT EDIT! -->

## Overview

scAnnot utilizes pre-trained SCANVI models on a core reference dataset
and organized by annotation hierarchy to recursively annotate
single-cell datasets at multiple levels (e.g. cell type \> subtype).

It generates annotated labels by leveraging cell representations learned
from the reference dataset. The modular workflow is highly customizable
and scalable to large datasets.

## Installation

To install scAnnot, please follow these steps:

**Step 1:** Ensure that you have Python 3.7 or higher installed on your
system.

**Step 2:** Open a terminal or command prompt.

**Step 3:** Install scAnnot using pip by executing the following
command:

    pip install scAnnot

**Step 4:** Once the installation is complete, you can verify it by
running the following command:

    scAnnot --version

If the installation was successful, the tool’s version number will be
displayed.

## Annotation Workflow

The main steps in the workflow are:

### 1. Data Preprocessing

The input AnnData object containing expression values is preprocessed to
filter to common genes with a reference dataset and apply optional
normalization steps.

This is handled by the `preprocess_data()` function.

### 2. Model Loading

Trained SCANVI models for each annotation level are stored in a
directory structure matching the hierarchy of annotations.

#### Model Directory Structure

The trained models are organized in a directory structure matching the
annotation hierarchy.

The root `/model.pt` file contains the model for Level 1 (primary cell
type) annotation.

Subdirectories named for each level cell type contain further nested
models:

    /root
    ├── model.pt  
    ├── L1_celltype1
    │   ├── model.pt
    │   ├── L2_celltype2  
    │   │   ├── model.pt 
    │   ├── L2_celltype3
    │       ├── model.pt
    ├── L1_celltype2
       ├── model.pt
    ├── L1_celltype3
       ├── model.pt

Each subdirectory contains a model for the next annotation level of
sub-celltypes specific to that cell type.

This organizational structure encodes the annotation schema and enables
the recursive modeling approach.

### 3. Recursive Annotation

Annotation begins with the top/primary cell types using the model in the
‘root/model.pt’ file.

Deeper levels are then recursively annotated by:

1.  Filtering to the current annotation
2.  Looping through sub-directories as next level down
3.  Loading appropriate model
4.  Making predictions to annotate
5.  Repeating on subset for next level

This is implemented by the `annotate_levels()` and
`annotate_deeper_levels()` functions. It allows fully generalized
annotation of hierarchical multi-level structure in single-cell
datasets.

## Usage

The scAnnot tool provides a command-line interface (CLI) and an
interactive interface (e.g., jupyter notebook) for annotating single
cell datasets. Here is an overview of the available commands and their
usage:

    scAnnot --input <input_file> --reference <reference_file> --model_dir <model_dir> --output <output_file>

-   `<input_file>`: Path to the single-cell data file.
-   `<reference_file>`: Path to the reference dataset file.
-   `<model_dir>`: Path to the directory containing trained SCANVI
    models.
-   `<output_file>`: Path to the output file where annotated data will
    be saved.

### In command line

**1. output the table of predicted lables in csv format**

    scAnnot human_brain_glial_Fu_2021_PCR/h5/umap.h5ad --output test.csv

**2. output the anndata with predicted lables in h5ad**

    scAnnot human_brain_glial_Fu_2021_PCR/h5/umap.h5ad --output test.h5ad

### In jupyter notebook

    ad=scAnnot('human_brain_glial_Fu_2021_PCR/h5/umap.h5ad')

    #show umap from the latent space
    ad=scAnnot('human_brain_glial_Fu_2021_PCR/h5/umap.h5ad',show=True)

show the UMAP plot:

## Additional Details

-   **Supported Data Formats:** scAnnot now only supports data in H5ad
    format. Ensure that your data is formatted correctly before running
    the tool.
-   **Reference Dataset:** To achieve accurate annotation results, it is
    crucial to provide a reference dataset that closely matches your
    scRNA-seq data. The reference dataset should contain annotated cells
    from various cell types.
-   **pre-trained scANVI models:** The models are trained on the
    reference dataset for each level cell types and saved in a directory
    structure matching the annotation hierarchy.
-   **Output Formats:** The tool saves the annotated data in various
    formats, depending on the output name. By default, CSV format is
    used for output files, but you can specify other formats such as TXT
    or H5ad if desired.

**Let me know if you have any other questions!**
