# Develop Serverless Apps with Firebase: Challenge Lab

## GSP344

### Overview

In a challenge lab you’re given a scenario and a set of tasks. Instead of following step-by-step instructions, you will use the skills learned from the labs in the course to figure out how to complete the tasks on your own! 

### Setup and requirements

#### Provision the environment

Link to the project:

```bash
gcloud config set project $(gcloud projects list --format='value(PROJECT_ID)' --filter='qwiklabs-gcp')
```

Clone the repo:

```bash
git clone https://github.com/rosera/pet-theory.git
```

### Challenge scenario

In this lab you will create a frontend solution using a Rest API and Firestore database. 

Cloud Firestore is a NoSQL document database that is part of the Firebase platform where you can store, sync, and query data for your mobile and web apps at scale. 

Lab content is based on resolving a real world scenario through the use of Google Cloud serverless infrastructure.

### Task 1. Create a Firestore database

In this scenario you create a Firestore Database in Google Cloud. 

The high level architecture diagram below summarizes the general architecture.

Requirements:

- Cloud Firestore: `Native Mode`

- Location: `europe-west1`

#### Create a Firestore database

To complete this section successfully, you are required to implement the following:

- Cloud Firestore Database

- Use Firestore Native Mode

- Add location `europe-west1`

Go to Navigation menu > Firestore > Create > Select Naive Mode > Location: nam5 > Create Database

### Task 2. Populate the database

In this scenario, populate the database using test data.

#### Populate the database

Example Firestore schema:

Collection|Document|Field
data|70234439|[dataset]

The Netflix Shows Dataset includes the following information:

Field	Description
show_id:	Unique ID for every Movie / Tv Show
type:	Identifier - A Movie or TV Show
title:	Title of the Movie / Tv Show
director:	Director of the Movie
cast:	Actors involved in the movie / show
country:	Country where the movie / show was produced
date_added:	Date it was added on Netflix
release_year:	Actual Release year of the move / show
rating:	TV Rating of the movie / show
duration:	Total Duration - in minutes or number of seasons

To complete this section successfully, you are required to implement the following tasks:

Use the sample code from `pet-theory/lab06/firebase-import-csv/solution`:

```bash
cd ~/pet-theory/lab06/firebase-import-csv/solution
```

```bash
npm install
```

To import CSV use the node `pet-theory/lab06/firebase-import-csv/solution/index.js`:

```bash
node index.js netflix_titles_original.csv
```

> Note: Verify the Firestore Database has been updated by viewing the data in the Firestore UI.

### Task 3. Create a REST API

In this scenario, create an example REST API.

#### Cloud Run development

- Container Registry Image: `rest-api:0.1`
- Cloud Run Service: `netflix-dataset-service`
- Permission: `--allow-unauthenticated`

To complete this section successfully, you are required to implement the following tasks:

Access `pet-theory/lab06/firebase-rest-api/solution-01`.

```bash
cd ~/pet-theory/lab06/firebase-rest-api/solution-01
```

Build and Deploy the code to Google Container Registry.

```bash
npm install
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.1
```

Deploy the image as a Cloud Run service.

```bash
gcloud run deploy netflix-dataset-service --image gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.1 --allow-unauthenticated
```

> Note: Deploy your service with 1 max instance to ensure you do not exceed the max limit for Cloud Run instances.

Go to **Cloud Run** and obtain the service URL of **netflix-dataset-service**:

```bash
export SERVICE_URL=<service URL>
```

Verify response with: `{"status":"Netflix Dataset! Make a query."}`

```bash
curl -X GET $SERVICE_URL
```

### Task 4. Firestore API access

In this scenario, deploy an updated revision of the code to access the Firestore DB.

#### Deploy Cloud Run revision 0.2

- Container Registry Image: `rest-api:0.2`
- Cloud Run Service: `netflix-dataset-service`
- Permission: `--allow-unauthenticated`

To complete this section successfully, you are required to implement the following tasks:

Access `pet-theory/lab06/firebase-rest-api/solution-02`.

```bash
cd ~/pet-theory/lab06/firebase-rest-api/solution-02
```

Build the updated application.

```bash
npm install

```

Use Cloud Build to tag and deploy image revision to Container Registry.

```bash
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.2
```

Deploy the new image a Cloud Run service.

```bash
gcloud run deploy netflix-dataset-service --image gcr.io/$GOOGLE_CLOUD_PROJECT/rest-api:0.2 --allow-unauthenticated
```

> Note: Deploy your service with 1 max instance to ensure you do not exceed the max limit for Cloud Run instances.

Go to **Cloud Run** and obtain the service URL of **netflix-dataset-service**:

```bash
export SERVICE_URL=<service URL>
```

Check response with json dataset.

```bash
curl -X GET $SERVICE_URL/2019
```

### Task 5. Deploy the Staging Frontend

In this scenario, deploy the Staging Frontend.

#### Deploy Staging Frontend

- REST_API_SERVICE: `REST API SERVICE URL`
- Container Registry Image: `frontend-staging:0.1`
- Cloud Run Service: `frontend-staging-service`

To complete this section successfully, you are required to implement the following tasks:

Access pet-theory/lab06/firebase-frontend.

```bash
cd ~/pet-theory/lab06/firebase-frontend/
```

Build the frontend staging application.

```bash
npm install
```

Use Cloud Build to tag and deploy image revision to Container Registry.

```bash
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-staging:0.1
```

Deploy the new image as a Cloud Run service.

```bash
gcloud run deploy frontend-staging-service --image gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-staging:0.1
```

> Note: Deploy your service with 1 max instance to ensure you do not exceed the max limit for Cloud Run instances.

Frontend access to Rest API and Firestore Database.

Access the Frontend Service URL.

> Note: It's using a demo dataset to provide the onscreen entries.

### Task 6. Deploy the Production Frontend

In this scenario, update the Staging Frontend to use the Firestore database.

#### Deploy Production Frontend

- REST_API_SERVICE: `REST API SERVICE URL`
- Container Registry Image: `frontend-production:0.1`
- Cloud Run Service: `frontend-production-service`

To complete this section successfully, you are required to implement the following tasks:

Access `pet-theory/lab06/firebase-frontend/public`.

```bash
cd ~/pet-theory/lab06/firebase-frontend/public
```

Update the frontend application i.e. `app.js` to use the REST API.

Don't forget to append the year to the SERVICE_URL.

```bash
nano app.js # comment line 3 and uncomment line 4, replace netflix-dataset-service url
npm install
```

Use Cloud Build to tag and deploy image revision to Container Registry.

```bash
gcloud builds submit --tag gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-production:0.1
```

Deploy the new image a Cloud Run service.

```bash
gcloud run deploy frontend-production-service --image gcr.io/$GOOGLE_CLOUD_PROJECT/frontend-production:0.1
```

> Note: Deploy your service with 1 max instance to ensure you do not exceed the max limit for Cloud Run instances.

Frontend access to Rest API and Firestore Database.

Now that the services have been deployed you will be able to see the contents of the Firestore database using the frontend service.

### Congratulations

Congratulations! 

In this lab, you have successfully created a Firestore database, populated it with data, created a REST API, and deployed a frontend application that interacts with the Firestore database and REST API. 

You also learned how to deploy a staging and production frontend application.
