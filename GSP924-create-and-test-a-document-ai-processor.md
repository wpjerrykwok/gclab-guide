Create and Test a Document AI Processor
experiment
Lab
schedule
1 hour
universal_currency_alt
No cost
show_chart
Introductory
info
This lab may incorporate AI tools to support your learning.
GSP924
Google Cloud self-paced labs logo

Overview
The Document AI API is a document understanding solution that takes unstructured data, such as documents and emails, and makes the data easier to understand, analyze, and consume. With the general form processor used in this lab, you can extract key/value pairs from a simple document.

In this lab you will learn how to create document parsers using Document AI, submit documents for processing via Google Cloud using the Cloud console & command line, and use Python to make synchronous API calls.

What you'll learn
You will learn how to perform the following tasks:

Create and test Document AI processor using the console.
Test Document AI processors using the command line.
Test Document AI synchronous API calls using Python.
Setup and requirements
Before you click the Start Lab button
Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources will be made available to you.

This hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended).
Note: Use an Incognito or private browser window to run this lab. This prevents any conflicts between your personal account and the Student account, which may cause extra charges incurred to your personal account.
Time to complete the lab---remember, once you start, you cannot pause a lab.
Note: If you already have your own personal Google Cloud account or project, do not use it for this lab to avoid extra charges to your account.
How to start your lab and sign in to the Google Cloud console
Click the Start Lab button. If you need to pay for the lab, a pop-up opens for you to select your payment method. On the left is the Lab Details panel with the following:

The Open Google Cloud console button
Time remaining
The temporary credentials that you must use for this lab
Other information, if needed, to step through this lab
Click Open Google Cloud console (or right-click and select Open Link in Incognito Window if you are running the Chrome browser).

The lab spins up resources, and then opens another tab that shows the Sign in page.

Tip: Arrange the tabs in separate windows, side-by-side.

Note: If you see the Choose an account dialog, click Use Another Account.
If necessary, copy the Username below and paste it into the Sign in dialog.

student-00-ec16b9e96f0d@qwiklabs.net
Copied!
You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

s4j610LVntVx
Copied!
You can also find the Password in the Lab Details panel.

Click Next.

Important: You must use the credentials the lab provides you. Do not use your Google Cloud account credentials.
Note: Using your own Google Cloud account for this lab may incur extra charges.
Click through the subsequent pages:

Accept the terms and conditions.
Do not add recovery options or two-factor authentication (because this is a temporary account).
Do not sign up for free trials.
After a few moments, the Google Cloud console opens in this tab.

Note: To view a menu with a list of Google Cloud products and services, click the Navigation menu at the top-left. Navigation menu icon
Activate Cloud Shell
Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

Click Activate Cloud Shell Activate Cloud Shell icon at the top of the Google Cloud console.
When you are connected, you are already authenticated, and the project is set to your Project_ID, qwiklabs-gcp-04-41995a23c7cc. The output contains a line that declares the Project_ID for this session:

Your Cloud Platform project in this session is set to qwiklabs-gcp-04-41995a23c7cc
gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

(Optional) You can list the active account name with this command:
gcloud auth list
Copied!
Click Authorize.
Output:

ACTIVE: *
ACCOUNT: student-00-ec16b9e96f0d@qwiklabs.net

To set the active account, run:
    $ gcloud config set account `ACCOUNT`
(Optional) You can list the project ID with this command:
gcloud config list project
Copied!
Output:

[core]
project = qwiklabs-gcp-04-41995a23c7cc
Note: For full documentation of gcloud, in Google Cloud, refer to the gcloud CLI overview guide.
Task 1. Enable the Cloud Document AI API
In this task you will enable the Document AI API and create and test a general form processor. The general form processor will process any type of document and extract all the text content it can identify in the document. It is not limited to printed text, it can handle handwritten text and text in any orientation, supports a number of languages, and understands how form data elements are related to each other so that you can extract key/value pairs for form fields that have text labels.

Enable the Cloud Document AI API
Before you can begin using Document AI, you must enable the API.

From the Navigation menu (Navigation menu icon), click APIs & services > Library.

Search for Cloud Document AI API, then click the Enable button to use the API in your Google Cloud project.

If the Cloud Document AI API is already enabled you will see the Manage button and you can continue with the rest of the lab.

Click Check my progress to verify the objectives.

Enabled Cloud Document AI API
Task 2. Create and test a general form processor
Next you will create a Document AI processor using the Document AI Form Parser.

Create a processor
In the console, from the Navigation menu (Navigation menu icon), click Document AI > Overview.

Click Explore processors.

Click Create Processor for Form Parser, which is a type of general processor.

Specify the processor name as form-parser and select the region US (United States) from the list.

Click Create to create the general form-parser processor.

This will create the processor and return to the processor details page that will display the processor ID, status, and the prediction endpoint.

Make a note of the Processor ID as you will use it with curl to make a POST call to the API in a later task.
Download the sample form
In this task you will download the sample form from Cloud Storage. In order to upload this form in the next task, you first need to download it to your local machine.

Download the form.pdf file to your local machine.
The file should download directly. If the file opens in your browser instead, then download the file using the file controls within your browser. The form.pdf file is a HEALTH INTAKE FORM with sample hand-written data.

Upload a form for Document AI processing
Next you will upload the sample form you downloaded to your form-parser processor. It will then be analyzed and the results displayed in the console.

On the form-parser page, click the Upload Test Document button. A dialog will pop up - select the file you downloaded in the previous task for uploading.
A progress bar will indicate the level of completion of the analysis process and finally the results will be displayed. You will see that the general processor has captured the data on the form into a set of key/value pairs.

The key/value pairs parsed from the source document will be presented in the console. The left hand pane lists the data, and the right hand pane highlights with blue rectangles the source locations in the parsed document. Examine the output and compare the results with the source data.

Parser analysis, the resulting data in the left pane

In this task you will test a Document AI general form processor by making API calls from the command line.

Click Check my progress to verify the objectives.

Create and test a general form processor
Task 3. Set up the lab instance
In this section, you will set up the lab instance to use the Document AI API.

Connect to the lab VM instance using SSH
You will perform the remainder of the lab tasks in the lab VM called document-ai-dev.

From the Navigation menu (Navigation menu icon), click Compute Engine > VM Instances.

Click the SSH link for the VM Instance called document-ai-dev.

You will need the Document AI processor ID of the processor you created in Task 1 for this step. If you did not save it, then in the Cloud Console tab:

Open the Navigation menu (Navigation menu icon).
Click Document AI > Processors .
Click the name of your processor to open the details page.
From here you can copy the processor ID.
In the SSH session, create an environment variable to contain the Document AI processor ID. You must replace the placeholder for [your processor id]:
export PROCESSOR_ID=[your processor id]
Copied!
In the SSH session confirm that the environment variable contains the Document AI processor ID:
echo Your processor ID is:$PROCESSOR_ID
Copied!
This should print out the Processor ID similar to the following:
  Your processor ID is:4897d834d2f4415d
You will use this SSH session for the remaining tasks in this lab.

Authenticate API requests
In order to make requests to the Document AI API, you need to provide a valid credential. In this task create a service account, limit the permissions granted to that service account to those required for the lab, and then generate a credential for that account that can be used to authenticate Document AI API requests.

Set an environment variable with your Project ID, which you will use throughout this lab:
export PROJECT_ID=$(gcloud config get-value core/project)
Copied!
Create a new service account to access the Document AI API by using:
export SA_NAME="document-ai-service-account"
gcloud iam service-accounts create $SA_NAME --display-name $SA_NAME
Copied!
Bind the service account to the Document AI API user role:
gcloud projects add-iam-policy-binding ${PROJECT_ID} \
--member="serviceAccount:$SA_NAME@${PROJECT_ID}.iam.gserviceaccount.com" \
--role="roles/documentai.apiUser"
Copied!
Create the credentials that will be used to log in as your new service account and save them in a JSON file called key.json in your working directory:
gcloud iam service-accounts keys create key.json \
--iam-account  $SA_NAME@${PROJECT_ID}.iam.gserviceaccount.com
Copied!
Set the GOOGLE_APPLICATION_CREDENTIALS environment variable, which is used by the library to find your credentials, to point to the credentials file:
export GOOGLE_APPLICATION_CREDENTIALS="$PWD/key.json"
Copied!
Check that the GOOGLE_APPLICATION_CREDENTIALS environment variable is set to the full path of the credentials JSON file you created earlier:
echo $GOOGLE_APPLICATION_CREDENTIALS
Copied!
This environment variable is used by the gcloud command line tool to specify which credentials to use when executing commands. To read more about this form authentication, see the Application Default Credentials guide.

Download the sample form to the VM instance
Now you can download a sample form and then base64 encode it for submission to the Document AI API.

Enter the following command in the SSH window to download the sample form to your working directory:
gsutil cp gs://cloud-training/gsp924/health-intake-form.pdf .
Copied!
Create a JSON request file for submitting the base64 encoded form for processing:
echo '{"inlineDocument": {"mimeType": "application/pdf","content": "' > temp.json
base64 health-intake-form.pdf >> temp.json
echo '"}}' >> temp.json
cat temp.json | tr -d \\n > request.json
Copied!
Click Check my progress to verify the objectives.

Authenticate API requests and download the sample form
Task 4. Make a synchronous process document request using curl
In this task you process the sample document by making a call to the synchronous Document AI API endpoint using curl.

Submit a form for processing via curl. The result will be stored in output.json:
export LOCATION="us"
export PROJECT_ID=$(gcloud config get-value core/project)
curl -X POST \
-H "Authorization: Bearer "$(gcloud auth application-default print-access-token) \
-H "Content-Type: application/json; charset=utf-8" \
-d @request.json \
https://${LOCATION}-documentai.googleapis.com/v1beta3/projects/${PROJECT_ID}/locations/${LOCATION}/processors/${PROCESSOR_ID}:process > output.json
Copied!
Make sure your output.json file contains the results of the API call:
 cat output.json
Copied!
If you receive an authentication error, make sure you have set the GOOGLE_APPLICATION_CREDENTIALS environment variable to point to the credentials JSON file you created earlier. You may need to wait a few minutes for the IAM policy to propagate, so try again if you receive an error.
The access token for the Cloud IAM service account is generated on the fly and passed to the API using the Authorization: HTTP header. The response contains JSON formatted data that is saved to the file output.json.

Extract the form entities
Next, explore some of the information extracted from the sample form.

Extract the raw text detected in the document as follows:
sudo apt-get update 
sudo apt-get install jq
cat output.json | jq -r ".document.text"
Copied!
This lists all of the text detected in the uploaded document.

Extract the list of form fields detected by the form processor:
cat output.json | jq -r ".document.pages[].formFields"
Copied!
This lists the object data for all of the form fields detected in the document. The textAnchor.startIndex and textAnchor.endIndex values for each form can be used to locate the names of the detected forms in the document.text field. The Python script that you will use in the next task will do this mapping for you.

The JSON file is quite large as it includes the base64 encoded source document as well as all of the detected text and document properties. You can explore the JSON file by opening the file in a text editor or by using a JSON query tool like jq.

Check that a document has been processed by the Document AI API.
Task 5. Test a Document AI form processor using the Python client libraries
Make a synchronous call to the Document AI API using the Python Document AI client libraries.

Now you will process a document using the synchronous endpoint. For processing large amounts of documents at a time you can use the asynchronous API. To learn more about using the Document AI APIs, read the guide.

If you want to run Python scripts directly, you need to provide the appropriate credentials to those scripts, so that they can make calls to the API using a service account that has been configured with the correct permissions. To read more about how to configure this form of authentication, see the Authenticating as a service account documentation.

Configure your VM Instance to use the Document AI Python client
Now install the Python Google Cloud client libraries into the VM Instance.

Enter the following command in the SSH terminal shell to import the lab files into your VM Instance:
gsutil cp gs://cloud-training/gsp924/synchronous_doc_ai.py .
Copied!
Enter the following command to install the Python client libraries required for Document AI and the other libraries required for this lab:
sudo apt install python3-pip
python3 -m pip install --upgrade google-cloud-documentai google-cloud-storage prettytable
Copied!
You should see output indicating that the libraries have been installed successfully.

Review the Document AI API Python code
Take a minute to review the Python code in the sample file. You can use an editor of your choice, such as vi or nano, to review the code in the SSH session or you can use the command from the previous section to copy the example code into the Cloud Shell and use the Code Editor to view the source code if you prefer.

The first two code blocks import the required libraries and parses parameters to initialize variables that identify the Document AI processor and input data.
import argparse
from google.cloud import documentai_v1beta3 as documentai
from google.cloud import storage
from prettytable import PrettyTable

parser = argparse.ArgumentParser() parser.add_argument("-P", "--project_id", help="Google Cloud Project ID") parser.add_argument("-D", "--processor_id", help="Document AI Processor ID") parser.add_argument("-F", "--file_name", help="Input file name", default="form.pdf") parser.add_argument("-L", "--location", help="Processor Location", default="us") args = parser.parse_args() 
The process_document function is used to make a synchronous call to a Document AI processor. The function creates a Document AI API client object.
The processor name required by the API call is created using the project_id,location, and processor_id parameters and the document to be processed is read in and stored in a mime_type structure.

The processor name and the document are then passed to the Document API client object and a synchronous call to the API is made. If the request is successful the document object that is returned will include properties that contain the data that has been detected by the Document AI processor.

def process_document(project_id, location, processor_id, file_path ):

    # Instantiates a client
    client = documentai.DocumentProcessorServiceClient()

    # The full resource name of the processor, e.g.:
    # projects/project-id/locations/location/processor/processor-id
    # You must create new processors in the Cloud Console first
    name = f"projects/{project_id}/locations/{location}/processors/{processor_id}"

    # Read the file into memory
    with open(file_path, "rb") as image:
        image_content = image.read()

    # Create the document object
    document = {"content": image_content, "mime_type": "application/pdf"}

    # Configure the process request
    request = {"name": name, "document": document}

    # Use the Document AI client synchronous endpoint to process the request
    result = client.process_document(request=request)

    return result.document
The script then calls the process_document function with the required parameters and saves the response in the document variable.
document = process_document(args.project_id,args.location,args.processor_id,args.file_name )
The final block of code prints the .text property that contains all of the text detected in the document then displays the form information using the text anchor data for each of the form fields detected by the form parser.
print("Document processing complete.")
# print the raw text
print("Text: \n{}\n".format(document.text))

# Define a function to retrieve an object dictionary for a named element
def get_text(doc_element: dict, document: dict):
    """
    Document AI identifies form fields by their offsets
    in document text. This function converts offsets
    to text snippets.
    """
    response = ""
    # If a text segment spans several lines, it will
    # be stored in different text segments.
    for segment in doc_element.text_anchor.text_segments:
        start_index = (
            int(segment.start_index)
            if segment in doc_element.text_anchor.text_segments
            else 0
        )
        end_index = int(segment.end_index)
        response += document.text[start_index:end_index]
    return response

# Grab each key/value pair and their corresponding confidence scores.
document_pages = document.pages

print("Form data detected:\n")
# For each page fetch each form field and display fieldname, value and confidence scores
for page in document_pages:
    print("Page Number:{}".format(page.page_number))
    for form_field in page.form_fields:
        fieldName=get_text(form_field.field_name,document)
        nameConfidence = round(form_field.field_name.confidence,4)
        fieldValue = get_text(form_field.field_value,document)
        valueConfidence = round(form_field.field_value.confidence,4)
        print(fieldName+fieldValue +"  (Confidence Scores: (Name) "+str(nameConfidence)+", (Value) "+str(valueConfidence)+")\n")
Click Check my progress to verify the objectives.

Test a Document AI form processor
Task 6. Run the Document AI Python code
Execute the sample code and process the same file as before.

Create environment variables for the Project ID and the IAM service account credentials file:
export PROJECT_ID=$(gcloud config get-value core/project)
export GOOGLE_APPLICATION_CREDENTIALS="$PWD/key.json"
Copied!
Call the synchronous_doc_ai.py python program with the parameters it requires:
python3 synchronous_doc_ai.py \
--project_id=$PROJECT_ID \
--processor_id=$PROCESSOR_ID \
--location=us \
--file_name=health-intake-form.pdf | tee results.txt
Copied!
You will see the following block of text output:
 FakeDoc M.D. HEALTH INTAKE FORM Please fill out the questionnaire carefully. The information you provide will be used to complete your health profile and will be kept confidential. Date: Sally Walker Name: 9/14/19 ... 

The first block of text is a single text element containing all of the text in the document. This block of text does not include any awareness of form based data so some items, such as the Date and Name entries, are mixed together in this raw text value.

The code then outputs a more structured view of the data using the form data that the form-parser has inferred from the document structure. This structured output also includes the confidence score for the form field names and values. The output from this section gives a much more useful mapping between the form field names and the values, as can be seen with the link between the Date and Name form fields and their correct values.

Form data detected:

Page Number:1 Phone #: (906) 917-3486 (Confidence Scores: (Name) 1.0, (Value) 1.0) ... Date: 9/14/19 (Confidence Scores: (Name) 0.9999, (Value) 0.9999) ... Name: Sally Walker (Confidence Scores: (Name) 0.9973, (Value) 0.9973) ... 
Click Check my progress to verify the objectives.

Run the Document AI Python code
Congratulations!
You've successfully used the Document AI API to extract data from documents using a general form processor. In this lab, you created and tested a Document AI processor using the console and the command line, and made Document AI synchronous API calls using Python.

Next steps/ Learn more
Read more in the Cloud Document AI API documentation.
Google Cloud training and certifica