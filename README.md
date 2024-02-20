**scAnnot User Documentation**
================

<!-- WARNING: THIS FILE WAS AUTOGENERATED! DO NOT EDIT! -->

> scAnnot is a tool for performing hierarchical multi-level annotation
> of single-cell datasets using pre-trained SCANVI models.
>
> This document will guide you through the installation process, explain
> the workflow and the usage of the tool, and provide additional info.

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

    scAnnot test.h5ad --reference ref.h5ad --output test.csv

**2. output the anndata with predicted lables in h5ad**

    scAnnot test.h5ad --reference ref.h5ad --output test.h5ad

### In jupyter notebook

    ad=scAnnot('test.h5ad', 'ref.h5ad')

    #show umap from the latent space
    ad=scAnnot('test.h5ad', 'ref.h5ad', show=True)

the UMAP plot for the level1 annotation:
![level1](https://github.com/rnacentre/scAnnot/blob/master/img/level1.png)

the UMAP plot for the original annotation:
![original](https://github.com/rnacentre/scAnnot/blob/master/img/original.png)

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

## References

For citation and further information please refer to the Research Square
preprint: Chen, X. et al. Brain Cell Atlas: An integrative ensemble of
cell transcriptomes across human brain regions. Research Square (2023)
doi:10.21203/rs.3.rs-3221500/v1.

**Let me know if you have any other questions!**
