Dialogflow CX: Parameter Manipulation
experiment
Lab
schedule
1 hour 10 minutes
universal_currency_alt
No cost
show_chart
Introductory
info
This lab may incorporate AI tools to support your learning.
GSP949
Google Cloud self-paced labs logo

Overview
As you start to give your virtual agent the ability to have more dynamic conversations, some of the more advanced features of Dialogflow CX can make your agent even more conversational. In this lab you'll learn how to use some advanced features of Dialogflow CX to enhance the conversational experience of your virtual agent. You'll learn how to add the ability for a user to check a flight's status through the virtual agent by providing a confirmation number and connect the 'Book a Flight' scenario into a new Anything else? page so that the agent is always ready to handle another request from the user.

Objectives
By the end of this lab, you will be able to:

Use regular expressions to do parameter validation (e.g., on a PNR Number).
Reset parameters to null when the user starts a new flow of conversation.
Prerequisites
This lab builds upon the basic Flight Booker agent developed in Dialogflow CX: Bot Building Basics and therefore assumes knowledge of Dialogflow CX elements such as intents, entities, training phrases, flows, and pages. Building upon these basics, this lab will implement more advanced conversational techniques using some advanced features of Dialogflow CX. Though taking the earlier lab first is recommended, proceed if you are already familiar with Dialogflow CX and its fundamental features and usage.

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

student-01-8f3bfee8cc00@qwiklabs.net
Copied!
You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

aJtMS3H9ann3
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
In this task, you'll get logged into Dialogflow CX and create a new agent.

Note: Name your virtual agent 'Flight Booker - Parameter Manipulation' when you get to that point in the steps.
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

Set the location to us-west1.

Ensure timezone and default language are set appropriately.

Click Create.

Once the agent is created, you will see the design and configuration portion of the Dialogflow CX UI.

Click Check my progress to verify the objective.
Create an Agent

Task 2. Importing a .blob virtual agent file
In this task, you will import a virtual agent from an earlier lab.

Select View all agents from the Agent dropdown menu at the top.

Click the context menu (three vertical dots) to the right of your virtual agent.

Select Restore from the expanded menu options.

Select the Cloud Storage radio button if not already selected.

Enter gs://spls/DialogflowCX_agents/gsp929-start-agent.blob for URI.

Click Restore.

Refer to the Dialogflow CX "restore" documentation as needed.

Now you have a virtual agent that has everything completed from the earlier lab.

Note: If you'd like to retain a copy of the sample agent, download gsp929-start-agent.blob to your local hard drive.
Click Check my progress to verify the objective.
Importing a virtual agent file

Check your knowledge

Why do you have to enable the Dialogflow API?

A service ticket will be created and a on-site engineer will manually provision a Dialogflow instance for you

The virtual agent needs to use webhook to invoke external APIs.

Most Google Cloud services in a new project needs to be explicitly enabled before you may gain access to it.

Verify the restored agent
Confirm the following pages and intents appear in your virtual agent:

Pages:

Start
Ticket information
Confirm trip
Intents:

Default Welcome Intent
Default Negative Intent
main.book_a_flight
confirmation.yes
confirmation.no
Task 3. Add functionality for checking flight status
Now you'll add the ability for a user to speak to your agent to check flight status. You'll add a custom regex entity to capture the user's booking reference, also known as the Passenger Name Record (PNR) number. Regex (Regular Expression) is chosen because it's easy to specify a fixed format for the PNR - the advantage is built-in error checking.

Create a custom Regex entity for booking reference (PNR Number)
Across the airline industry, a PNR is used to capture the basic information for a passenger and their flight details. Every PNR record has a unique reference identifier made up of alphanumeric (letters and numeric digits) characters and a length of five or six characters.

Naturally, when an end user provides a PNR number, you should validate that it conforms to the rules of a PNR number. Do this with a custom Regex entity.

Click on the Manage tab.

Click on Entity Types in the menu on the left.

Click + Create.

Enter PNR_Number in the Display name text box for the entity.

Check the Regexp entities box.

Enter ^[a-zA-Z0-9]{5,6}$ in the Add value text box under Entity.

What does this regex expression actually mean? The bracketed characters, [a-zA-Z0-9], mean that any letter regardless of case or digit will match. The {5,6} means the value must be five or six characters in length. The $ asserts that we are at the end of the string.

For instance, we may have a PNR of XCr942 or 1f38C.

There are lots of regex resources online if you need to construct something a little different for your needs.

Click Save.

Your entity should look like the following when you click on it:

The Entity type page displaying the highlighted entity value and selected Regexp entities checkbox

Create intents and pages for the flight status functionality
With the @PNR_Number custom entity created, we will now create the intents and pages that make up this flight status check conversational flow.

Click on Intents in the menu on the left.

Click + Create.

Enter main.flight_status in the Display name.

Add training phrases such as the following:

Phrases	Annotations
PNR K4P89E, what is the flight status?	K4P89E
My PNR is Z453K9, is it still on time?	Z453K9
Is my flight on time?	n/a
What is my flight status?	n/a
I would like to know if my flight is still on time	n/a
Check flight status	n/a
Note: The PNR_Number entities should be automatically slot-filled by Dialogflow. If they aren't, you can manually annotate them by selecting and choosing the @PNR_Number entity.
Ensure the PNR examples in the training phrases are annotated correctly to indicate @PNR_Number and NOT @sys.number (system entity):

You do this by selecting the text and, using the filter, search for "PNR".
Then select the correct entity (@PNR_Number). Make sure the whole thing is selected, not just a portion of it. This helps Dialogflow understand the format it should be looking for.
It should look like the following when you're done:

The Training phrases page with the highlighted PNR number

Notice PNR_Number listed in the parameter section at the bottom of the intent configuration page. If this were sensitive data, we might have chosen the Redact in log option.

Click Save.

Set up the Start page
Next, you'll update the Start page with a new route using the main.flight_status intent and create the subsequent pages to complete this new conversational flow. Adding intents and their routes in the Start page ensures a user's request is recognized and can trigger Dialogflow to react correctly from anywhere in the flow.

Click on Build to view the overall flow.

Click on the Start page.

Click on + to the right of Routes in the upper right.

Select main.flight_status from the Intent dropdown menu.

Scroll down to the Transition section.

Ensure that the Page radio button is enabled.

Select +new Page from the Page dropdown menu.

Enter Check flight status in the Page name box.

Click Save.

Close the routes configuration pane as needed to view the flow pane again.

Notice the main.flight_status intent shows up in the list of routes for the Start page. In the flow pane, there's an arrow going from the Start to the Check flight status page.

Click on the Check flight status page.

Click on + to the right of Parameters.

Enter pnr_num for the Display name.

Select @PNR_Number from the dropdown for the Entity type.

Click to enable Required if not already.

Your parameter should look like the following:

The Parameter page displaying the highlighted Display name, entity type, and required checkbox

Scroll down to the Initial prompt fulfillment section.

Enter What is your flight booking reference please? under Agent says.

Click Save.

Close the Parameter configuration pane as needed to view the flow pane again.

Click + to the right of Routes in the Check flight status page.

Scroll down to the Condition section.

Enter $page.params.status for the Parameter.

Ensure = is selected for the Operand.

Enter FINAL for the Value.

Your condition should look similar to the following:

The condition page displaying the highlighted parameter, operand, and value

Note: Did you notice some quotes around the word FINAL in the screenshot? The value of FINAL is acceptable with or without quotes, i.e., FINAL or "FINAL". This is because it's a single word without spaces. When you have more than one word it's best to use quotes. For instance, "Flight Status".
The condition expression, "$page.params.status = FINAL", tells Dialogflow that, once your parameters have been successfully captured from the user, there's nothing further to do on this page.

Scroll down to the Transition section.

Select + new Page from the Page dropdown.

Enter Confirm flight status for the Page name.

Click Save.

Set up the Confirm flight status page
Click on the Confirm flight status page.

Click on Edit fulfillment.

Enter a message under Agent says such as the following:

Looking up your flight reference $session.params.pnr_num, we are pleased to confirm that your flight is on time and will depart LAX at 10am for NYC.  Please arrive at least 1.5 hours before departure.

Would you like to make any changes to your flight?
Copied!
Note: You will notice that all of the flight status queries in your testing will result in this same static message. How could you implement a dynamic lookup of flight status information? Through the webhook capability where Dialogflow CX will call an external API, provide the PNR reference, and retrieve the actual flight status.
To use a webhook, you would enable the webhook checkbox. However, it is outside the scope of this lab as we are focused on the topic of conversational design. This is mentioned for completeness and to provide a pointer for your further learning.
Click Save.

Click + to the right of Routes. Recall that you may need to close out the right-hand pane configuration window to see this.

Select confirmation.yes from the Intent dropdown.

Scroll down to the Fulfillment section.

Enter Our agents will be in touch soon for your request to amend your scheduled flight. under Agent says.

Scroll down to the Transition section.

Select +new Page from the Page dropdown.

Enter Anything else? for the Page name.

Click Save.

Add another route, this time selecting confirmation.no from the Intent dropdown.

Add a message for this route; Great, we look forward to having you fly with us soon..

Add a transition to the same Anything else? page that you created.

Click Save.

Set up the Anything else? page
Click on the Anything else? page.

Click to open the Entry fulfillment.

Enter Have you any further queries for me? for the Agent says.

Click Save.

Click + to add a route from the Anything else? page.

Select confirmation.yes from the Intent dropdown.

Enter Please let me know how I can be of service. for the Agent says under Fulfillment.

Select Start for the Page under the Transition section.

Click Save.

Add another route, this time specifying the confirmation.no intent.

Enter No worries, I'm glad to be of assistance today. Goodbye. for the Agent says under Fulfillment.

Choose End Session page as the transition.

Click Save.

Task 4. Testing your virtual agent
Part 1 (positive tests)
Click on Test Agent to open the test simulator.

Ideally you have defined some test data with expected results. One way you can organize your test data is in the order of progression of the conversation, much like you see in a chat bot conversation.

Proceed to performing a round of testing on your agent similar to the test data below.

Who	Message
User	Check flight status
Agent	What is your flight booking reference please?
User	1234
Agent	What is your flight booking reference please?
User	abcdefghijk
Agent	What is your flight booking reference please?
User	P34K09
Agent	Looking up your flight reference P34K09, ...
User	no
Agent	Great, we look forward to having you fly with us soon. Have you any further...
User	no
Agent	No worries, I'm glad to be of assistance today. Goodbye.
Notice that the test data deliberately uses 1234 (too short) and abcdefghijk (too long) before using a valid PNR number, P34K09. Because of the regex expression you defined, the first two values entered by the user are considered invalid PNR numbers. Therefore, the agent keeps asking until it can fulfill the parameter status condition (FINAL) and transition to the next page.

Note: If you make any mistakes in your testing, you can always reset the test using the trash icon in the upper right of the Test Agent.
Notice in the simulator pane that there is some data about the conversation. For instance, the flow of pages, intents matched, parameters and their filled values.

Click on the reset icon to clear the current test data. It's a good habit to do this after every complete test round unless you want to repeat the same test again (using the recycle icon).

Part 2 (negative tests)
Now, instead of responding No when the flow gets to the Anything else? page, you'll respond Yes.

Start another round of testing on your agent, this time a negative test. For instance:

Who	Message
User	Check flight status
Agent	What is your flight booking reference please?
User	PN34K5
Agent	Looking up your flight reference PN34K5, ...
User	no
Agent	Great, we look forward to having you fly with us soon. Have you any further...
User	yes
Agent	Please let me know how I can be of service
User	check flight status
Agent	Looking up your flight reference PN34K5, ...
Note: Did you notice that when you requested to check flight status the second time, you weren't prompted for the PNR? That's not the expected response for this scenario where the user wants to look up a different record.
What happened? Well, Dialogflow remembered the number entered earlier. To make it work for this scenario, you'll need to reset, or nullify, the pnr_num parameter.
Click Check my progress to verify the objective.
Testing your virtual agent

Task 5. Resetting parameters
Click on the Anything else? page.

Click on the Entry fulfillment field to open the Fulfillment configuration pane.

Scroll down to the Parameter presets section.

Click Add a parameter.

Enter pnr_num for the Parameter name under Parameter presets.

Enter null for the Value.

Click Save.

Run through your tests again to ensure all parameters are reset to null so that the agent will reprompt for new values when the conversation starts over.

Your Anything else? page should look like the following:

The Anything else? page displaying the highlighted entry fulfillment field and parameter presets section

Connect the Anything else? page to the Confirm trip page
The Anything else? page can be reused for the flight booking conversational flow that you imported at the start of this lab. The expected outcome is our virtual agent is able to continue the conversation if required by the user at the end of their queries.

Click on the Confirm trip page.

Click on the confirmation.yes route.

Add a transition to the Anything else? page.

Click Save.

Run through the check flight status tests again to ensure the pnr_num parameter is reset to null so that the agent will reprompt for a new value when the user wants to check a different flight.

Note: Click on the reset icon in the Test Agent pane to make sure your next test scenario starts fresh.
Now run through the flight booking test scenario to completion and book the flight. Answer, Yes, when you reach the Anything else? portion of the flow and start a new flight booking.

Note: What worked and what didn't? You may have noticed that the agent remembers the flight details from your previous booking and does not bother asking you again. Is this the correct behavior?
Just like in the check flight status scenario, you will want to reset the flight booking parameters to null.

Click on the Anything else? page.

Click on Entry fulfillment.

Add parameters as follows, setting them to null like you did with the pnr_num:

Parameter	Value
departure_city	null
destination_city	null
departure_date	null
return_date	null
passenger_name	null
Click Save.

Run through the tests again to ensure all parameters are reset to null when you answer No to the question on whether the information is correct.

Knowledge check


A custom regex entity is useful for which of the following data types?

A custom size entity with fixed values (e.g., small, medium, large).

A custom alphanumeric entity that conforms to a pattern.

A system date entity.


In what scenario is it required to reset parameters captured during the conversation?

When additional values need to be captured and you no longer need the previously captured data for the conversation.

Parameters never need to be reset because the AI in Dialogflow will figure it out.

Parameters should always be reset.

Export your agent
If you want to export your virtual agent to use in your own project, use the following steps:

Select View all agents from the Agent dropdown at the top.

Click on the context menu (three vertical dots More icon) and choose Export.

Click on the Download radio button.

Click Export.

Congratulations!
You now know how to use regular expressions to do parameter validation on a PNR Number and reset parameters to null when the user starts a new flow of conversation.

Finish your quest
This self-paced lab is part of the Create Conversational AI Agents with Dialogflow CX quest. A quest is a series of related labs that form a learning path. Completing this quest earns you a badge to recognize your achievement. You can make your badge or badges public and link to them in your online resume or social media account. Enroll in this quest or any quest that contains this lab and get immediate completion credit. See the Google Cloud Skills Boost catalog to see all available quests.

Google Cloud training and certification
...helps you make the most of Google Cloud technologies. Our classes include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. Certifications help you validate and prove your skill and expertise in Google Cloud technologies.

Manual Last Updated October 26, 2023

Lab Last Tested October 27, 2023

Copyright 2024 Google LLC All rights reserve