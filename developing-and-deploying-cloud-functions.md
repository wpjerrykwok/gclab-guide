Developing and Deploying Cloud Functions
experiment
Lab
schedule
45 minutes
universal_currency_alt
No cost
show_chart
Introductory
info
This lab may incorporate AI tools to support your learning.
Overview
Cloud Functions (2nd gen) is the next version of Google Cloud Functions, Google Cloud's Functions-as-a-Service offering. This new version comes with an advanced feature set and is now powered by Cloud Run and Eventarc, giving you more advanced control over performance and scalability, and more control around the functions runtime and triggers from over 90+ event sources.

In this lab, you create Cloud Functions that respond to HTTP requests, and event-driven functions that are triggered by Cloud Storage events. You will also deploy multiple revisions of a Cloud Function and explore new settings.

What's New?
This new version of Cloud Functions provides an enhanced FaaS experience powered by Cloud Run, Cloud Build, Artifact Registry, and Eventarc.

Longer request processing: Run your Cloud Functions longer than the 5-minute default, making it easier to run longer request workloads such as processing large streams of data from Cloud Storage or BigQuery. For HTTP functions, this is up to 60 minutes. For event-driven functions, this is up to 10 minutes.
Larger instances: Take advantage of up to 16 GB of RAM and 4 vCPUs on Cloud Functions allowing larger in-memory, compute-intensive, and more parallel workloads.
Concurrency: Process up to 1000 concurrent requests with a single function instance, minimizing cold starts and improving latency when scaling.
Minimum instances: Provide for pre-warmed instances to cut your cold starts and make sure the bootstrap time of your application does not affect application performance.
Traffic splitting: Support multiple versions of your functions, split traffic between different versions and roll your function back to a prior version.
Objectives
In this lab, you:

Write a function that responds to HTTP requests, and deploy the function using Google Cloud console.
Write a function that responds to Cloud Storage events, and deploy the function from your local Cloud Shell environment.
Write unit tests for your function and test your function locally.
Deploy multiple revisions of a Cloud Function using the Google Cloud console.
Test the latest revision of your function and verify it works as expected.
Setup
For each lab, you get a new Google Cloud project and set of resources for a fixed time at no cost.

Sign in to Qwiklabs using an incognito window.

Note the lab's access time (for example, 1:15:00), and make sure you can finish within that time.
There is no pause feature. You can restart if needed, but you have to start at the beginning.

When ready, click Start lab.

Note your lab credentials (Username and Password). You will use them to sign in to the Google Cloud Console.

Click Open Google Console.

Click Use another account and copy/paste credentials for this lab into the prompts.
If you use other credentials, you'll receive errors or incur charges.

Accept the terms and skip the recovery resource page.

Note: Do not click End Lab unless you have finished the lab or want to restart it. This clears your work and removes the project.

Activate Google Cloud Shell
Google Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud.

Google Cloud Shell provides command-line access to your Google Cloud resources.

In Cloud console, on the top right toolbar, click the Open Cloud Shell button.

Highlighted Cloud Shell icon

Click Continue.

It takes a few moments to provision and connect to the environment. When you are connected, you are already authenticated, and the project is set to your PROJECT_ID. For example:

Project ID highlighted in the Cloud Shell Terminal

gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

You can list the active account name with this command:
gcloud auth list
Copied!
Output:

Credentialed accounts:
 - @.com (active)
Example output:

Credentialed accounts:
 - google1623327_student@qwiklabs.net
You can list the project ID with this command:
gcloud config list project
Copied!
Output:

[core]
project = 
Example output:

[core]
project = qwiklabs-gcp-44776a13dea667a6
Note: Full documentation of gcloud is available in the gcloud CLI overview guide .
Task 1. Enable APIs
Before you create Cloud Functions, you must enable the relevant APIs.

Sign in to the Google Cloud console with your lab credentials, and open the Cloud Shell terminal window.

Run the following commands in Cloud Shell to set your Project ID and Region environment variables:

PROJECT_ID=$(gcloud config get-value project)
REGION=us-east4
Copied!
Run the following command to enable all necessary service APIs.

gcloud services enable \
  artifactregistry.googleapis.com \
  cloudfunctions.googleapis.com \
  cloudbuild.googleapis.com \
  eventarc.googleapis.com \
  run.googleapis.com \
  logging.googleapis.com \
  storage.googleapis.com \
  pubsub.googleapis.com
Copied!
Task 2. Create an HTTP function
For the first function, you create an authenticated Node.js function that responds to HTTP requests to convert a temperature value from Fahrenheit to Celsius, and respond with the converted temperature value.

Create the function
To open the Functions Overview page in the Google Cloud console, in the Navigation menu (navmenu), click Cloud Functions.

You might need to click More Products in the navigation menu to view the Cloud Functions item.
Click Create Function.

In the function configuration form, for Environment, select 2nd gen.

For Function name, type temperature-converter.

For Region, select the us-east4 region.

Click Runtime, build, connections and security settings.

For Maximum number of instances, enter 1.

Leave the remaining settings as their defaults, and click Next.

The default authentication setting requires authentication when invoking the function, which is recommended.
Our function will be implemented in Node.js. In the Code editor, for Runtime, select the latest version of the language runtime, for example Node.js 20.

For Entry point, type convertTemp.

In the Inline Editor, replace any existing sample code with the following code in the index.js file:

const functions = require('@google-cloud/functions-framework');

functions.http('convertTemp', (req, res) => {
 var dirn = req.query.convert;
 var ctemp = (req.query.temp - 32) * 5/9;
 var target_unit = 'Celsius';

 if (req.query.temp === undefined) {
    res.status(400);
    res.send('Temperature value not supplied in request.');
 }
 if (dirn === undefined)
   dirn = process.env.TEMP_CONVERT_TO;
 if (dirn === 'ctof') {
   ctemp = (req.query.temp * 9/5) + 32;
   target_unit = 'Fahrenheit';
 }

 res.send(`Temperature in ${target_unit} is: ${ctemp.toFixed(2)}.`);
});
Copied!
This is a simple HTTP function in Node.js that converts a temperature value in Fahrenheit or Celsius passed in the request and responds with the converted value.

Functions framework is an open source FaaS (function as a service) framework and library that lets you write lightweight functions that run in different environments that include Cloud Functions, your local development machine, and Cloud Run.
For more information, view the functions framework documentation.

Deploy and test the function
To deploy the function, click Deploy. Wait until the Cloud Build and underlying Cloud Run service is created.

After the function is deployed, in the Functions details page, a green check mark is displayed next to the function name.

verify function was deployed

In Cloud Shell, retrieve the HTTP URI of the function and store it in an environment variable:

FUNCTION_URI=$(gcloud functions describe temperature-converter --gen2 --region $REGION --format "value(serviceConfig.uri)"); echo $FUNCTION_URI
Copied!
In Cloud Shell, test the function with the following command:

curl -H "Authorization: bearer $(gcloud auth print-identity-token)" "${FUNCTION_URI}?temp=70"
Copied!
You should see the following message as a response:

Temperature in Celsius is: 21.11.
Rerun the command passing in the temperature value in Celsius and the conversion unit:

curl -H "Authorization: bearer $(gcloud auth print-identity-token)" "${FUNCTION_URI}?temp=21.11&convert=ctof"
Copied!
You should see the following message as a response:

Temperature in Fahrenheit is: 70.00.
Click Check my progress to verify the objective.
Create an HTTP function

Task 3. Create a Cloud Storage function
In this task, you create an event-driven function in Node.js that responds to events from a Cloud Storage bucket.

Setup
To use Cloud Storage triggers with Cloud Functions, the Cloud Storage service agent must have the Pub/Sub Publisher (roles/pubsub.publisher) IAM role on your project.

Cloud Storage functions are based on Pub/Sub notifications from Cloud Storage, and support the finalize, delete, archive, and metadata update event types.

Set up an environment variable for the Cloud Storage agent's service account:

SERVICE_ACCOUNT=$(gcloud storage service-agent)
Copied!
Grant the Pub/Sub publisher role to the Cloud Storage service agent on your project. A service agent is a Google-managed service account that allows the service to access your resources.

gcloud projects add-iam-policy-binding $PROJECT_ID --member serviceAccount:$SERVICE_ACCOUNT --role roles/pubsub.publisher
Copied!
To trigger the function in a later task, you will upload a file to Cloud Storage. Copy this file that contains sample temperature data to your Cloud Shell environment:

gcloud storage cp gs://cloud-training/CBL491/data/average-temps.csv .
Copied!
Create the function
In this subtask, you develop a function locally in Cloud Shell. Create a directory and navigate to it:

mkdir ~/temp-data-checker && cd $_
Copied!
Create the index.js and package.json files for your Node.js function:

touch index.js && touch package.json
Copied!
In the Cloud Shell toolbar, click Open Editor.

You can switch between Cloud Shell and the code editor using Open Editor and Open Terminal, or click Open in new window to leave the editor open in a separate tab.
In the editor, add the following code to the temp-data-checker/index.js file:

const functions = require('@google-cloud/functions-framework');

// Register a CloudEvent callback with the Functions Framework that will
// be triggered by Cloud Storage events.
functions.cloudEvent('checkTempData', cloudEvent => {
  console.log(`Event ID: ${cloudEvent.id}`);
  console.log(`Event Type: ${cloudEvent.type}`);

  const file = cloudEvent.data;
  console.log(`Bucket: ${file.bucket}`);
  console.log(`File: ${file.name}`);
  console.log(`Created: ${file.timeCreated}`);
});
Copied!
The function uses `console.log` statements to log messages to stdout. Cloud Functions includes simple runtime logging by default. Log messages are picked up and parsed by Cloud Functions and sent to Cloud Logging, where they can be viewed in the Cloud console.
To specify dependencies and other package information for your Node.js function, add the following content to the temp-data-checker/package.json file:

{
  "name": "temperature-data-checker",
  "version": "0.0.1",
  "main": "index.js",
  "dependencies": {
    "@google-cloud/functions-framework": "^2.1.0"
  }
}
Copied!
Deploy the function
In the Cloud Shell terminal window, first set an environment variable for the bucket name:

BUCKET="gs://gcf-temperature-data-$PROJECT_ID"
Copied!
Create a Cloud Storage bucket to store our temperature data file:

gcloud storage buckets create -l $REGION $BUCKET
Copied!
To deploy the function, execute the following command in Cloud Shell:

gcloud functions deploy temperature-data-checker \
 --gen2 \
 --runtime nodejs20 \
 --entry-point checkTempData \
 --source . \
 --region $REGION \
 --trigger-bucket $BUCKET \
 --trigger-location $REGION \
 --max-instances 1
Copied!
By specifying the trigger-bucket, every change to the files in this bucket will trigger function execution.

Note: If you see a permissions error, please wait a few minutes, and try the deployment again. It takes some time for the APIs to be enabled.
Verify that the function was deployed successfully.

verify function was deployed

Test the function
Test the function by uploading the temperature data file to the Cloud Storage bucket:

gcloud storage cp ~/average-temps.csv $BUCKET/average-temps.csv
Copied!
Run the following command. You should see information from the received CloudEvent in the logs:

gcloud functions logs read temperature-data-checker \
 --region $REGION --gen2 --limit=100 --format "value(log)"
Copied!
Note: It may take a minute for the logs to generate.
You should see output similar to the following:

Created: 2022-10-05T22:12:32.470Z
File: average-temps.csv
Bucket: gcf-temperature-data-cf-project
Event Type: google.cloud.storage.object.v1.finalized
Event ID: 5834307012388233
The Cloud Storage function created in this lab extracts metadata about the bucket and resource that was created. To extract and process the data with downstream services, use the Cloud Storage client libraries for your language runtime.
Click Check my progress to verify the objective.
Create a Cloud Storage Function

Task 4. Local development and testing
Cloud Functions supports several methods of running your functions outside of Cloud Functions itself. This is useful during iterative development, pre-deployment testing in local environments, data locality compliance, and multi-cloud deployments.

In this task, you modify an existing HTTP function, develop unit and integration tests, and test the function locally before deploying the function to Cloud Functions.

Download the function source code
To open the Functions Overview page in the Google Cloud console, in the Navigation menu (navmenu), click Cloud Functions.

To view the details of the temperature-converter function, select it's name.

To view the function source code, click the SOURCE tab.

Click Download ZIP, and save the zip file in a folder on your computer.

In the Cloud Shell toolbar, click the More menu (moremenu), and select Upload.

In the Upload dialog, click Choose Files.

Select the zip file from the folder that you downloaded previously, click Open, and then click Upload in the dialog.

After the file is uploaded, in Cloud Shell, run the following command:

mkdir ~/temp-data-converter && cd $_
Copied!
Extract the contents of the .zip file:

unzip ../function-source.zip
Copied!
Add unit tests to the function code
Let's first add some unit tests to the temperature-convert function.

Create a directory that will contain all the function tests, and the unit test source file:

mkdir tests && touch tests/unit.http.test.js
Copied!
In the Cloud Shell toolbar, click Open Editor.

In the editor, add the following code to the temp-data-converter/tests/unit.http.test.js file. The test code creates a mock that wraps HTTP requests and responses and uses Sinon stubs to interpret the responses received from the function.

const {getFunction} = require('@google-cloud/functions-framework/testing');

describe('functions_convert_temperature_http', () => {
  // Sinon is a testing framework that is used to create mocks for Node.js applications written in Express.
  // Express is Node.js web application framework used to implement HTTP functions.
  const sinon = require('sinon');
  const assert = require('assert');
  require('../');

  const getMocks = () => {
    const req = {body: {}, query: {}};

    return {
      req: req,
      res: {
        send: sinon.stub().returnsThis(),
        status: sinon.stub().returnsThis()
      },
    };
  };

  let envOrig;
  before(() => {
    envOrig = JSON.stringify(process.env);
  });

  after(() => {
    process.env = JSON.parse(envOrig);
  });

  it('convertTemp: should convert a Fahrenheit temp value by default', () => {
    const mocks = getMocks();
    mocks.req.query = {temp: 70};

    const convertTemp = getFunction('convertTemp');
    convertTemp(mocks.req, mocks.res);
    assert.strictEqual(mocks.res.send.calledOnceWith('Temperature in Celsius is: 21.11.'), true);
  });

  it('convertTemp: should convert a Celsius temp value', () => {
    const mocks = getMocks();
    mocks.req.query = {temp: 21.11, convert: 'ctof'};

    const convertTemp = getFunction('convertTemp');
    convertTemp(mocks.req, mocks.res);
    assert.strictEqual(mocks.res.send.calledOnceWith('Temperature in Fahrenheit is: 70.00.'), true);
  });

  it('convertTemp: should convert a Celsius temp value by default', () => {
    process.env.TEMP_CONVERT_TO = 'ctof';
    const mocks = getMocks();
    mocks.req.query = {temp: 21.11};

    const convertTemp = getFunction('convertTemp');
    convertTemp(mocks.req, mocks.res);
    assert.strictEqual(mocks.res.send.calledOnceWith('Temperature in Fahrenheit is: 70.00.'), true);
  });

  it('convertTemp: should return an error message', () => {
    const mocks = getMocks();

    const convertTemp = getFunction('convertTemp');
    convertTemp(mocks.req, mocks.res);

    assert.strictEqual(mocks.res.status.calledOnce, true);
    assert.strictEqual(mocks.res.status.firstCall.args[0], 400);
  });
});
Copied!
Note: We have four unit tests, of which three are positive tests that expect a certain temperature value in the response from the function.
The fourth test is a negative test that expects the function to return a response status code of 400 (Bad Request), since there is no temperature value passed in the request object.

In the temp-data-converter/package.json file, update the dependencies for your Node.js function to include the scripts, and devDependencies sections:

{
  "name": "temperature-converter",
  "version": "0.0.1",
  "main": "index.js",
  "scripts": {
    "unit-test": "mocha tests/unit*test.js --timeout=6000 --exit",
    "test": "npm -- run unit-test"
  },
  "devDependencies": {
    "mocha": "^9.0.0",
    "sinon": "^14.0.0"
  },
  "dependencies": {
    "@google-cloud/functions-framework": "^2.1.0"
  }
}
Copied!
Run the unit tests
In the Cloud Shell terminal window, run the following command to first install the function's dependencies:

npm install
Copied!
This commands runs the Node package manager that downloads any dependencies defined in the package.json file.
Execute the unit test:

npm test
Copied!
From the command output, verify that all of the tests passed.

> temperature-converter@0.0.1 test
> npm -- run unit-test

> temperature-converter@0.0.1 unit-test
> mocha tests/unit*test.js --timeout=6000 --exit

functions_convert_temperature_http
  ✔ convertTemp: should convert a Fahrenheit temp value by default
  ✔ convertTemp: should convert a Celsius temp value
  ✔ convertTemp: should convert a Celsius temp value by default
  ✔ convertTemp: should return an error message

4 passing (10ms)
Mocha is a JavaScript test framework that runs on Node.js an enables asynchronous testing in a simple, flexible manner.
You should also consider implementing unit, integration, and system tests for your HTTP and event-driven Cloud Functions. To learn more about implementing pre-deployment testing, see the links in the Learn More section at the end of this lab.
Task 5. Function revisions
Cloud Functions (2nd gen) supports multiple revisions of a function, enabling you to split traffic between different revisions or roll your function back to a prior revision.

Each time you deploy or redeploy a function, a new revision of the underlying Cloud Run service is automatically created. Revisions are immutable and cannot be modified once they are created. To make changes to a function, you must redeploy it.

In this task, you deploy the HTTP function that you created in the previous task with an environment variable, and use the Google Cloud console to manage traffic between two revisions of the function.

Redeploy the function
In the Google Cloud console, navigate to the Function details page of the temperature-converter function.

To open the function's underlying Cloud Run service details page, click the function's name link under Powered by Cloud Run (powered by cloud run).

The Cloud Run service details page contains charts of your function's metrics which you can use to determine the health of your function.
Click Edit and deploy new revision.

In the Container tab, select the Variables and secrets tab.

To add an environment variable, in the Environment variables section, click Add Variable.

For Name 1, type TEMP_CONVERT_TO

To provide a value for the environment variable, for Value 1, type ctof

Click Deploy.

Wait for the deploy process to complete. After the process is completed, you will have two revisions of the function displayed.

By default, the latest deployed revision receives 100% of traffic to the function. This revision is deployed with an environment variable that instructs the function to convert all temperature values from Celsius to Fahrenheit by default.

Test the latest function revision
In Cloud Shell, to test the function, run the following curl command with the temp query parameter value in Celsius:

curl -H "Authorization: bearer $(gcloud auth print-identity-token)" "${FUNCTION_URI}?temp=21.11"
Copied!
You should see the following response message from the function:

Temperature in Fahrenheit is: 70.00.
Click Check my progress to verify the objective.
Create function revisions

Congratulations!
In this lab, you deployed 2nd Gen Cloud Functions that responded to HTTP requests and Cloud Storage Events. You implemented pre-deployment testing with unit tests for an HTTP function and executed them to verify positive and negative scenarios when calling the function. You also deployed multiple revisions of a function and tested the latest revision to verify the function behavior.

Next Steps / Learn More