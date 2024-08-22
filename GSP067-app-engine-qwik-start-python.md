# App Engine: Qwik Start - Python

## GSP067

## Overview

App Engine allows developers to focus on doing what they do best, writing code, and not what it runs on. 

Developers upload their apps to App Engine, and Google Cloud takes care of the rest. 

The notion of servers, virtual machines, and instances have been abstracted away, with App Engine providing all the compute necessary. 

Developers don't have to worry about operating systems, web servers, logging, monitoring, load-balancing, system administration, or scaling, as App Engine takes care of all that. 

Developers only need to focus on building solutions for their organizations or their users.

The App Engine standard environment provides application-hosting services supporting the following languages: Python, Java, PHP, Go, Node.js, and Ruby. 

The App Engine flexible environment provides even more flexibility by supporting custom runtimes, however it is out-of-scope for this lab.

App Engine is Google Cloud's original serverless runtime, and since its original launch in 2008, has been joined by:

- Cloud Functions, great for situations where you don't have an entire app, have broken up a larger, monolithic app into multiple microservices, or have short event-driven tasks that execute based on user activity.

- Cloud Run, the serverless container-hosting service similar to App Engine but more accurately reflects the state of software development today.

In this lab, you'll learn how to deploy a basic app to App Engine, but we invite you to also explore Cloud Functions and Cloud Run. 

App Engine makes it easy to build and deploy an application that runs reliably even under heavy load and with large amounts of data. (Cloud Functions and Cloud Run do the same.)

App Engine apps can access numerous additional Cloud or other Google services for use in their applications:

- NoSQL database: Cloud Datastore, Cloud Firestore, Cloud BigTable
- Relational database: Cloud SQL or Cloud AlloyDB, Cloud Spanner
- File/object storage: Cloud Storage, Cloud Filestore, Google Drive
- Caching: Cloud Memorystore (Redis or memcached)
- Task execution: Cloud Tasks, Cloud Pub/Sub, Cloud Scheduler, Cloud Workflows
- User authentication: Cloud Identity Platform, Firebase Auth, Google Identity Services

Applications run in a secure, sandboxed environment, allowing App Engine standard environment to distribute requests across multiple servers, and scaling servers to meet traffic demands. 

Your application runs within its own secure, reliable environment that is independent of the hardware, operating system, or physical location of the server.

This hands-on lab shows you how to create a small App Engine application that displays a short message.

### What you'll learn

In this lab you'll do the following with a Python app:

- Clone/download

- Test

- Update

- Test

- Deploy

## Setup and requirement

```bash
gcloud config set compute/region us-west1
```

## Task 1. Enable Google App Engine Admin API

The App Engine Admin API enables developers to provision and manage their App Engine Applications.

In the left **Navigation menu**, click **APIs & Services** > **Library**.

Type "App Engine Admin API" in the search box.

Click the **App Engine Admin API** card.

Click **Enable**. If there is no prompt to enable the API, then it is already enabled and no action is needed.

## Task 2. Download the Hello World app

There is a simple Hello World app for Python you can use to quickly get a feel for deploying an app to Google Cloud. 

Follow these steps to download Hello World to your Google Cloud instance.

Enter the following command to copy the Hello World sample app repository to your Google Cloud instance:

```bash
git clone https://github.com/GoogleCloudPlatform/python-docs-samples.git
```

Go to the directory that contains the sample code:

```bash
cd python-docs-samples/appengine/standard_python3/hello_world
```

Setup python environment:

```bash
sudo apt install python3 -y
sudo apt install python3.11-venv
python3 -m venv create myvenv
source myvenv/bin/activate
```

## Task 3. Test the application

Test the application using the Google Cloud development server (`dev_appserver.py`), which is included with the preinstalled App Engine SDK.

From within your helloworld directory where the app's app.yaml configuration file is located, start the Google Cloud development server with the following command:

```bash
dev_appserver.py app.yaml
```

The development server is now running and listening for requests on port 8080.

View the results by clicking the **Web preview** > **Preview on port 8080**.

You'll see this in a new browser window:

## Task 4. Make a change

You can leave the development server running while you develop your application. 

The development server watches for changes in your source files and reloads them if necessary.

Let's try it. 

Leave the development server running. 

We'll open another command line window, then edit `main.py` to change "Hello World!" to "Hello, Cruel World!".

Click the (**+**) next to your Cloud Shell tab to open a new command line session.

Enter this command to go to the directory that contains the sample code:

```bash
cd python-docs-samples/appengine/standard_python3/hello_world
```

Enter the following to open main.py in nano to edit the content:

```bash
nano main.py
```

Change "Hello World!" to "Hello, Cruel World!".

Save the file with CTRL-S and exit with CTRL-X.

Reload the Hello World! Browser or click the **Web Preview** (web preview icon) > **Preview on port 8080** to see the results.

## Task 5. Deploy your app

To deploy your app to App Engine, run the following command from within the root directory of your application where the app.yaml file is located:

```bash
gcloud app deploy
```

Enter the number that represents your region: `us-west1`

The App Engine application will then be created.

Example output:

```bash
Creating App Engine application in project [qwiklabs-gcp-233dca09c0ab577b] and region [us-west1]....done.
Services to deploy:

descriptor:      [/home/gcpstaging8134_student/python-docs-samples/appengine/standard/hello_world/app.yaml]
source:          [/home/gcpstaging8134_student/python-docs-samples/appengine/standard/hello_world]
target project:  [qwiklabs-gcp-233dca09c0ab577b]
target service:  [default]
target version:  [20171117t072143]
target url:      [https://qwiklabs-gcp-233dca09c0ab577b.appspot.com]

Do you want to continue (Y/n)?
```

Enter **Y** when prompted to confirm the details and begin the deployment of service.

Example output:

```bash
Beginning deployment of service [default]...
Some files were skipped. Pass `--verbosity=info` to see which ones.
You may also view the gcloud log file, found at
[/tmp/tmp.dYC7xGu3oZ/logs/2017.11.17/07.18.27.372768.log].
╔════════════════════════════════════════════════════════════╗
╠═ Uploading 5 files to Google Cloud Storage                ═╣
╚════════════════════════════════════════════════════════════File upload done.
Updating service [default]...done.
Waiting for operation [apps/qwiklabs-gcp-233dca09c0ab577b/operations/2e88ab76-33dc-4aed-93c4-fdd944a95ccf] to complete...done.
Updating service [default]...done.
Deployed service [default] to [https://qwiklabs-gcp-233dca09c0ab577b.appspot.com]

You can stream logs from the command line by running:
  $ gcloud app logs tail -s default

To view your application in the web browser run:
  $ gcloud app browse
```

> Note: If you receive an error as "Unable to retrieve P4SA" while deploying the app, then re-run the above command.

## Task 6. View your application

To launch your browser enter the following command, then click on the link it provides:

```bash
gcloud app browse
```

Example output (note that your link will be different):

```bash
Did not detect your browser. Go to this link to view your app:
https://qwiklabs-gcp-233dca09c0ab577b.appspot.com
```

Your application is deployed and you can read the short message in your browser.

## Congratulations
