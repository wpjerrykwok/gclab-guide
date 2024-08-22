# Dataproc: Qwik Start - Command Line

## GSP104

### Overview

 Dataproc is a fast, easy-to-use, fully-managed cloud service for running Apache Spark and Apache Hadoop clusters in a simpler, more cost-efficient way. Operations that used to take hours or days take seconds or minutes instead. Create Dataproc clusters quickly and resize them at any time, so you don't have to worry about your data pipelines outgrowing your clusters.

This lab shows you how to use the command line to create a Dataproc cluster, run a simple Apache Spark job in the cluster, and then modify the number of workers in the cluster.

What you'll do In this lab, you learn how to:

Create a Dataproc cluster using the command line Run a simple Apache Spark job Modify the number of workers in the cluster Setup and requirements

Task 1. Create a cluster In Cloud Shell, run the following command to set the Region: gcloud config set dataproc/region Region Copied! Dataproc creates staging and temp buckets that are shared among clusters in the same region. Since we're not specifying an account for Dataproc to use, it will use the Compute Engine default service account, which doesn't have storage bucket permissions by default. Let's add those. First, run the following commands to grab the PROJECT_ID and PROJECT_NUMBER: PROJECT_ID=$(gcloud config get-value project) &&
gcloud config set project $PROJECT_ID

PROJECT_NUMBER=$(gcloud projects describe $PROJECT_ID --format='value(projectNumber)') Copied! Now run the following command to give the Storage Admin role to the Compute Engine default service account: gcloud projects add-iam-policy-binding $PROJECT_ID
--member=serviceAccount:$PROJECT_NUMBER-compute@developer.gserviceaccount.com
--role=roles/storage.admin Copied! Run the following command to create a cluster called example-cluster with e2-standard-4 VMs and default Cloud Dataproc settings: gcloud dataproc clusters create example-cluster --worker-boot-disk-size 500 --worker-machine-type=e2-standard-4 --master-machine-type=e2-standard-4 Copied! If asked to confirm a zone for your cluster. Enter Y. Your cluster will build for a couple of minutes.

Waiting for cluster creation operation...done. Created [... example-cluster] When you see a "Created" message, you're ready to move on.

Test completed task Click Check my progress to verify your performed task. If you have successfully created a Dataproc cluster, you will see an assessment score.

Create a Dataproc cluster Task 2. Submit a job Run this command to submit a sample Spark job that calculates a rough value for pi: gcloud dataproc jobs submit spark --cluster example-cluster
--class org.apache.spark.examples.SparkPi
--jars file:///usr/lib/spark/examples/jars/spark-examples.jar -- 1000 Copied! The command specifies:

That you want to run a spark job on the example-cluster cluster The class containing the main method for the job's pi-calculating application The location of the jar file containing your job's code The parameters you want to pass to the job—in this case, the number of tasks, which is 1000 Note: Parameters passed to the job must follow a double dash (--). See the gcloud documentation for more information. The job's running and final output is displayed in the terminal window:

Waiting for job output... ... Pi is roughly 3.14118528 ... state: FINISHED Test completed task Click Check my progress to verify your performed task. If you have successfully submitted a job, you will see an assessment score.

Submit a job Task 3. Update a cluster To change the number of workers in the cluster to four, run the following command: gcloud dataproc clusters update example-cluster --num-workers 4 Copied! Your cluster's updated details are displayed in the command's output:

Waiting on operation [projects/qwiklabs-gcp-7f7aa0829e65200f/regions/global/operations/b86892cc-e71d-4e7b-aa5e-6030c945ea67]. Waiting for cluster update operation...done. You can use the same command to decrease the number of worker nodes: gcloud dataproc clusters update example-cluster --num-workers 2 Copied! Now you can create a Dataproc cluster and adjust the number of workers from the gcloud command line on the Google Cloud.

