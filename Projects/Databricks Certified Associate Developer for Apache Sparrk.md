[[Data Engineering]] [[spark]]
# Databricks Certified Associate Developer for Apache Spark
**certifies knowledge about Apache Spark architecture basics and using Spark DataFrames**
- offered in either Python or Scala
- take via online proctoring, webcam & mic monitored
- download software on your workstation
- Spark documentation is provided during the exam!

## Exam Structure
- 60 questions, 2 hours
- USD $200
- Certification *does not expire*, but only certifies for a specific version of Spark, e.g. v3.0
- 60 questions
	-  at least 70% score to pass (42 questions+ correct)
	
## Exam Themes
### Architecture
- 10 questions on conceptual architecture
	- definitions
	-  hierarchies
	-  Spark behavior predictions
-  7 questions on applied architecture (use cases)
	#### **Context and hierarchy of Spark components**
	-  Job
	-  stage
	-  task
	-  executor
	-  slot
	-  dataframe
	-  RDD
	-  Partitions
	#### **how Spark does certain things**
	-  fault tolerance
	-  lazy evaluation
	-  adaptive query execution
	-  actions vs. transformations
	-  narrow vs. wide transformations
	-  shuffling
	-  broadcast and accumulator variables
	-  storage levels
	#### **Spark deployment**
	-  deployment modes
	-  location of Spark driver vs. executors
	-  purpose of deployment components, Spark configuration options

### Spark Coding
- 43 questions on Spark DataFrame API
	- select appropriate code block
	- find error in code block
	- fill in blanks
	- order code blocks
	- **reading and understanding documentation, including type hints**
		- selecting, filtering, dropping, sorting, renaming
		- column syntax: when to use strings vs. col()
	- **how to read and write DataFrames to and from disk**
	- **joins, UDFs, aggregations, partitioning**


## How to prepare:
1. Learn the concepts
	- 2 books:
		- [Spark: the Definitive Guide](https://pages.databricks.com/definitive-guide-Spark.html)
			- esp. sections 1, 2, and 4
		- [Learning Spark, 2nd Edition](https://databricks.com/p/ebook/learning-Spark-from-oreilly)
			- esp. chapters 1-7
2. [Use the DataFrame API](https://community.cloud.databricks.com/)
	- practice for free!
3. Do practice exams
	- Carefully review wrong answers, reading & understanding explainations
	- [Practice exam here](https://files.training.databricks.com/assessments/practice-exams/PracticeExam-DCADAS3-Python.pdf)
	- another practice exam available on Databricks Academy