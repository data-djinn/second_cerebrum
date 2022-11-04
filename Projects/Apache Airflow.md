[[Data Engineering]] [[Python]]
# DAG: Directed Acyclic Graph
==series of tasks that you want to run as part of your workflow==
- Set of nodes connected by:
	- Directed edges
	- No cycles 
  - Meaning: no path following the directed edges sees a specific node more than once
  - specify the relationship between the tasks, any dependencies, and the order in which the tasks should be run
- written in `python` and saved as a `.py` file
- **DAG_ID** is used extensively by the tool to orchestrate the DAG run

### DAG runs
- specify when a DAG should run automatically va an **`execution_date`**:
	- specified schedue defined by a CRON expression
	- could be daily, weekly, every minute/hour, or any other interval
	  
### Operators
- an operator encapulates the operation to be performed in each task in a DAG
- some are platform-specific
- create your own custom operators!
- 

##### Crontab notation: every hour at minute N would be `N * * * *`
##### Install tips
- requires `Flask`
- good idea to 
## Example pipeline ![[Pasted image 20220421010004.png]]
## how to schedule?
- manually
- cron scheduling tool
- What about dependencies?
```python
# Create the DAG object
dag = DAG(dag_id="car_factory_simulation",
          default_args={"owner": "airflow","start_date": airflow.utils.dates.days_ago(2)},
          schedule_interval="0 * * * *")

# Task definitions
assemble_frame = BashOperator(task_id="assemble_frame", bash_command='echo "Assembling frame"', dag=dag)
place_tires = BashOperator(task_id="place_tires", bash_command='echo "Placing tires"', dag=dag)
assemble_body = BashOperator(task_id="assemble_body", bash_command='echo "Assembling body"', dag=dag)
apply_paint = BashOperator(task_id="apply_paint", bash_command='echo "Applying paint"', dag=dag)

# Complete the downstream flow
assemble_frame.set_downstream(place_tires)
assemble_frame.set_downstream(assemble_body)
assemble_body.set_downstream(apply_paint)
```

## Workflow
==a set of steps to accomplish a given data engineernig task, such as: downloading files, copying data, filtering information, writing to a database, etc.==
- varying levels of complexitites
- some are 2 steps, some are 100s
download sales data -> clean sales data -> run ML pipeline -> upload results to webserver -> Generate report and email to CEO


## Airflow is a platform to manage workflows:
- can implement programs from any language, but workflows are written in Python
- accessed via code, via command-line, or via web interface
other tools:
- Luigi
- SSIS
- Bash scripts
##### **Implements workflows as DAGs: Directed Acylcic Graphs**
- consists of tasks & dependencies between tasks
- created with various details about the DAG, including the name, start date, owner, etc.
```
etl_dag = DAG(
    dag_id='etl_pipeline', default args={"start_date":"2020-01-08"}
)
```
##### run a task `airflow run etl_pipeline example_task_id 2022-03-14`
### Creation

### Scheduling
### Monitoring
- 