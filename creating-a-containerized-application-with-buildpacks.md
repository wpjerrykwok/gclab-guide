# Creating a Containerized Application with Buildpacks

## Overview

Buildpacks are another approach for building container images and provide an alternate approach to turn your source code into a container image. 

Buildpacks are distributed and executed in images called builders. 

Each builder can have one or more buildpacks. 

A builder turns your source code into a container image. 

The buildpacks do the actual work to build and package the container image that you can deploy to Cloud Run or run with Docker locally.

You can create your own buildpacks, or use those provided by multiple vendors. 

Google Cloud's buildpacks allow developers to create and deploy containerized applications without the need to install Docker locally, or create a Dockerfile. 

Buildpacks are also built into Cloud Run to enable a source-based deployment workflow.

## Objectives

In this lab, you:

- Build an application with pack, a command-line tool that is used with builders to create container images from source code.
- Use the Google Cloud's buildpacks builder to build a container image.
- Run and test the container locally with Docker.
- Build and redeploy the container to Cloud Run.

## Task 1. Configure your environment and project

In this task, you set environment variables, and configure your Cloud Shell environment.

### Configure your Cloud Shell environment

To set your project ID and region environment variables, in Cloud Shell, run the following commands:

```bash
PROJECT_ID=$(gcloud config get-value project)
REGION=us-east4
```

Set the compute region in Cloud Shell:

```bash
gcloud config set compute/region $REGION
```

### Enable Google APIs

To use Cloud Run, and the Google Translate API later in this lab, enable relevant APIs for your project:

```bash
gcloud services enable artifactregistry.googleapis.com run.googleapis.com translate.googleapis.com
```

## Task 2. Build and run an application with Docker

In this task, you build a sample application with the `pack` command line tool and the Google Cloud's buildpacks builder.

### Develop the application

Create an `app` directory and change to it:

```bash
mkdir app && cd app
```

Copy the sample `python` application for this lab from Cloud Storage, and extract the contents from the archive:

```bash
gsutil cp gs://cloud-training/CBL513/sample-apps/sample-py-app.zip . && unzip sample-py-app
```

View the sample application files and source code:

```bash
ls sample-py-app
```

```bash
cat sample-py-app/main.py
```

> Because the Python buildpack does not generate a default container entry-point for the application, we use a Procfile to configure the application's start command.
The application is written in Python and returns a sample welcome message in response to a request made to the application.

### Build the container

Change to the sample application directory:

```bash
cd sample-py-app
```

To build the container, run `pack`:

```bash
pack build --builder=gcr.io/buildpacks/builder sample-py-app
```

A partial output is similar to:

```bash
...
...
[exporter] Setting default process type 'web'
[exporter] Saving sample-py-app...
[exporter] *** Images (9f9f9a48fd46):
[exporter]       sample-py-app
[exporter] Adding cache layer 'google.python.pip:pip'
[exporter] Adding cache layer 'google.python.pip:pipcache'
Successfully built image sample-py-app
```

> Note: With pack, you did not need to write and provide a Dockerfile to build the container image.

To view the images downloaded and built in your Cloud Shell host, run:

```bash
docker images
```

```bash
> REPOSITORY                  TAG       IMAGE ID       CREATED        SIZE
> gcr.io/buildpacks/builder   latest    514fb6f1bbfe   29 hours ago   804MB
> gcr.io/buildpacks/gcp/run   v1        22db1b5e48e3   29 hours ago   177MB
> buildpacksio/lifecycle      0.16.0    67e021546a3f   43 years ago   30.5MB
> sample-py-app               latest    9f9f9a48fd46   43 years ago   571MB
```

Run the container locally in Docker by passing in the PORT environment variable to the application and binding the host's port 8080 to the container port:

```bash
docker run -it -e PORT=8080 -p 8080:8080 -d sample-py-app
```

> The application code listens on the port that is provided in the environment variable, in this case, port 8080.

Test the containerized application with the curl command:

```bash
curl http://localhost:8080/
```

You should see the following message as a response:

```bash
> Welcome to this sample app, built with Buildpacks.
```

## Task 3. Build and run an application on Cloud Run

Typically, as a next step in your development and deployment lifecycle, you should push the container image that you built in the previous task to Artifact Registry, and then deploy the image to a container-based environment like Google Kubernetes Engine or Cloud Run.

In this task, you modify the sample application code, then build and deploy the containerized application directly from source with Cloud Run.

### Modify the application code

You modify the sample application code to use the Google Translation API that translates a piece of text from English to Spanish.

Edit the `main.py` file with an editor of your choice, for example, **vi** or **nano**. You can also click **Open Editor** from the Cloud Shell menu to edit the file.

Replace the entire contents of the `main.py` file with the code below:

```py
from flask import Flask, request
import google.auth
from google.cloud import translate

app = Flask(__name__)
_, PROJECT_ID = google.auth.default()
TRANSLATE = translate.TranslationServiceClient()
PARENT = 'projects/{}'.format(PROJECT_ID)
SOURCE, TARGET = ('en', 'English'), ('es', 'Spanish')

@app.route('/', methods=['GET', 'POST'])
def index():
    # reset all variables
    text = translated = None

    if request.method == 'POST':
        text = request.get_json().get('text').strip()
        if text:
            data = {
                'contents': [text],
                'parent': PARENT,
                'target_language_code': TARGET[0],
            }
            # handle older call for backwards-compatibility
            try:
                rsp = TRANSLATE.translate_text(request=data)
            except TypeError:
                rsp = TRANSLATE.translate_text(**data)
            translated = rsp.translations[0].translated_text

    # create context
    context = {
        'trtext': translated
    }
    return context

if __name__ == "__main__":
    # Dev only: run "python main.py" and open http://localhost:8080
    import os
    app.run(host="localhost", port=int(os.environ.get('PORT', 8080)), debug=True)
```

> The application code uses the Google Translate API to translate a piece of text passed in a JSON request from English to Spanish.

### Build and deploy the container

To build and deploy the container on Cloud Run, execute the following command:

```bash
gcloud run deploy sample-py-app --source . --region=${REGION} --allow-unauthenticated
```

> The `allow-unauthenticated` option enables access to the service without requiring any authentication.

When prompted, type Y to accept the default repository that is created in Artifact Registry to store the container image.

When the command completes, a Cloud Run service named `sample-py-app` is created.

The command output is similar to:

```bash
Building using Buildpacks and deploying container to Cloud Run service [sample-py-app] in project [qwiklabs-gcp-00-0d56d42aca1a] region [asia-east1]
OK Building and deploying new service... Done.                                                           
OK Creating Container Repository...
OK Uploading sources...
OK Building Container... Logs are available at [https://console.cloud.google.com/cloud-build/builds/8bea2ded-4745-41f9-a82d-128e409daa20?project=34240880885].
OK Creating Revision...                  
OK Routing traffic...
OK Setting IAM Policy...
Done.
Service [sample-py-app] revision [sample-py-app-00001-nec] has been deployed and is serving 100 percent of traffic.
Service URL: https://sample-py-app-ulvp7xw3bq-de.a.run.app
```

### Test the Cloud Run service

Set an environment variable for the Cloud Run service that was created in the previous step:

```bash
SERVICE_URL=[SERVICE URL]
```

> Replace the [SERVICE URL] with the value returned from Cloud Run in the output of the command in the previous step.

To test the service, , and execute the `curl` command:

```bash
curl $SERVICE_URL -H 'Content-Type: application/json' -d '{"text" : "Welcome to this sample app, built with Google Cloud buildpacks."}'
```

```bash
{"trtext":"Bienvenido a esta aplicaci\u00f3n de muestra, creada con paquetes de compilaci\u00f3n de Google Cloud."}
```

## Congratulations

In this lab, you built an application with the `pack` command, and the Google Cloud's buildpacks builder to build a container image for a sample python application. 

You first ran and tested the container locally with Docker before rebuilding and deploying the container to Cloud Run.
