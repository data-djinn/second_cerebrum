[[Data Engineering]] [[kedro]] [[Python]]

### What are data pipelines:
- any collection of ordered transformations on data

### Why do they torn out bad?
- **hard-coded file paths and parameters**
- incomprehensible transformations
- untraceability

### Basic structure of Kedro:
- Nodes
    - pure transformation functions
- Datasets
    - Standardized input and output functions
- Pipelines
    - stitches nodes and datasets together