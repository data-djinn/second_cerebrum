[[Data Engineering]] [[kedro]] [[Python]]

### What are data pipelines:
- any collection of ordered transformations on data

### Why do they turn out bad?
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


![[Pasted image 20220223120800.png]]

# Intro to Kedro
## What is Kedro
- open source framework for ML & data practitioners 
- follows[The Twelve-Factor App (12factor.net)](https://12factor.net/) philosophy encouraging:
  - Modularity
  - Separation of concerns & versioning

## Why
- address shortcomings of Jupyter Notebook and glue-code with a focus on creating maintainable data science code
- like "django" for ML projects

## Components of Kedro
### project template (inspired by cookiecutter data science)
- facilitates onboarding

### Data catalog
- core declarative IO abstraction layer

### Nodes + pipeline
- constructs which enable data-centric workflows (not task-centric)
- uses python classes (create your own!)

### Extensibility
- Inherit,
- hook in
  - run metrics
  - timeit
-  or plug-in other components (MLFlow, Dolt)

![[Pasted image 20220223121819.png]]

### Example
![[Pasted image 20220223122426.png]]

```yaml
# catalog.yml

companies:
  type: pandas.CSVDataSet
  filepath: data/01_raw/companies.csv
  layer: raw

shuttles:
  type: pandas.ExcelDataSet
  filepath: s3://my_bucket/01_raw/shuttles.xlsx
  layer: row
```

- seperation of concerns - business logic & config
- kedro uses fsspec behind the scenes
- supports s3/GCP/Azure/sFTP

```python
# nodes.py

def _parce_pct(col: pd.Series) -> pd.Series
  ...
  
def pre_process_companies(df: pd.Series) -> pd.DataFrame
```

- collection of nodes is passed to a pipeline :
![[Pasted image 20220223122955.png]]
- order is not important, as long as Model input table is made at the end


