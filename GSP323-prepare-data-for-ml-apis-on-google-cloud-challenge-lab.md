Prepare Data for ML APIs on Google Cloud: Challenge Lab GSP323 Overview In a challenge lab you’re given a scenario and a set of tasks. Instead of following step-by-step instructions, you will use the skills learned from the labs in the course to figure out how to complete the tasks on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

When you take a challenge lab, you will not be taught new Google Cloud concepts. You are expected to extend your learned skills, like changing default values and reading and researching error messages to fix your own mistakes.

To score 100% you must successfully complete all tasks within the time period!

This lab is recommended for students who have enrolled in the Prepare Data for ML APIs on Google Cloud skill badge. Are you ready for the challenge?

Topics tested:

Create a simple Dataproc job Create a simple DataFlow job Perform two Google machine learning backed API tasks Challenge scenario As a junior data engineer in Jooli Inc. and recently trained with Google Cloud and a number of data services you have been asked to demonstrate your newly learned skills. The team has asked you to complete the following tasks.

You are expected to have the skills and knowledge for these tasks so don’t expect step-by-step guides. Task 1. Run a simple Dataflow job In this task, you use the Dataflow batch template Text Files on Cloud Storage to BigQuery under "Process Data in Bulk (batch)" to transfer data from a Cloud Storage bucket (gs://cloud-training/gsp323/lab.csv). The following table has the values you need to correctly configure the Dataflow job.

You will need to make sure you have:

Create a BigQuery dataset called lab_955 with a table called customers_275. Create a Cloud Storage Bucket called qwiklabs-gcp-02-7be1ee7806c0-marking. Field Value Cloud Storage input file(s) gs://cloud-training/gsp323/lab.csv Cloud Storage location of your BigQuery schema file gs://cloud-training/gsp323/lab.schema BigQuery output table qwiklabs-gcp-02-7be1ee7806c0:lab_955.customers_275 Temporary directory for BigQuery loading process gs://qwiklabs-gcp-02-7be1ee7806c0-marking/bigquery_temp Temporary location gs://qwiklabs-gcp-02-7be1ee7806c0-marking/temp Optional Parameters > JavaScript UDF path in Cloud Storage gs://cloud-training/gsp323/lab.js Optional Parameters > JavaScript UDF name transform Optional Parameters > Machine Type e2-standard-2 Wait for the job to finish before trying to check your progress. Task 2. Run a simple Dataproc job In this task, you run an example Spark job using Dataproc.

Before you run the job, log into one of the cluster nodes and copy the /data.txt file into hdfs (use the command hdfs dfs -cp gs://cloud-training/gsp323/data.txt /data.txt). Run a Dataproc job using the values below.

Field Value Region us-east1 Job type Spark Main class or jar org.apache.spark.examples.SparkPageRank Jar files file:///usr/lib/spark/examples/jars/spark-examples.jar Arguments /data.txt Max restarts per hour 1 Dataproc Cluster Compute Engine Region us-east1 Machine Series E2 Manager Node Set Machine Type to e2-standard-2 Worker Node Set Machine Type to e2-standard-2 Max Worker Nodes 2 Primary disk size 100 GB Internal IP only Deselect "Configure all instances to have only internal IP addresses Wait for the job to finish before trying to check your progress. Task 3. Use the Google Cloud Speech-to-Text API Use Google Cloud Speech-to-Text API to analyze the audio file gs://cloud-training/gsp323/task3.flac. Once you have analyzed the file, upload the resulting file to: gs://qwiklabs-gcp-02-7be1ee7806c0-marking/task3-gcs-650.result

Task 4. Use the Cloud Natural Language API Use the Cloud Natural Language API to analyze the sentence from text about Odin. The text you need to analyze is "Old Norse texts portray Odin as one-eyed and long-bearded, frequently wielding a spear named Gungnir and wearing a cloak and a broad hat." Once you have analyzed the text, upload the resulting file to: gs://qwiklabs-gcp-02-7be1ee7806c0-marking/task4-cnl-657.result