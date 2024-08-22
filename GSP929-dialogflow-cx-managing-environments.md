Dialogflow CX: Managing Environments
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
GSP929
Google Cloud self-paced labs logo

Overview
Most businesses go through different phases of project development and production cycles. Effective maintainance of these projects require systems and processes to manage versions and environments. Dialogflow provides tools within the UI for managing multiple versions and loading specific versions to dedicated environments, which allows usage for different purposes (and perhaps by different teams). In this lab you'll explore the management of Dialogflow versions and environments.

The following are definitions of versions and environments with respect to Dialogflow.

Versions: at the end of the development cycle, it's customary to freeze your virtual agent so it can go through a QA cycle and presumably to production later. Each time you freeze your agent, you'll create a version of it, complete with version number and description.
Environments: during the development cycle, you may have different teams needing to access different versions, and certainly for production you'll have a QA'd version that doesn't change until the next update. If your business is larger, you may even have independent divisions of the business with their own virtual agents. You can create different environments and load the version each team needs to access. Note that by default there is a working environment, Draft, where all changes are made to an agent using the Dialogflow user interface. To load a virtual agent you've modified in the Draft environment to another environment, you'll need to first establish a version of it.
Objectives
By the end of this lab, you will be able to:

Create versions of your virtual agent.
Create environments where your virtual agent will be published.
Load a saved version of your virtual agent to an environment.
Change which version is loaded to an environment.
Prerequisites
This lab uses the basic Flight Booker agent developed in Dialogflow CX: Bot Building Basics and assumes basic knowledge of Dialogflow CX such as using the Test Simulator and how intents, flows, and pages work.

Setup
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

student-01-bd37c67a34bb@qwiklabs.net
Copied!
You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

Zte9CdOVRyCJ
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
Task 1. Getting started with Dialogflow CX
First, get logged in to Dialogflow CX and create a new agent.

Name your virtual agent 'Flight Booker - Env Mgt' when you get to that point in the steps.

Assumption: You've already logged into Google Cloud before continuing with the steps below.

In an new incognito window, navigate to Dialogflow.

Click on Sign-in with Google.

Select the student account that you started the lab with.

Next, you explicitly specify that you want to use Dialogflow CX instead of Dialogflow ES.

In the menu bar at the left, click on Dialogflow CX.

A new page for Dialogflow CX opens and a blue Dialogflow icon appears. On this page, you should see a pop-up asking you to select a project.

Search the list in the pop-up for the project that matches your assigned Project ID for this lab. Click on your project ID.

Note: If you don't see your Project ID listed, look at the user on the right side to confirm that you are using Dialogflow CX as "student".
Dialogflow CX title bar highlighting the project box and the user avatar

You will now see a page telling you "To use Dialogflow CX with this project, enable the following APIs".

Click on Enable API.

It shouldn't take more than half a minute or so for this activity to complete.

If it seems this task is taking a long time, refresh the page.

Once complete, you will be on the Dialogflow CX Agents page.

Click on Create agent.

If prompted with Get started with Dialogflow CX click Build your own.

Enter a name for the agent (e.g., "Cloudio-cx").

Set the location to us-central1.

Ensure timezone and default language are set appropriately.

Click Create.

Once the agent is created, you will see the design and configuration portion of the Dialogflow CX UI.

After creating the agent, navigate to Agent Settings > General > Logging settings and click on Enable Cloud Logging and Enable conversation history option. It will generate logs for this agent.

Click Save.

The Agent Settings page, wherein the checked Enable stackdriver logging box is highlighted.
Click Check my progress below to verify you're on track in this lab.

Getting started with Dialogflow CX
Task 2. Importing a .blob virtual agent file
Import the virtual agent from the earlier lab, Dialogflow CX: Bot Building Basics, and use it to explore the environment functionality:

Click the following link to download the sample lab 1 virtual agent solution, gsp929-start-agent, to your local hard drive.

Select View all agents in the Agent dropdown menu at the top of the Dialogflow CX UI.

Click the context menu (three vertical dots More icon) to the right of your virtual agent.

Select Restore from the expanded menu options.

Select the Upload radio button.

Click on select file.

Navigate to and select the gsp929-start-agent.blob that you downloaded to your hard drive.

Click Open.

Click Restore.

Refer to the Dialogflow CX 'restore' documentation as needed.

Now you have a virtual agent that has everything completed from the earlier lab.

Click Check my progress below to verify you're on track in this lab.
Importing a .blob virtual agent file

Task 3. Testing in the Draft environment
Click on Test Agent in the upper right to open the simulator pane.

Notice there is an Environment dropdown.

By default, you should see Draft in this dropdown because you haven't yet created any other environment.

Notice there is a second dropdown for Flow.

By default, you should see Flow in this dropdown because no other flows exist. You could select Default Start Flow, but it isn't necessary at this point.

In the Talk to agent box, type "i want to book a flight".

The next response from the agent should be, 'What city would you like the flight to depart from?'

Notice the data at the top of the conversation that shows Environment: Draft and Flow: Default Start Flow.

You may have noticed some of this while completing the earlier lab. Now you'll need to pay closer attention to what's selected in the dropdowns as you move forward through this lab.

Click Check my progress below to verify you're on track in this lab.
Testing in the Draft environment

Task 4. Creating environments
Click on the Manage tab in the main menu.

Select Environments on the left side.

Click + Create to create a new environment.

Enter 'QA' for the Display Name.

Click Save.

Note: You'll see a message saying 'Start Flow' is not included in the environment. Why do you think this is?
Up to this point, you haven't yet created a published version of the Default Start Flow, or any other flow for that matter. Notice under Version next to the Default Start Flow that the only item in the dropdown list is 'Not published'.
Dialogflow needs at least one Start Flow to be published in an environment. Once you publish a version of your agent (which includes a Start Flow), Dialogflow will no longer throw this error.
Click x to dismiss the environment creation error message.

Select Versions in the main menu.

Click on Default Start Flow.

Click + Create to create a version of the flow.

For the Display name field enter 'Flight booker main v1 chat bot' .

Enter a description in the description box on what's included in this version of the virtual agent. For instance, you could enter 'Version 1 includes functionality to retrieve ticketing to/from and travel dates.'

Click Save.

You should now see the Default Start Flow in the Versions list with the # versions, equal to 1.

Click on Default Start Flow under Flow to view additional details about the version.

Notice whether the status shows Not ready or Ready.

Select Environments to reattempt configuring a new environment.

Click + Create.

Enter 'QA' for the Display Name.

In the Flow section, choose Flight booker main v1 chat bot from the Version dropdown list next to the Default Start Flow. Recall that this didn't exist in the dropdown earlier.

Click Save.

Note: You may see a message such as:
Version 'projects/qwiklabs-gcp-03-407df58d36b0/locations/us-central1//agents/e2779218-b813-4844-a0ea-ec2ef504636d/flows/00000000-0000-0000-0000-000000000000/versions/1' is not ready to serve because its training is RUNNING. Wait for training to finish or fix the version if its training failed.
This is caused when Dialogflow is still capturing and training your versioned agent. Do you remember if the status of the version creation showed Not ready? Try saving again after waiting a few moments for the version creation status to change to Ready.
You should now see your new QA environment in the list with a Last modified date.

Task 5. Testing in your new environment
Next, you can test out your versioned virtual agent in the environment you created.

Click on Test Agent in the upper right to open the simulator pane.

Select QA from the Environment dropdown.

Note: This step is important in order to test the specific version in a specific environment. Otherwise, you could be testing something you weren't expecting.
Click the "Reset" button in the Test Agent pane to clear the simulator from previous tests.

In the Talk to agent box, enter 'i want to book a flight'.

Notice the information at the top of the conversation changed slightly in that it now shows Environment: QA.

This is one of the ways you can run test cases in different environments. However, you may be thinking, if the version loaded to QA is the same as what's in Draft, testing will produce the same results. You're right. Next you'll make a change that makes it more obvious.

Click Check my progress below to verify you're on track in this lab.
Testing in QA environment

Task 6. Creating an additional version
Next you'll make a change to your virtual agent, save it as a new version, and load it to a new environment.

Click on the Build tab from the main menu.

Click on the Ticket information page.

Click on Edit fulfillment to edit the entry fulfillment information.

Add a new prompt in the Agent says box saying, 'I'll be happy to assist you with that.'

Click Save.

At this point, you've saved the change to your working draft. Next you'll create a new version of the virtual agent that includes this change.

Go back to Manage and Versions.

Click on the Default Start Flow to begin the process of creating a new version for it.

Repeat the versioning steps above to create a new version of your Default Start Flow called 'Flight booker main v2 chat bot'.

Add a description, such as 'Version 2 adds a friendly greeting before prompting for flight details'.

Click Save.

You should now see number of versions incremented to 2 for the Default Start Flow in the Versions list.

Task 7. Creating an additional environment
Repeat the steps you followed above to create a new environment called 'Dev' that uses the new version 2 of your Default Start Flow.

Select your Flight booker main v2 chat bot from the version dropdown.

Click Save.

Note: You may again get an error similar to the following, so just wait a few moments and try saving again.
Version 'projects/qwiklabs-gcp-00-fe6cab958249/locations/us-central1/agents/6792c492-5f79-4ccf-8f17-e757b34f38b9/flows/00000000-0000-0000-0000-000000000000/versions/2' is not ready to serve because its training is RUNNING. Wait for training to finish or fix the version if its training failed.
Now your latest version of your flow is loaded to the Dev environment.

Next you'll go back to the simulator to test your new Flight booker main v2 chat bot version.

Open the Test Agent pane if not open already.

Click the "Reset" button in the Test Agent pane to clear the simulator from previous tests.

From the Environment dropdown select Dev.

Type 'i want to book a flight' into the Talk to agent box.

You should get a response from the agent saying, I'll be happy to assist you with that., followed by a prompt for the departure city. This indicates that it's running the v2 version of your agent.

Click Check my progress below to verify you're on track in this lab.
Testing in Dev environment

Knowledge check (optional)

What must you do before you can deploy a virtual agent to an existing custom environment?

You must remove the version of the agent already loaded.

You must create another new custom environment.

You must create a new custom environment and select it in the dropdown of the Test Agent.

You must create a version of the virtual agent.

Task 8. Managing different environments
You've created two environments now and loaded a different version into each. What if you need to change the version loaded into one of those environments?

Click on Environments in the left pane as needed to get to the view of both of your environments, QA and Dev.

Click on the QA environment.

Select Flight booker main v2 chat bot from the version dropdown.

Click Save.

Notice the Last modified time for the version.

Use the test simulator to ensure the version two is loaded. (Recall that you added the friendly greeting to this one.) Don't forget to click "Reset" to begin a fresh test scenario and choose QA in the environment dropdown.

Go back and load Flight booker main v1 chat bot to the QA environment.

Retest. Was it what you expected? You should no longer see the friendly greeting prior to the prompt for departure city.

Discuss your working environment - What version of your agent do you suppose would be tested if you ran the test simulator using the Draft environment? You guessed it! The working copy that you've recently saved or uploaded. You can test the working copy of your virtual agent in Draft until you're ready to create a version of it. At that point, you could save it to another environment so that another team can begin testing that frozen version.

By selecting a specific environment, the test is running the version of your virtual agent loaded to the specified environment (which may be different from what you're currently working on in Dialogflow in Draft mode). Another benefit is you can test different versions without going through the process of retraining the model (which can take some time for larger, more complex virtual agents).
Check your knowledge

When you see the message, 'Start Flow' is not included in the environment, what does it mean?

You have already loaded another version to the environment.

You cannot create a custom environment because there are no existing versions that could be deployed to it (Dialogflow needs at least one Start Flow).

You cannot create a new version because there are no environments where it can be deployed.

Its just a warning, not an error. You can ignore it.


When you want to test the Default Start Flow for a version of your agent in a specific environment, you must do what at minimum? Choose the most accurate answer.

Specify which flow you mean to test.

Specify the environment and flow you mean to test.

Specify which environment you mean to test in.

Specify the environment, flow, and intents you mean to test.

Before you sign out, you can to export your virtual agent if you want. Recall that this is done through the following general steps:

Select View all agents from the Agent dropdown at the top.

Click on the context menu (three vertical dots) and choose Export.

Click on the Download radio button.

Click Export.

Congratulations!
Now you can manage different versions of a virtual agent and run tests in different environments.

Google Cloud training and certification
...helps you make the most of Google Cloud technologies. Our classes include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level trainin