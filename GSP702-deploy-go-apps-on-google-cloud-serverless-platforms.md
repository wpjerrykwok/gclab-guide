# Deploy Go Apps on Google Cloud Serverless Platforms

## GSP702

### Overview

Go is an open source programming language by Google that makes it easy to build fast, reliable, and efficient software at scale. 

In this lab you explore the basics of Go by deploying a simple Go app to Cloud Run, Cloud Functions, and App Engine. 

You then use the Go app to access data in BigQuery and Firestore.

#### What you'll do

In this lab, you perform the following:

- Set up your Firestore Database and import data
- Get an introduction to the power of Cloud Build
- Explore data in BigQuery and Firestore
- Deploy a Go app to App Engine, Cloud Run, and Cloud Functions
- Examine the Go app code
- Test the app on the each of the platforms

### What is Go?

Go (golang) is a general-purpose language designed with systems programming in mind. 

It is strongly typed and garbage-collected and has first class support for concurrent programming.

Programs are constructed from packages, whose properties allow efficient management of dependencies.

Unlike Python and Javascript, Go is compiled not interpreted. 

Go source code is compiled into machine code before execution. 

As a result, Go is typically faster and more efficient than interpreted languages and requires no installed runtime like Node, Python, or JDK to execute.

### Serverless platforms

Serverless computing enables developers to focus on writing code without worrying about infrastructure. 

It offers a variety of benefits over traditional computing, including zero server management, no up-front provisioning, auto-scaling, and paying only for the resources used. 

These advantages make serverless ideal for use cases like stateless HTTP applications, web, mobile, IoT backends, batch and stream data processing, chatbots, and more.

Go is perfect for cloud applications because of its efficiency, portability, and how easy it is to learn. 

This lab shows you how to use Cloud Build to deploy a Go app to the Cloud Run, Cloud Functions, and App Engine, all Google serverless platforms:

- Cloud Run
- Cloud Functions
- App Engine

> Note: Refer to Serverless Options for information on which serverless platform is best adapted for your needs.

#### Cloud Build

Cloud Build is a service that executes your builds on Google Cloud infrastructure. 

Cloud Build can import source code from Cloud Storage, Cloud Source Repositories, GitHub, or Bitbucket, execute a build to your specifications, and produce artifacts such as Docker containers.

Cloud Build executes your build as a series of build steps, where each build step is run in a Docker container. 

A build step can do anything that a container can do irrespective of the environment. 

For more information, refer to the Cloud Build documentation.

#### Cloud Run

Cloud Run is a fully managed compute platform that automatically scales your stateless containers.

Cloud Run is serverless; it abstracts away all infrastructure management, so you can focus on what matters most—building great applications.

##### Architecture

When you deploy your sample Go app, Google Cloud Data Drive, to Cloud Run, the data flow architecture looks like this:

Go Web App on Cloud Run architecture diagram

#### Cloud Functions

Cloud Functions is Google Cloud's event-driven serverless compute platform. 

Because Go compiles to a machine code binary, has no dependent frameworks to start, and does not require an interpreter, Go cold start time is between 80 and 1400ms. 

This means that the applications and services can remain dormant without costing anything, and then startup and start cold serving traffic in 1400ms or less. 

After this initial start, the function serves traffic instantly. 

This factor alone makes Go an ideal language for a Cloud Functions deployment.

##### Architecture

When you deploy your sample Go app, Google Cloud Data Drive, to Cloud Functions, the data flow architecture looks like this:

Go Web App on Cloud Run architecture diagram

#### App Engine

The App Engine standard environment is based on container instances running on Google's infrastructure. 

Containers are preconfigured with one of several available runtimes.

##### Architecture

When you deploy your sample Go app, Google Cloud Data Drive, to App Engine, the data flow architecture looks like this:

Go Serverless architecture diagram

### Google Cloud Data Drive source code

In this lab you deploy a simple app called Google Cloud Data Drive. 

Google developed and open sourced this app to extract data from Google Cloud quickly. 

The Google Cloud Data Drive app is one of a series of tools that demonstrates effective usage patterns for using Cloud API's and services.

The Google Cloud Data Drive Go app exposes a simple composable URL path to retrieve data in JSON format from supported Google Cloud data platforms. 

The application currently supports BigQuery and Firestore, but has a modular design that can support any number of data sources.

These HTTP URL patterns are used to get data from Google Cloud running Google Cloud Data Drive.

- Firestore : `[SERVICE_URL]/fs/[PROJECT_ID]/[COLLECTION]/[DOCUMENT]`

- BigQuery: `[SERVICE_URL]/bq/[PROJECT_ID]/[DATASET]/[TABLE]`

Where:

- `[SERVICE URL]`: The base URL for the application from App Engine, Cloud Run, or Cloud Functions. For App Engine this will look like `https://[PROJECT ID].appspot.com/`.

- `[PROJECT ID]`: The Project ID of the Firestore Collection or BigQuery Dataset you want to access. You find the **Project ID** in the left panel of your lab.

- `[COLLECTION]`: The Firestore Collection ID (`symbols/product`)

- `[DOCUMENT]`: The Firestore document you would like to return (`symbol`)

- `[DATASET]`: The BigQuery Dataset name (`publicviews`)

- `[TABLE]`: The BigQuery Table name (`ca_zip_codes`)

> Note: Although this lab uses curl to test the app, you can create the URLs yourself for an added functionality test in a browser.

### Task 1. Setup your environment

In Cloud Shell, enter the following command to create an environment variable to store the **Project ID** to use later in this lab:

```bash
gcloud config set compute/region us-central1
export REGION=$(gcloud config get compute/region)
export PROJECT_ID=$(gcloud info --format="value(config.project)")
```

Get the sample code for this lab by copying from Google Cloud Storage (GCS):

```bash
mkdir DIY-Tools
gsutil cp -R gs://spls/gsp702/DIY-Tools/* DIY-Tools/
```

#### Prepare your databases

This lab uses sample data in BigQuery and Firestore to test your Go app.

##### BigQuery database

BigQuery is a serverless, future proof data warehouse with numerous features for machine learning, data partitioning, and segmentation. 

This lets you analyze gigabytes to petabytes of data using ANSI SQL at blazing-fast speeds, and with zero operational overhead.

The BigQuery dataset is a view of California zip codes and was created for you when the lab started.

##### Firestore database

Firestore, is a serverless document database, with super fast document lookup and real-time eventing features. 

It is also capable of a 99.999% SLA. 

To use data in Firestore to test your app, you must initialize Firestore in native mode and then import the sample data.

A Firestore native mode database instance has been created.

In the Cloud Console, click **Navigation menu** > **Firestore** to open Firestore in the Console.

Wait for the Firestore Database instance to initialize. 

This process also initializes App Engine in the same region, which allows you to deploy the application to App Engine without first creating an App Engine instance.

In Cloud Shell, launch a Firestore import job that provides sample Firestore data for the lab:

```bash
gcloud firestore import gs://$PROJECT_ID-firestore/prd-back
```

This import job loads a Cloud Firestore backup of a collection called `symbols` into the `$PROJECT_ID-firestore` storage bucket.

This import job takes up to 5 minutes to complete. Start the next section while you wait.

#### Configure permissions for Cloud Build

Cloud Build is a service that executes your builds on Google Cloud infrastructure. 

By default, Cloud Build does not have sufficient permissions to deploy applications to

- App Engine
- Cloud Run
- Cloud Functions

You must enable these services before you can use Cloud Build to deploy the Google Cloud Data Drive app.

In the Console, click **Navigation menu** > **Cloud Build** > **Settings**.

Set the **Status** for **Cloud Functions** to **Enable**.

When prompted, click **Grant access to All Service Accounts**.

Set the **Status** for **Cloud Run** to **Enable**.

Set the **Status** for **App Engine** to **Enable**.

### Task 2. Deploy to Cloud Run

Google Cloud Run is very similar to App Engine standard environment, except that Cloud Run allows you to bring your own container (BYOC). 

For the Google Cloud Data Drive app, you supply a Dockerfile that creates a Docker container, and then deploy that container to Cloud Run.

> Note: The same container can be hosted in Google Kubernetes Engine or any other platform that hosts Docker containers, even on-premises compute platforms.

#### Review the `Dockerfile`

Go is an excellent language to write apps for deployment on container platforms. 

The only item included in the container is the Go compiled binary. 

In the below example you can see we use a base container called `distroless/static`. 

This base container is built from scratch and contains only public certificate roots and timezone file information. 

The Go binary is injected and registered as a start command (see the last line of the `Dockerfile` below). 

There are no additional frameworks included or needed. 

This type of container construction creates the smallest container and security footprint possible and makes the container extremely portable to GKE, Anthos, and on premise hosting solutions.

Have a look at the `Dockerfile` code that builds the container below:

```dockerfile
# Use the official Golang image to create a build artifact.
# This is based on Debian and sets the GOPATH to /go.
# https://hub.docker.com/_/golang
FROM golang:1.21 as builder

# Create and change to the app directory.
WORKDIR /app

# Retrieve application dependencies.
# This allows the container build to reuse cached dependencies.
COPY go.* ./
RUN go mod download

# Copy local code to the container image.
COPY .  ./

WORKDIR /app/cmd/webserver
# Build the binary.
RUN GO111MODULE=on CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -mod=readonly -v -o gcp-data-drive

# Use the Google Distroless image for a minimal container.
FROM gcr.io/distroless/static

# Copy the binary to the production image from the builder stage.
COPY --from=builder /app/cmd/webserver/gcp-data-drive /gcp-data-drive

# Run the web service on container startup.
CMD ["/gcp-data-drive"]
```

View the Dockerfile on GitHub.

#### Review the Cloud Build YAML config file

The Cloud Build YAML file, `DIY-Tools/gcp-data-drive/cloudbuild_run.yaml`, shown below contains the Cloud Build step definitions that deploy your application to Cloud Run. You use this file to deploy the application to Cloud Run.

- The first step executes the `git` command to clone the source repository that contains the application. This step is parameterized to allow you to easily switch between application branches.

- The second step executes the `gcloud builds submit` command to use Cloud Build to create a container containing the compiled application using the steps contained in the `Dockerfile`. The container is then saved to `gcr.io`

- The third step deploys the application container image from `gcr.io` to Cloud Run in the us-central1 region.

You could build the container yourself and manually deploy this app using `gcloud run deploy`, but Cloud Build allows you to offload this to Google infrastructure. 

The Google Cloud Build system is a core component of an automated CI/CD system because it allows you to listen to commit events with tag filters.

These features come together to create an automated pipeline from developers committed to lifecycle deployment in a consistent and reliable way.

> Note: The comments have been removed for clarity.

```yaml
steps:
- name: 'gcr.io/cloud-builders/git'
  args: ['clone','--single-branch','--branch',
        '${_GIT_SOURCE_BRANCH}','${_GIT_SOURCE_URL}']

- name: 'gcr.io/cloud-builders/gcloud'and I args: ['builds','submit', '--tag','gcr.io/$PROJECT_ID/gcp-data-drive'] dir: 'DIY-Tools/gcp-data-drive'

- name: 'gcr.io/cloud-builders/gcloud' args: ['run','deploy','gcp-data-drive', '--image','gcr.io/$PROJECT_ID/gcp-data-drive', '--platform','managed', '--region','us-central1', '--allow-unauthenticated'] dir: 'DIY-Tools/gcp-data-drive' 
```

View the steps in cloudbuild_run.yaml on GitHub.

#### Check the import process

Look in Cloud Shell to see if the import process loading the data into Filestore has finished. 

When it completes, continue to the next section to deploy the Google Cloud Data Drive app.

#### Deploy the Google Cloud Data Drive app

In Cloud Shell, change to the directory for the application that you cloned from GitHub:

```bash
cd ~/DIY-Tools/gcp-data-drive
```

Deploy the Google Cloud Data Drive app to Cloud Run with Cloud Build:

```bash
gcloud builds submit --config cloudbuild_run.yaml \
  --project $PROJECT_ID --no-source \
  --substitutions=_GIT_SOURCE_BRANCH="master",_GIT_SOURCE_URL="https://github.com/GoogleCloudPlatform/DIY-Tools",_GCP_REGION="us-central1"
```

Enter the following command to allow unauthorized access to the Google Cloud Run services:

```bash
gcloud beta run services add-iam-policy-binding --region=us-central1 --member=allUsers --role=roles/run.invoker gcp-data-drive
```

The deployment takes a few minutes and you may see some red text in the console. These are not failure logs.

Store the Cloud Run service URL in an environment variable:

```bash
export CLOUD_RUN_SERVICE_URL=$(gcloud run services --platform managed describe gcp-data-drive --region us-central1 --format="value(status.url)")
```

Use `curl` to call the application to query data from the Firestore `symbols` collection in your project:

```bash
curl $CLOUD_RUN_SERVICE_URL/fs/$PROJECT_ID/symbols/product/symbol | jq .
```

> Note: If you get an authentication error, wait a minute and retry the previous curl command.

This responds with the contents of a JSON file containing the values from the symbols collection in your project, which looks similar to this:

```json
[
  {
    "asin": "B07DR9XYKB",
    "bbw": false,
    "brand": "",
    "category": "toy_display_on_website",
    "cpip": 1640,
    "docid": "887961768916",
    "fba": false,
    "fbafees": 610,
    "inStock": "NOW",
    "lastMatchLookup": "2020-03-13T13:00:47.040414Z",
    "lastOfferLookup": "2020-03-13T13:00:47.933649Z",
    "listPrice": 1599,
    "manufacturer": "Fisher-Price",
    "pkgquantity": 0,
    "salesrank": 54773,
    "sfbc": 4683,
    "sfbr": 0.99,
    "smallImage": "http://ecx.images-amazon.com/images/I/41fDombwLCL._SL75_.jpg",
    "title": "Fisher-Price Imaginext Toy Story Buzz Lightyear & Pizza Planet Truck",
    "upc": "887961768916"
  },
  {
    "asin": "0744018307",
    "bbw": true,
    "brand": "",
    "category": "book_display_on_website",
    "cpip": 2000,
    "docid": "9780744018301",
    "fba": false,
    "fbafees": 722,
    "inStock": "NOW",
    "lastMatchLookup": "2020-03-13T14:00:10.209183Z",
    "lastOfferLookup": "2020-03-13T14:00:13.670858Z",
    "listPrice": 3999,
    "manufacturer": "Prima Games",
    "pkgquantity": 0,
    "salesrank": 337073,
    "sfbc": 0,
    "sfbr": 0,
    "smallImage": "http://ecx.images-amazon.com/images/I/51NFIAHRfTL._SL75_.jpg",
    "title": "Wolfenstein II: The New Colossus: Prima Collector's Edition Guide",
    "upc": "9780744018301"
  }
]
```

Use `curl` to call the application to query data from the BigQuery `publicviews.ca_zip_codes` table in your lab project:

```bash
curl $CLOUD_RUN_SERVICE_URL/bq/$PROJECT_ID/publicviews/ca_zip_codes | jq .
```

This responds with the contents of a JSON file containing the results of the BigQuery SQL statement `SELECT * FROM publicviews.ca_zip_codes;`, which looks similar to this:

```sql
[
  {
    "Zipcode": "94123",
    "area_land_miles": 1.024,
    "state_code": "CA"
  },
  {
    "Zipcode": "96090",
    "area_land_miles": 1.027,
    "state_code": "CA"
  },
  {
    "Zipcode": "94929",
    "area_land_miles": 1.062,
    "state_code": "CA"
  }
]
```

Go is a great language for use on Cloud Run mainly because of its portability and compile size. 

Even though Go is a statically typed compiled language, you can effectively access different data platforms in a modular way. 

Look at the details of the application container for this Go App.

#### Take a closer look at the container

In the Cloud Console, click **Navigation menu** > **Cloud Run**.

Click **gcp-data-drive** in the services list.

Click the **Revisions** tab.

In the right panel, in the **Container** tab, click the **Image URL**.

The **Container** pane includes the URL for the Container image to examine the image in the Container Registry. 

The Container Registry opens in a new browser tab at the **Image details** window.

As you can see from the virtual size, the container is small as it only contains the Go binary, which gives it the smallest security attack service possible. 

Go containers also make great Anthos services because they are small and easily portable across CPU and OS types. 

This makes them easily transferable from Cloud to on premise platforms, and vice versa.

### Task 3. Deploy to Cloud Functions

Cloud Functions is Google Cloud's event-driven serverless compute platform. 

When you combine Go and Cloud Functions, you get the best serverless has to offer in terms of fast spin up times and infinite scale so your application can have the best event driven response times possible.

Have a look at the source code and see how to reuse the Google Cloud Data Drive source code in a Cloud Function.

#### Review the `main` function

At the start of the `main` function in `DIY-Tools/gcp-data-drive/cmd/webserver/main.go`, you tell the web server to send all HTTP requests to the `gcpdatadrive.GetJSONData` Go function.

```go
func main() {

    // Register the initial HTTP handler.
    http.HandleFunc("/", gcpdatadrive.GetJSONData)

    port := os.Getenv("PORT")
...
```

View the main function in main.go on GitHub.

In a Cloud Function, the `main.go` is not used, instead the Cloud Function runtime is configured to send HTTP requests directly to the `gcpdatadrive.GetJSONData` Go function that is defined in the `DIY-Tools/gcp-data-drive/gcpdatadrive.go` file.

You can see how this is done by looking at how the Google Cloud Data Drive application is deployed to Cloud Functions using Cloud Build with `cloudbuild_gcf.yaml`:

> Note: The comments have been removed for clarity.

View the cloudbuild_gcf.yaml on GitHub.

These Cloud Build steps are also similar to those used to deploy the application to Cloud Run, but in this case, you deploy the application to Cloud Functions using the `gcloud functions deploy` command.

Notice that the Cloud Functions `--entrypoint` parameter is used to specify the `GetJSONData` function and not the `main` function in the main Go package that is used when it is deployed to App Engine or Cloud Run.

#### Deploy the Google Cloud Data Drive app

Assign the Cloud Functions service account the `roles/artifactregistry.reader` role to allow the Cloud Functions service account to read from Artifact Registry:

```bash
PROJECT_NUMBER=$(gcloud projects list --filter="PROJECT_ID=$PROJECT_ID" --format="value(PROJECT_NUMBER)")

SERVICE_ACCOUNT_EMAIL="${PROJECT_NUMBER}@cloudbuild.gserviceaccount.com"

gcloud projects add-iam-policy-binding ${PROJECT_ID} \ 
    --member "serviceAccount:${SERVICE_ACCOUNT_EMAIL}" \ 
    --role "roles/artifactregistry.reader"
```

Still in `DIY-Tools/gcp-data-drive`, deploy to Cloud Functions with Cloud Build:

```bash
gcloud builds submit \ 
  --config cloudbuild_gcf.yaml \ 
  --project $PROJECT_ID \ 
  --no-source \ 
  --substitutions=_GIT_SOURCE_BRANCH="master",_GIT_SOURCE_URL="https://github.com/GoogleCloudPlatform/DIY-Tools",_GCP_REGION="us-central1"
```

> Note: If you get a 403 permission error, wait a minute and retry the build command. It typically takes a couple of minutes for the service account permissions to propagate.

Enter the following command to allow unauthorized access to the Google Cloud Data Drive Cloud Function:

```bash
gcloud alpha functions add-iam-policy-binding gcp-data-drive \ 
  --member=allUsers \ 
  --role=roles/cloudfunctions.invoker
```

Store the Cloud Functions HTTP Trigger URL in an environment variable:

```bash
export CF_TRIGGER_URL=$(gcloud functions describe gcp-data-drive --format="value(httpsTrigger.url)")
```

Use `curl` to call the application to query data from the Firestore `symbols` collection in your project:

```bash
curl $CF_TRIGGER_URL/fs/$PROJECT_ID/symbols/product/symbol | jq .
```

This responds with the contents of a JSON file containing the values from the `symbols` collection in your project .

```json
[
  {
    "asin": "",
    "brand": "",
    "category": "",
    "docid": "914600502073",
    "fbafees": 0,
    "lastMatchLookup": "0001-01-01T00:00:00Z",
    "listPrice": 0,
    "manufacturer": "",
    "pkgquantity": 0,
    "salesrank": 0,
    "smallImage": "",
    "title": "",
    "upc": "914600502073"
  },
  {
    "asin": "0744018307",
    "bbw": true,
    "brand": "",
    "category": "book_display_on_website",
    "cpip": 2000,
    "docid": "9780744018301",
    "fba": false,
    "fbafees": 722,
    "inStock": "NOW",
    "lastMatchLookup": "2020-03-13T14:00:10.209183Z",
    "lastOfferLookup": "2020-03-13T14:00:13.670858Z",
    "listPrice": 3999,
    "manufacturer": "Prima Games",
    "pkgquantity": 0,
    "salesrank": 337073,
    "sfbc": 0,
    "sfbr": 0,
    "smallImage": "http://ecx.images-amazon.com/images/I/51NFIAHRfTL._SL75_.jpg",
    "title": "Wolfenstein II: The New Colossus: Prima Collector's Edition Guide",
    "upc": "9780744018301"
  }
]
```

Use `curl` to call the application to query data from the BigQuery `publicviews.ca_zip_codes` table in your lab project:

```bash
curl $CF_TRIGGER_URL/bq/$PROJECT_ID/publicviews/ca_zip_codes
```

This responds with the contents of a JSON file containing the results of the BigQuery SQL statement `SELECT * FROM publicviews.ca_zip_codes;`.

```sql
[
  {
    "Zipcode": "96090",
    "area_land_miles": 1.027,
    "state_code": "CA"
  },
  {
    "Zipcode": "94929",
    "area_land_miles": 1.062,
    "state_code": "CA"
  }
]
```

### Task 4. Additional Cloud Functions triggers

Cloud Functions has an event driven architecture. 

The app you deployed uses an HTTP request as an event. 

Explore the code of another Go app that takes in a different event type. 

The function is triggered on a Firestore write event.

The Go source code below is adapted from the Go Code sample guide:

```go
package mygopackage

 import (
        "context"
        "fmt"
        "log"
        "time"

        "cloud.google.com/go/functions/metadata"
)

// FirestoreEvent is the payload of a Firestore event.
type FirestoreEvent struct {
        OldValue   FirestoreValue `json:"oldValue"`
        Value      FirestoreValue `json:"value"`
        UpdateMask struct {
                FieldPaths []string `json:"fieldPaths"`
        } `json:"updateMask"`
}

// FirestoreValue holds Firestore fields.
type FirestoreValue struct {
        CreateTime time.Time `json:"createTime"`
        Fields     interface{} `json:"fields"`
        Name       string      `json:"name"`
        UpdateTime time.Time   `json:"updateTime"`
}

// DoSomeThingOnWrite is triggered by
// a change to a Firestore document.
func DoSomeThingOnWrite(ctx context.Context, e FirestoreEvent) error {
        meta, err := metadata.FromContext(ctx)
        if err != nil {
                return fmt.Errorf("metadata.FromContext: %v", err)
        }
        // The variables e and meta contain
        // the information from the event
        // so now we can Go do some logic
        // work with the data. In this case
        // we are simply writing it to the log.

        log.Printf("Function triggered by change to: %v", meta.Resource)
        log.Printf("Old value: %+v", e.OldValue)
        log.Printf("New value: %+v", e.Value)
        return nil
}
```

View the example source code on GitHub.

This example contains code that can be used to deploy Cloud Functions that handle Firestore events, instead of the HTTP request trigger used in the lab sample application. 

You register this function with a Cloud Firestore event trigger using `DoSomeThingOnWrite` as the Cloud Functions entrypoint.

Cloud Functions currently support the following event triggers.

The example above is a simple case, but you can imagine the potential. 

Simple Go Cloud Functions do tasks that used to come with the burden of managing an operating system. 

For example, you can use a function like this to run Data Loss Prevention (DLP) to sanitize data when a customer writes something to Cloud Firestore via a mobile app.

The Cloud Function could rewrite a summary report to Firestore for web consumption based on a pub/sub event. 

Any number of small processes that are event based are good candidates for Go Cloud Functions. 

Best of all, there are zero servers to patch.

### Task 5. Deploy to App Engine

App Engine is well suited for running Go applications. 

App Engine is a serverless compute platform that is fully managed to scale up and down as workloads fluctuate. 

Go applications are compiled to a single binary executable file during deployment. 

Go cold startup times for applications are often between 80 and 1400 in milliseconds and when running, App Engine can horizontally scale to meet the most demanding global scale workloads in seconds.

#### Review the Cloud Build YAML config file

The Cloud Build YAML file, `DIY-Tools/gcp-data-drive/cloudbuild_appengine.yaml`, shown below contains the Cloud Build step definitions that deploy your application to App Engine. 

You use this file to deploy the application to App Engine.

The first step executes the `git` command to clone the source repository that contains the application. 

This step is parameterized to allow you to easily switch between application branches.

The second step executes the `sed` command to replace the `runtime: go113` line in the `app.yaml` file with `runtime: go121`. 

This is necessary because the Go 1.13 runtime is deprecated and will be removed in the future. Note that this is just a patch to keep the app running. 

You should update the app to use the latest Go runtime in your own projects.

The third step executes the `gcloud app deploy` command to deploy the application to App Engine.

As with the other examples, you could manually deploy this app using `gcloud app deploy`, but Cloud Build allows you to offload this to Google infrastructure, for example if you want to create a serverless CI/CD pipeline.

> Note: The comments from the file have been removed for clarity.

```yaml
steps:
- name: 'gcr.io/cloud-builders/git'
  args: ['clone','--single-branch','--branch',
        '${_GIT_SOURCE_BRANCH}','${_GIT_SOURCE_URL}']

- name: 'ubuntu'  # Or any base image containing 'sed'
  args: ['sed', '-i', 's/runtime: go113/runtime: go121/', 'app.yaml'] # Replace go113 with go121
  dir: 'DIY-Tools/gcp-data-drive/cmd/webserver'

- name: 'gcr.io/cloud-builders/gcloud'
  args: ['app','deploy','app.yaml','--project','$PROJECT_ID']
  dir: 'DIY-Tools/gcp-data-drive/cmd/webserver'
```

View cloudbuild_appengine.yaml on GitHub.

#### Deploy the Google Cloud Data Drive app

Still in `DIY-Tools/gcp-data-drive`, deploy the Go webserver app to App Engine using Cloud Build:

```bash
gcloud builds submit  \ 
  --config cloudbuild_appengine.yaml \ 
  --project $PROJECT_ID --no-source \ 
  --substitutions=_GIT_SOURCE_BRANCH="master",_GIT_SOURCE_URL="https://github.com/GoogleCloudPlatform/DIY-Tools"
```

Deployment takes a few minutes to complete.

Store the App Engine URL in an environment variable to use in the command to call the app:

> Note: The App Engine URL is the target url in the output.

```bash
export TARGET_URL=https://$(gcloud app describe --format="value(defaultHostname)")
```

Use `curl` to call the application running on App Engine to query data from Firestore:

```bash
curl $TARGET_URL/fs/$PROJECT_ID/symbols/product/symbol | jq .
```

This responds with the contents of a JSON file containing three values from the `symbols` collection in your project.

```json
[
  {
    "asin": "",
    "brand": "",
    "category": "",
    "docid": "914600502073",
    "fbafees": 0,
    "lastMatchLookup": "0001-01-01T00:00:00Z",
    "listPrice": 0,
    "manufacturer": "",
    "pkgquantity": 0,
    "salesrank": 0,
    "smallImage": "",
    "title": "",
    "upc": "914600502073"
  },
  {
    "asin": "0744018307",
    "bbw": true,
    "brand": "",
    "category": "book_display_on_website",
    "cpip": 2000,
    "docid": "9780744018301",
    "fba": false,
    "fbafees": 722,
    "inStock": "NOW",
    "lastMatchLookup": "2020-03-13T14:00:10.209183Z",
    "lastOfferLookup": "2020-03-13T14:00:13.670858Z",
    "listPrice": 3999,
    "manufacturer": "Prima Games",
    "pkgquantity": 0,
    "salesrank": 337073,
    "sfbc": 0,
    "sfbr": 0,
    "smallImage": "http://ecx.images-amazon.com/images/I/51NFIAHRfTL._SL75_.jpg",
    "title": "Wolfenstein II: The New Colossus: Prima Collector's Edition Guide",
    "upc": "9780744018301"
  }
]
```

Use `curl` to call the app running on App Engine to query data from BigQuery:

```bash
curl $TARGET_URL/bq/$PROJECT_ID/publicviews/ca_zip_codes | jq .
```

This responds with the contents of a JSON file containing the results of the BigQuery SQL statement `SELECT * FROM publicviews.ca_zip_codes;`.

```sql
[
  {
    "Zipcode": "96090",
    "area_land_miles": 1.027,
    "state_code": "CA"
  },
  {
    "Zipcode": "94929",
    "area_land_miles": 1.062,
    "state_code": "CA"
  }
]
```

#### Increase the load

Increase the load to see what happens.

Use the nano editor to create a simple shell script to put some load on the application:

```bash
nano loadgen.sh
```

Type or paste the following script into the editor:

```bash
#!/bin/bash
for ((i=1; i<=1000; i++)); do
   curl "$TARGET_URL/bq/$PROJECT_ID/publicviews/ca_zip_codes" > /dev/null &
done
```

Press Ctrl+X, Y, and then Enter to save the new file.

Make the script executable:

```bash
chmod +x loadgen.sh
```

Run the load test:

```bash
./loadgen.sh
```

In the Console, click Navigation menu > App Engine > Instances.

The Instances window opens and shows a summary of requests/second and a list of instances spawned when you ran the load test in Cloud Shell. 

Notice how App Engine automatically created additional app instances and distributed the incoming HTTP traffic.

> Note: It may take 3 to 5 minutes for the Summary graph to show data. Don't forget to refresh the Instances window!

In Cloud Shell, press Ctrl+C to end the load test if it is still running.

### Congratulations

In this self-paced lab, you explored how to use the Go programming language to deploy to the all Google Cloud serverless compute platforms. 

The following are key takeaways:

- The Go programming language has awesome profitability and efficiency features that make it an excellent fit for modern cloud and on prem applications.

- The same Go code base can be deployed to all Google Cloud serverless, container based, and Anthos compute platforms without modification.

- Cloud Build (also serverless) is a key part of a cloud based CI/CD pipeline to provide consistency in delivering to your application lifecycles.

- Cloud Run, Cloud Functions, and App Engine are simple serverless platforms that operate at Google scale and are easy to deploy to.

- Google Cloud Data Drive is an open source data extraction library that I can use in my existing Google Cloud environment.
