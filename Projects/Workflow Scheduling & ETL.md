[[Data Engineering]]
![[Pasted image 20211023121341.png]]
# DAG = Directed Acyclical Graph
- set of nodes connected by
    - directed edges
    - no cycles
        - i.e., no path following the directed edges sees a specific node more than once
![[Pasted image 20211023121447.png]]

![[Pasted image 20211023121557.png]]

## option 1: use linux chron jobs

## option 2: Airflow example
```
# Create the DAG object
dag = DAG(dag_id="example_dag", ..., schedule_interval="0 * * * *") # chrontab notation for "very hour"

# Define operations
start_cluster = StartClusterOperator(task_id="start_cluster", dag=dag)
ingest_customer_data = SparkJobOperator(task_id="ingest_customer_data", dag=dag)
ingest_product_data = SparkJobOperator(task_id="ingest_product_data", dag=dag)
entrich_customer_data = PythonOperator(task_id="enrich_customer_data",..., dag=dag)

# Set up dependency flow
start_cluster.set_downstream(ingest_customer_data)
ingest_customer_data.set_downstream(enrich_customer_data)
ingest_product_data.set_downstream(enrich_customer_data)

