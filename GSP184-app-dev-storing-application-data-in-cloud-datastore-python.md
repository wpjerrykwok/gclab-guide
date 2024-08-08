App Dev: Storing Application Data in Cloud Datastore - Python experiment Lab schedule 50 minutes universal_currency_alt No cost show_chart Intermediate info This lab may incorporate AI tools to support your learning. GSP184 Google Cloud self-paced labs logo

Overview Google Cloud Datastore is a NoSQL document database built for automatic scaling, high performance, and ease of application development. In this lab, you use Datastore to store application data for an online Quiz application. You also configure the application to retrieve from Datastore and display the data in the quiz.

The Quiz application skeleton has already been written. You clone the repository that contains the skeleton using Google Cloud Shell, review the code using the Cloud Shell editor, and view it using the Cloud Shell web preview feature. You then modify the code that stores data to use Cloud Datastore.

Objectives In this lab, you learn how to perform the following tasks:

Harness Cloud Shell as your development environment Preview the application Update the application code to integrate Cloud Datastore Setup and requirements Before you click the Start Lab button Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources will be made available to you.

This hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended). Note: Use an Incognito or private browser window to run this lab. This prevents any conflicts between your personal account and the Student account, which may cause extra charges incurred to your personal account. Time to complete the lab---remember, once you start, you cannot pause a lab. Note: If you already have your own personal Google Cloud account or project, do not use it for this lab to avoid extra charges to your account. How to start your lab and sign in to the Google Cloud console Click the Start Lab button. If you need to pay for the lab, a pop-up opens for you to select your payment method. On the left is the Lab Details panel with the following:

The Open Google Cloud console button Time remaining The temporary credentials that you must use for this lab Other information, if needed, to step through this lab Click Open Google Cloud console (or right-click and select Open Link in Incognito Window if you are running the Chrome browser).

The lab spins up resources, and then opens another tab that shows the Sign in page.

Tip: Arrange the tabs in separate windows, side-by-side.

Note: If you see the Choose an account dialog, click Use Another Account. If necessary, copy the Username below and paste it into the Sign in dialog.

student-04-eb21f607695f@qwiklabs.net Copied! You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

1HA5hiIgrRZt Copied! You can also find the Password in the Lab Details panel.

Click Next.

Important: You must use the credentials the lab provides you. Do not use your Google Cloud account credentials. Note: Using your own Google Cloud account for this lab may incur extra charges. Click through the subsequent pages:

Accept the terms and conditions. Do not add recovery options or two-factor authentication (because this is a temporary account). Do not sign up for free trials. After a few moments, the Google Cloud console opens in this tab.

Note: To view a menu with a list of Google Cloud products and services, click the Navigation menu at the top-left. Navigation menu icon Activate Cloud Shell Cloud Shell is a virtual machine that is loaded with development tools. It offers a persistent 5GB home directory and runs on the Google Cloud. Cloud Shell provides command-line access to your Google Cloud resources.

Click Activate Cloud Shell Activate Cloud Shell icon at the top of the Google Cloud console. When you are connected, you are already authenticated, and the project is set to your Project_ID, qwiklabs-gcp-00-2b38bb7ad73f. The output contains a line that declares the Project_ID for this session:

Your Cloud Platform project in this session is set to qwiklabs-gcp-00-2b38bb7ad73f gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

(Optional) You can list the active account name with this command: gcloud auth list Copied! Click Authorize. Output:

ACTIVE: * ACCOUNT: student-04-eb21f607695f@qwiklabs.net

To set the active account, run: $ gcloud config set account ACCOUNT (Optional) You can list the project ID with this command: gcloud config list project Copied! Output:

[core] project = qwiklabs-gcp-00-2b38bb7ad73f Note: For full documentation of gcloud, in Google Cloud, refer to the gcloud CLI overview guide. Launch the Cloud Shell code editor From Cloud Shell, click the Open Editor icon to launch the code editor. You may need to click on Open In New Window. The Open Editor button.

Note: The code editor launches in a separate tab of your browser. Task 1. Create a virtual environment Click on the Open Terminal icon. Python virtual environments are used to isolate package installation from the system.

virtualenv -p python3 vrenv Copied! Activate the virtual environment: source vrenv/bin/activate Copied! Task 2. Prepare the Quiz application The repository containing the Quiz application is located on GitHub.com. In this section, you use Cloud Shell to enter commands that clone the repository and run the application.

Clone source code in Cloud Shell Clone the repository for the class: git clone https://github.com/GoogleCloudPlatform/training-data-analyst Copied! Configure and run the Quiz application Change the working directory:

cd ~/training-data-analyst/courses/developingapps/python/datastore/start Copied! Export an environment variable, GCLOUD_PROJECT that references the Project ID:

export GCLOUD_PROJECT=$DEVSHELL_PROJECT_ID Copied! Note: Project ID in Cloud Shell While working in Cloud Shell, you will have access to the Project ID in the $DEVSHELL_PROJECT_ID environment variable. Install the application dependencies and ignore the incompatibility warnings:

pip install -r requirements.txt Copied! Run the application:

python run_server.py Copied! The application is running when you see a message similar to the following:

Running on http://127.0.0.1:8080/ (Press CTRL+C to quit)
Restarting with stat
Debugger is active!
Debugger PIN: 179-313-240 Review the Quiz application In Cloud Shell, click Web preview > Preview on port 8080 to preview the quiz application.
The option 'Preview on port 8080' highlighted within the web preview menu.

You should see the user interface for the web application. The three main parts to the application are:

Create Question Take Test Leaderboard The Welcome to the Quite Interesting Quiz page displaying three parts: Create Question, Take Test, and Leaderboard.

In the navigation bar, click Create Question.

You should see a simple form that contains textboxes for the question and answers with radio buttons to select the correct answer.

Note: Quiz authors can add questions in this part of the application. This part of the application is written as a server-side web application using the popular Python web application framework Flask. In the navigation bar, click Take Test, and then GCP to access the Google Cloud questions.

You should see a sample question.

The sample question template, along with the sample answers and the Submit Answer button.

Quiz takers answer questions in this part of the application.

Note: This part of the application is written as a client-side web application. To return to the server-side application, click on the Quite Interesting Quiz link in the navigation bar.

Task 3. Examine the Quiz application code In this lab, you'll view and edit files. You can use the shell editors that are installed on Cloud Shell, such as nano or vim or the Cloud Shell code editor.

This lab uses the Cloud Shell code editor to review the Quiz application code.

Review the Flask Web application Navigate to the /training-data-analyst/courses/developingapps/python/datastore/start folder using the file browser panel on the left side of the editor. Note: Paths will be relative to this folder. This application is a standard Python application written using the popular Flask application framework. Select the ...run_server.py file.

This file contains the entrypoint for the application, and runs it on port 8080.

Select the ...quiz/init.py file.

This file imports routes for the web application and REST API.

Select the ...quiz/webapp/questions.py and ...quiz/webapp/routes.py file.

These files contain the routes that map URIs to handlers that display the form and collect form data posted by quiz authors in the web application.

Select the ...quiz/webapp/templates folder.

This folder contains templates for the web application user interface using Jinja2 templates.

View the ...quiz/webapp/templates/add.html file.

This file contains the Jinja2 template for the Create Question form.

Notice how there is a select list to pick a quiz, textboxes where an author can enter the question and answers, and radio buttons to select the correct answer.

Select the ...quiz/api/api.py file.

This file contains the handler that sends JSON data to students taking a test.

Select the ...quiz/gcp/datastore.py file.

This is the file where you write Datastore code to save and load quiz questions to and from Cloud Datastore.

This module will be imported into the web application and API.

Task 4. Adding entities to Cloud Datastore In this section, you write code to save form data in Cloud Datastore.

Note: Update code within the sections marked as follows:

TODO
END TODO
To maximize your learning, try to write the code without reference to the completed code block at the end of the section. In addition, review the code, inline comments, and related Datastore Client API documentation for Cloud Datastore. Create an App Engine application to provision Cloud Datastore Return to Cloud Shell and stop the application by pressing Ctrl+c.

To create an App Engine application in your project, use:

gcloud app create --region "europe-west4" Copied! You'll see this message when the App Engine has been created:

Creating App Engine application in project [qwiklabs-gcp-f67238775c00cfaa] and region europe-west4....done. Success! The app is now created. Please use gcloud app deploy to deploy your first app. Note: You aren't using App Engine for your web application yet. However, Cloud Datastore requires you to create an App Engine application in your project. Click Check my progress below to check your lab progress.

Create an App Engine application Import and use the Python Datastore module Open the ...quiz/gcp/datastore.py file in the Cloud Shell editor and add the Updated datastore.py code listed below to perform the following:

Import the os module.

Use the os module to get the GCLOUD_PROJECT environment variable.

Import the datastore module from the google.cloud package.

Declare a datastore.Client client object named datastore_client.

Updated datastore.py

TODO: Import the os module
import os

END TODO
TODO: Get the GCLOUD_PROJECT environment variable
project_id = os.getenv('GCLOUD_PROJECT')

END TODO
from flask import current_app

TODO: Import the datastore module from the google.cloud package
from google.cloud import datastore

END TODO
TODO: Create a Cloud Datastore client object
The datastore client object requires the Project ID.
Pass through the Project ID you looked up from the
environment variable earlier
datastore_client = datastore.Client(project_id)

END TODO
Copied! Write code to create a Cloud Datastore entity Still in ...quiz/gcp/datastore.py, move to the save_question() function and remove the existing pass placeholder statement. Add the following datastore.py - save_question() function code listed below to perform the following: Use the Datastore client object to create a key for a Datastore entity whose kind is 'Question'. Use Datastore to create a Datastore question entity with the key. Iterate over the items in the dictionary of values supplied from the Web application form. In the body of the loop, assign each key and value to the Datastore entity object. Use the Datastore client to save the data. datastore.py - save_question() function

""" Create and persist and entity for each question The Datastore key is the equivalent of a primary key in a relational database. There are two main ways of writing a key:

Specify the kind, and let Datastore generate a unique numeric id
Specify the kind and a unique string id """ def save_question(question):
TODO: Create a key for a Datastore entity
whose kind is Question
key = datastore_client.key('Question')
END TODO
TODO: Create a Datastore entity object using the key
q_entity = datastore.Entity(key=key)
END TODO
TODO: Iterate over the form values supplied to the function
for q_prop, q_val in question.items():
END TODO
TODO: Assign each key and value to the Datastore entity
    q_entity[q_prop] = q_val
END TODO
TODO: Save the entity
datastore_client.put(q_entity)
END TODO
Copied! Save datastore.py. Run the application and create a Cloud Datastore entity Save the ...quiz/gcp/datastore.py file and then return to the Cloud Shell command prompt. To run the application, execute the following command: python run_server.py Copied! In Cloud Shell, click Web preview > Preview on port 8080 to preview the quiz application. Click Create Question. Complete the form with the following values, and then click Save. Form Field

Value

Author

Your Name

Quiz

Google Cloud Platform

Title

Which company owns Google Cloud?

Answer 1

Amazon

Answer 2

Google (select the Answer 2 radio button!)

Answer 3

IBM

Answer 4

Microsoft

You should return to the application home page.

Return to the Console, click Navigation menu > Datastore. Select default database and click Entities. You should see your new question!

Click Check my progress below to check your lab progress.

Create a Cloud Datastore entity Retrieve Cloud Datastore entities In this section, you write code to retrieve entity data from Cloud Datastore to view your question in the application.

Write code to retrieve Cloud Datastore entities In the code editor, in the ...quiz/gcp/datastore.py file, remove the code for the list_entities(quiz, redact) function and replace it with a query that: Retrieves Question entities for a specific quiz from Cloud Datastore. Uses the Datastore client to fetch the query, and uses the returned data to create a list. Enumerate the list of items, and promote each entity's Key identifier to a top level property. Return the results. Replace this code: """ Returns a list of question entities for a given quiz

filter by quiz name, defaulting to gcp
no paging
add in the entity key as the id property
if redact is true, remove the correctAnswer property from each entity """ def list_entities(quiz='gcp', redact=True): return [{'quiz':'gcp', 'title':'Sample question', 'answer1': 'A', 'answer2': 'B', 'answer3': 'C', 'answer4': 'D', 'correctAnswer': 1, 'author': 'Nigel'}]
""" Copied! With this code:

""" Returns a list of question entities for a given quiz

filter by quiz name, defaulting to gcp
no paging
add in the entity key as the id property
if redact is true, remove the correctAnswer property from each entity """ def list_entities(quiz='gcp', redact=True): query = datastore_client.query(kind='Question') query.add_filter('quiz', '=', quiz) results =list(query.fetch()) for result in results: result['id'] = result.key.id if redact: for result in results: del result['correctAnswer'] return results """ Copied! Save datastore.py. Run the application and test the Cloud Datastore query Now to test if your question is retrieved from Datastore and loaded into your Quiz application.
In Cloud Shell, press Ctrl+c to stop the application, then restart the application: python run_server.py Copied! Preview the quiz: If the browser running the quiz is still open, reload the browser. Otherwise, click Web preview > Preview on port 8080.

Click Take Test > GCP.

You should see the questions you created.

The quiz question, along with a list of possible answers and the Submit Answer button.

Congratulations! This concludes the self-paced lab, App Dev: Storing Application Data in Cloud Datastore - Python. You used Datastore to store application data for an online Quiz application. You also configured the application to retrieve and display the data in the quiz.