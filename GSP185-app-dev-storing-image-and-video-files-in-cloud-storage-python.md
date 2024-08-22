# App Dev: Storing Image and Video Files in Cloud Storage - Python

## GSP185

### Overview

Cloud Storage allows world-wide storage and retrieval of any amount of data at any time. You can use Cloud Storage for a range of scenarios including serving website content, storing data for archival and disaster recovery, or distributing large data objects to users via direct download.

In this lab you'll configure an application to use Cloud Storage to store and retrieve application data. The application is an online Quiz, the data is the form data, including an image you upload from your local machine.

Objectives
In this lab, you learn how to perform the following tasks:

Set up Cloud Shell as your development environment
Update the application code to integrate Cloud Datastore
Use the Quiz application to upload an image file into Cloud Storage and view the image in the Quiz
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

student-01-e21281e9633b@qwiklabs.net
Copied!
You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

egjxHuOPxbIZ
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
When you are connected, you are already authenticated, and the project is set to your Project_ID, qwiklabs-gcp-03-881580eb141d. The output contains a line that declares the Project_ID for this session:

Your Cloud Platform project in this session is set to qwiklabs-gcp-03-881580eb141d
gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

(Optional) You can list the active account name with this command:
gcloud auth list
Copied!
Click Authorize.
Output:

ACTIVE: *
ACCOUNT: student-01-e21281e9633b@qwiklabs.net

To set the active account, run:
    $ gcloud config set account `ACCOUNT`
(Optional) You can list the project ID with this command:
gcloud config list project
Copied!
Output:

[core]
project = qwiklabs-gcp-03-881580eb141d
Note: For full documentation of gcloud, in Google Cloud, refer to the gcloud CLI overview guide.
Launch the Cloud Shell code editor
From Cloud Shell, click Open Editor to launch the code editor.
The Open Editor button.

Note: The code editor launches in a separate tab of your browser, along with Cloud Shell.
Run the following command to configure your Project ID, replacing YOUR-PROJECT-ID with your Project ID:
gcloud config set project <YOUR-PROJECT-ID>
Copied!
Task 1. Prepare the quiz application
In this section, you access Cloud Shell, clone the git repository containing the Quiz application, and run the application.

Clone source code in Cloud Shell
To clone the repository for the class, execute the following command:
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
Copied!
Configure and run the quiz application
Change the working directory:

cd ~/training-data-analyst/courses/developingapps/python/cloudstorage/start
Copied!
Set the region using a variable:

REGION=us-central1
sed -i s/us-central/$REGION/g prepare_environment.sh
Copied!
Configure the application:

. prepare_environment.sh
Copied!
Note: Ignore any warnings.
This script file:

Creates an App Engine application.
Exports an environment variable, GCLOUD_PROJECT.
Updates pip, then runs pip install -r requirements.txt.
Creates entities in Cloud Datastore.
Prints out the Project ID.
Run the application:

python run_server.py
Copied!
The application is running when you see the following output:

* Running on http://127.0.0.1:8080/ (Press CTRL+C to quit)
* Restarting with stat
* Debugger is active!
* Debugger PIN: 502-577-323
Review the quiz application
To view the application, click Web preview > Preview on port 8080.

Click the Create Question link in the toolbar.

You should see a simple form that contains textboxes for the question and answers and radio buttons to select the correct answer

The Add question form, wherein the Choose File button is highlighted within the Image category.

Note: The form has a new file upload field that will be used to upload either image or video files. In this lab you upload an image file; you use the same process to upload video file.
Task 2. Examine the quiz application code
In this section, you review the case study application code.

In this lab you'll view and edit files. You can use the shell editors that are installed on Cloud Shell, such as nano or vim or the Cloud Shell code editor.

This lab uses the Cloud Shell code editor to review the Quiz application code.

Examine the application code
Navigate to the /training-data-analyst/courses/developingapps/python/cloudstorage/start folder using the file browser panel on the left side of the editor.

Select the add.html file in the ...quiz/webapp/templates/ folder.

This file contains the template for the Create Question form.

Notice how the form has been modified to use multipart/form-data as the enc-type, and there are two new form controls:

A file upload control called image
A hidden field called imageUrl
Select the routes.py file in the ...quiz/webapp folder.

This file contains the route for the POST handler that receives the form data. It has been modified to get the image file from the form.

Select the questions.py file in the ...quiz/webapp folder.

This file contains the handler that processes the form data extracted in the routes.py file. You will modify this file to use a new module that is a client for Cloud Storage.

Select the ...quiz/gcp/storage.py file.

This is the file where you will write code to save image file data into Cloud Storage.

Task 3. Create a Cloud Storage Bucket
In this section, you create a Cloud Storage bucket and export an environment variable that references it.

Return to the Cloud Shell command line.

Stop the application by pressing Ctrl+C.

Create a Cloud Storage bucket named <Project ID>-media:

gsutil mb gs://$DEVSHELL_PROJECT_ID-media
Copied!
You can create a bucket using the gsutil mb command, passing through the name of the bucket as gs://BUCKET_NAME

You can use $DEVSHELL_PROJECT_ID as the bucket name prefix followed by -media

To export the Cloud Storage bucket name as an environment variable named GCLOUD_BUCKET, execute the following command:

export GCLOUD_BUCKET=$DEVSHELL_PROJECT_ID-media
Copied!
Note: Recall that the application makes use of environment variables for configuration. This allows the development team to deploy the application into development, test, staging, and production just by changing these variables.
Click Check my progress to verify the objective.

Create a bucket
Task 4. Adding objects to Cloud Storage
In this section, you write code to save uploaded files into Cloud Storage.

Note: Update code within the sections marked as follows:
# TODO
# END TODO
To maximize your learning, review the code, inline comments, and related API documentation.
For information on API documentation for Cloud Storage, refer to the APIs and reference reference.

Import and use the Python Cloud Storage module
In code editor, move to the top of the ...quiz/gcp/storage.py file.
Get the bucket name from the GCLOUD_BUCKET environment variable.
Import the storage module from the google.client package.
Create a Cloud Storage client.
Get a reference to the Cloud Storage bucket.
quiz/gcp/storage.py

# TODO: Get the Bucket name from the
# GCLOUD_BUCKET environment variable

bucket_name = os.getenv('GCLOUD_BUCKET')

# END TODO

# TODO: Import the storage module

from google.cloud import storage

# END TODO

# TODO: Create a client for Cloud Storage

storage_client = storage.Client()

# END TODO

# TODO: Use the client to get the Cloud Storage bucket

bucket = storage_client.get_bucket(bucket_name)

# END TODO
Copied!
Write code to send a file to Cloud Storage
Still in storage.py, in the the upload_file(...) function, remove the existing pass statement, then use the Cloud Storage client to upload a file to your Cloud Storage bucket and make it publicly available.
Get a reference to a Cloud Storage blob object in the bucket.
Use the blob object to upload the image.
Make the file public.
Return the blob's public URL.
quiz/gcp/storage.py - upload)file(...) function

"""
Uploads a file to a given Cloud Storage bucket and returns the public url
to the new object.
"""
def upload_file(image_file, public):

    # TODO: Use the bucket to get a blob object

    blob = bucket.blob(image_file.filename)

    # END TODO

    # TODO: Use the blob to upload the file

    blob.upload_from_string(
        image_file.read(),
        content_type=image_file.content_type)

    # END TODO

    # TODO: Make the object public

    if public:
        blob.make_public()

    # END TODO


    # TODO: Modify to return the blob's Public URL

    return blob.public_url

    # END TODO
Copied!
Save storage.py.
Write code to use the Cloud Storage functionality
In the editor, move to the top of the ...quiz/webapp/questions.py file.
Modify the import statement to use your storage client as well as the datastore client.
Move to the upload_file(...) function. Use your storage client to upload a file, and assign the returned public URL to a variable.
Modify the return statement to return the public URL.
Move to the save_question(...) function. Write an if test to see if the image_file is present.
If it is, then call the upload_file(...) function, and assign the public URL to a entity property named imageUrl.
If not, then assign an empty string to the entity imageUrl property.
quiz/webapp/questions.py

# TODO: Import the storage module

from quiz.gcp import storage, datastore

# END TODO

"""
uploads file into google cloud storage
- upload file
- return public_url
"""
def upload_file(image_file, public):
    if not image_file:
        return None

    # TODO: Use the storage client to Upload the file
    # The second argument is a boolean

    public_url = storage.upload_file(
       image_file,
       public
    )

    # END TODO

    # TODO: Return the public URL
    # for the object

    return public_url

    # END TODO

"""
uploads file into google cloud storage
- call method to upload file (public=true)
- call datastore helper method to save question
"""
def save_question(data, image_file):

    # TODO: If there is an image file, then upload it
    # And assign the result to a new Datastore
    # property imageUrl
    # If there isn't, assign an empty string

    if image_file:
        data['imageUrl'] = str(
                  upload_file(image_file, True))
    else:
        data['imageUrl'] = u''

    # END TODO

    data['correctAnswer'] = int(data['correctAnswer'])
    datastore.save_question(data)
    return
Copied!
Save questions.py.
Run the application and create a Cloud Storage object
Save the ...gcp/storage.py and ...webapp/questions.py files, and then return to the Cloud Shell command.
Return to Cloud Shell to run the application:
python run_server.py
Copied!
Download an image file to your local machine from Google storage.

In Cloud Shell, click Web preview > Preview on port 8080 to preview the Quiz application.

Click the Create Question link.

Complete the form with the following values, and then click Save.

Form Field	Value
Author	Your name
Quiz	Google Cloud Platform
Title	Which product does this logo relate to?
Image	Upload the Google_Cloud_Storage_logo.png file you previously downloaded
Answer 1	App Engine
Answer 2	Cloud Storage (Select the Answer 2 radio button)
Answer 3	Compute Engine
Answer 4	Container Engine
Return to the Cloud Console and navigate to Navigation menu > Cloud Storage.

On the Cloud Storage > Browser page, click the correct bucket (named <Project ID>-media).

You should see your new object named Google_Cloud_Storage_logo.png.

Upload objects to your bucket
Run the client application and test the Cloud Storage public URL
Add /api/quizzes/gcp to the end of the application's URL.

You should see that JSON data has been returned to the client corresponding to the Question you added in the web application.

The imageUrl property should have a value corresponding to the object in Cloud Storage.

Return to the application home page and click the Take Test link.

Click GCP, and answer each question.

When you get to the question you just added, you should see the image has been formatted inside the client-side web application!

Congratulations!
This concluded the lab, App Dev: Storing Image and Video Files in Cloud Storage - Python. You used Cloud Storage to store and retrieve application data.
