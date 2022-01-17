[[Azure (WIP)]] [[Cloud]] [[Data Engineering]] [[spark]]

# Uses Apache Spark pools
- parallel processing framework for that supports in-memory processing to boost the performance of big-data analytics applications
- Spark is the core engine that is managed by the YARN (Yet Another Resource Negotiator) layer to ensure proper use of the distributed engine to process the Spark queries and jobs
- 

## Benefits
-   **Speed and Efficiency**: There is a quick start-up time for nodes and automatic shut-down when instances are not used within 5 minutes after the last job, unless there is a live notebook connection.
-   **Ease of creation**: Creating as Apache Spark pool can be done through the Azure portal, PowerShell, or .NET SDK for Azure Synapse Analytics.
-   **Ease of use**: Within the Azure Synapse Analytics workspace, you can connect directly to the Apache Spark pool and interact with the integrated notebook experience, or use custom notebooks derived from Nteract. Notebook integration helps you develop interactive data processing and visualization pipelines.
-   **REST APIs**: In order to monitor and submit jobs remotely, you can use Apache Livy as a Rest API for the Spark job server. Apache Livy is a service that enables interaction with an Spark cluster over a REST interface to enable submission of Spark jobs, snippets of Spark code, synchronous or asynchronous result retrieval, and Spark Context management.
-   **Integration with third-party Integrated Development Environments (IDEs)**: Azure Synapse Analytics provides an IDE for IntelliJ to create and submit applications to the Apache Spark pool
-   **Pre-loaded Anaconda libraries**: Over 200 Anaconda libraries are pre-installed on the Spark pool in Azure Synapse Analytics.
-   **Scalability**: Possibility for autoscale, so that pools can be scaled up/down as required, by adding or removing nodes.