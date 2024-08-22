# App Engine: 3 Ways: Challenge Lab

## ARC112

## Overview

In a challenge lab you’re given a scenario and a set of tasks. 

Instead of following step-by-step instructions, you will use the skills learned from the labs in the course to figure out how to complete the tasks on your own! 

## Challenge scenario

You're a junior cloud engineer who is just starting out in your career. 

So far you have been helping teams create and manage Google Cloud resources.

You are expected to have the skills and knowledge for these tasks.

### Your challenge

For this challenge, you are asked to use Google App Engine to deploy a basic web application in your choice of language (either Python, PHP, or Golang/Go).

You need to:

- Download an application

- Deploy the application

- Update the code and redeploy the application

## Task 1. Enable the Google App Engine Admin API

Enable the Google App Engine Admin API for the project to provision and manage the App Engine application.

```bash
gcloud services enable appengine.googleapis.com
```

## Task 2. Download the Hello World app

Download a simple Hello World app from the relevant repository according to your choice of language for deploying the web application on the VM instance `lab-setup` at the `$HOME` directory (~/).

Language|Repository
Python|https://github.com/GoogleCloudPlatform/python-docs-samples.git
PHP|https://github.com/GoogleCloudPlatform/php-docs-samples.git
Golang/Go|https://github.com/GoogleCloudPlatform/golang-samples.git

Go to the directory that contains the helloworld sample code.

In the Cloud console, go to **Compute Engine** > **VM instance**. 

Click **SSH** on `lab-setup`.

In **SSH**:

```bash
git clone https://github.com/GoogleCloudPlatform/php-docs-samples.git
```

## Task 3. Deploy your application

For this task, you need to deploy the `helloworld` app to the Google App Engine in the `us-east4` region.

Navigate from the root directory to where your application's `app.yaml` file is located.

> Note: Deploy the application to the App Engine standard environment.

In Cloud Shell

```bash
git clone https://github.com/GoogleCloudPlatform/php-docs-samples.git
```

```bash
cd php-docs-samples/appengine/standard/helloworld
```

```bash
gcloud app deploy
```

Choose the region accordingly.

### View your application

To launch and view the application in your browser, enter the following command:

```bash
gcloud app browse
```

Click on the link that is provided. Your application is deployed and you can read the default message in your browser.

## Task 4. Deploy updates to your application

Update your application code to change the default message of `Hello, World!` to `Hello, Cruel World!` and redeploy your application.

```bash
nano index.php
```

Update the message. Save and Close. Redeploy the app.

```bash
gcloud app deploy
```

## Congratulations

You have successfully deployed a basic Hello World web application to the Google App Engine.
