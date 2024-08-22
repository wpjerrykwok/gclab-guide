Dialogflow CX: Bot Building Basics
experiment
Lab
schedule
1 hour 30 minutes
universal_currency_alt
No cost
show_chart
Introductory
info
This lab may incorporate AI tools to support your learning.
GSP928
Google Cloud self-paced labs logo

Overview
Dialogflow CX provides a simple, visual bot building approach to virtual agent design. Bot designers now have a much clearer picture of the overall bot building process and multiple designers are able to easily collaborate on the same agent build. Dialogflow CX supports many languages for your agent to use and understand, but this lab will be using only English.

In this lab you will build a conversational agent using Dialogflow CX.

Prerequisites
You should be generally familiar with the basic concepts of conversational AI. Read through these Sample Transcripts to get an idea of what client transcripts might look like. Often the first step in creating an agent is to read through client transcripts and/or other contextual data to understand the use case and specific business requirements.

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
Enable the Dialogflow API
In the console search field, type in "Dialogflow API".
Select the Dialogflow API tile.
Click the Enable button.
Task 1. Create your agent
Visit the Dialogflow CX console, then select your Cloud Project name. Your Cloud Project name should match your Project ID for your lab like: qwiklabs-gcp-xx-xxxxxxxxxxx
Dialogflow CX console with project name highlighted
Click Check my progress to verify the objective.
Assessment Completed!
Enable API
Assessment Completed!

Click Create agent > Build your own If you do not see this page, refresh your browser.
Agents page with Create agents button highlighted
Name your agent Flight booker.

Pick global from the Location drop-down.

Click Create.

Create agent window, with display name and location fields completed
After creating the agent, navigate to Agent Settings > General > Logging settings and check the boxes next to Enable Cloud Logging and Enable Conversation History option. It will generate logs for this agent.

Click Save.

Agent Settings General tabbed page with Enable stackdriver logging option selected
Click Check my progress to verify the objective.

Assessment Completed!
Create an Agent
Assessment Completed!
Knowledge check


You must enable the Dialogflow API for your project to be able to access the Dialogflow CX console.
check
True

False

Task 2. Intents
Intents are the reasons an end-user has for interacting with the agent, for example, ordering something. You can create an intent for every topic they may want to navigate.

Intents can be reused across Pages and Flows. Each intent is defined by training phrases end-users typically ask. These can be annotated or "labeled" to collect specific parameters, such as arrival city or departure date.

Dialogflow CX will suggest annotations as you include training phrases for the intent; they can also be manually annotated to collect the parameter values you want to extract from the end-user's interaction with your agent.

Recommended: in order to reuse intents as well as make maintenance easier, name your intents with clear and explicit names.

Format of intent: category.some_description

Example of formatting:

Core Intents: main.book_a_flight
Common intent but not core: supplemental.flight_emissions
Reusable intents: confirmation.yes, confirmation_no, redirect.live_agent.
Create your first intent
Click Manage > Intents > + Create :
Intents tab with Create button highlighted
Display Name: main.book_a_flight
Under the Training Phrases header, add each of the following phrases into Dialogflow, click Enter after each phrase:
Book a flight
Can you book my flight to San Francisco next month
I want to use my reward points to book a flight from Milan in October
My family is visiting next week and we need to book 6 round trip tickets
Four business class tickets from Taiwan to Dubai for June 2nd to 30th
I need a flight Saturday from LAX to San Jose
Book SFO to MIA on August 10th one way
Help me book a ticket from 4/10 to 4/15 from Mexico City to Medellin Colombia please
I am booking a surprise trip for my mom, can you help arrange that for May 10th to May 25th to Costa Rica
Do you have any cheap flights to NYC for this weekend
I want to fly in my cousin from Montreal on August 8th
I want to find two seats to Panama City on July 4th
For my wedding anniversary we want to go to Seattle for Christmas
Note: For higher model accuracy, using 20-50 training phrases with short and long response options is recommended.
Click Save.
Intents tab with Save button highlighted
Some words are highlighted because Dialogflow has automatically labeled the entities, such as a date, place, or number.
Intents page displaying highlighted dates, places, and numbers
Note: You can also add training phrases in bulk by creating a training phrase CSV file and uploading it to Dialogflow.
Task 3. Flows and pages
Flows are used to define topics and the associated conversational paths. Every agent has one flow called the Default Start Flow. This single flow may be all you need for a simple agent.

More complicated agents may require additional flows, and different development team members can be responsible for building and maintaining these flows.

Default Start Flow diagram
Every flow starts with a Page, and is made of one or multiple different pages thereafter to handle the conversation within a particular flow. The current page an end-user is on is considered the "active page". Each page can be configured to collect any required information from the end-user.

Build from your Default Start Flow
The page your agent starts from is called the Default Start Flow. Pages store routing logic, responses (known as Fulfillment), specific actions to take if an intent cannot be matched (known as no-match) or receives no-input (which is when the agent does not receive a response in time).

Click Build.
Click Start to open the page.
Default Start Flow pagewith Build and Start button highlighted
From the expanded options on the Start page, select the + icon next to Routes.
Routes and plus icon highlighted
Select the intent main.book_a_flight from the drop-down, then click Save.
Route window with main.book_a_flight intent selected and Save button highlighted
Next, in the Routes section, click the main.book_a_flight route. Routes section with main.book_a_flight route highlighted

Scroll down to Transition and choose + new Page from the drop-down.

Name the page Ticket information and click Save.

Highlighted Page name field populated with Ticket information
Exit out of the windows to return to the main display of flows to see your new Ticket information page connected to the Start page.
Main display with flow diagram
The beginning of the flow now includes a greeting, and will then proceed to the Ticket information page when the main.book_a_flight intent is matched. On the Ticket Information page you will collect parameters from the end-user so they can book their flight.

Task 4. Entities and parameters
Entities define the type of information you wish to extract from an end-user, ex: city you want to fly to. Use Dialogflow's built-in " system entities'' for matching dates, times, colors, email addresses, and so on.

System entities can also be “extended” to include values that are not part of the default system values. If you need to create a fully customized entity, you can do so by creating a Custom Entity type for matching data that is custom to your business and not found as a system entity.

Parameters are information supplied by the end-user during a session, such as date, time, and destination city. Each parameter has a name and an entity type. They are written in snake_case (lowercase with underscores between words)

Create your first set of parameters
Next you will use an entity to extract a required parameter from the end-user.

Click on the page Ticket Information, then the + by Parameters to collect flight data.
Enter departure_city in the Display name field.
Choose @sys.geo-city from the Entity type drop-down.
Scroll down to Initial prompt fulfillment > Agent responses > Agent Says and add What city would you like the flight to depart from?
Click Save.
Initial prompt fulfillment section with Agent says field populated
Exit out of this window to make another parameter.
Click the + by Parameters again to create 4 additional parameters one by one with the following name, entity type, and how the agent will prompt the end-user.
Display name	Entity type	Agent says
departure_date	@sys.date	What is the month and day of the departure?
destination_city	@sys.geo-city	What is your destination city?
return_date	@sys.date	What is the month and day for the returning flight?
passenger_name	@sys.any	What is the passenger's name?
When finished they are listed like this:

Parameters page displaying the list of added parameters

Note: The order in which the parameters are listed affects the order in which the flight booking agent will ask for each. You can easily change the order by dragging parameters up or down.
Knowledge check


The primary reason that an end-user is interacting with your agent is captured by which resource type?

Pages

Entities
check
Intents
close
Parameters

Flows

Task 5. Conditions
Once the agent has collected the necessary 5 flight booking parameters, you want to route the end user to another page using a routing condition, which you will create next.

Exit out of the parameter window to return to the Ticket information page again.
Scroll down to locate Routes and click the + sign next to it.
Scroll down to Condition > Condition rules > select "Match AT LEAST ONE rule (OR)"
In the Parameter field enter $page.params.status.
Choose the = sign in the Operand drop-down.
In the Value field enter: "FINAL" (ensure you include the double quotes).
Click Save.
Condition section with Parameter field highlighted
Knowledge check


What’s the correct way to check if all parameters on a Page are filled?

$session.params.status = FINAL

$session.params.status = “FINAL”

#context.params.status = FINAL
check
$page.params.status = “FINAL”

$page.params.status = FINAL

#context.params.status = “FINAL”

Task 6. Fulfillment
Now add a response to say to the end-user when all 5 of their booking parameters are collected. These responses are called Fulfillment.

From the condition you just made, scroll down a bit and locate the section called Fulfillment.
Under Agent responses type the following for Agent says: Thank you for that information. Let me check on the availability of your ticket.
Click Save.
(Now stay on this page while you read on to the next step of confirming information.)

Fulfillment section with highlighted populated Agent says field
Task 7. Confirming information
After offering a response (or fulfillment), you need to create a transition to a new page that will repeat back to the end-user if the travel information collected (parameters) are correct.

Continue to scroll down (past the fulfillment you just created) until you reach Transition.
On the Page field, select the drop down to choose + new Page.
Type Confirm trip in the field called Page name.
Transition section with populated Page name field highlighted
Click Save.
Exit out of the window.
Take a look at the flow of your 3 pages.
Main display with flow diagram. Flow points include Start, Ticket information, and Confirm Trip
Repeating back the parameters collected from end-users
Session Parameters store information previously collected from the end-user and are active throughout the session. They also help you repeat information back to the end-user.

For example, we can have the agent repeat back a passenger's name: "Thanks for providing that information, $session.params.passenger_name." This displays to the end-user as "Thanks for providing information, John Day."

They are formatted as follows:

Prefix: $session.params.
Entity Name: passenger_name
So referencing the departure city would look like: $session.params.departure_city

Starting from the Build view, click on the Confirm Trip page > Entry fulfillment > Edit fulfillment field.
Confirm trip window with Edit fulfillment field highlighted
Since you used 5 parameters, you can repeat them back to the user via the following session parameters. Paste the following text within the Agent says section:
This is to confirm that $session.params.passenger_name will fly
From: $session.params.departure_city
To: $session.params.destination_city
Leaving on: $session.params.departure_date
Returning on: $session.params.return_date

Is this correct?
Copied!
Fullfillment window with Agent says field populated
Then click Save.
This is what it will look like to the end-user when the virtual agent repeats back the collected session parameters:
Virtual agent displaying the collected session parameters
Positive confirmation route
Exit out of the window to return to your Confirm Trip page. Click + next to Routes.
Confirm trip page with plus button highlighted
Click the Intents drop-down , then click + new Intent.
Intent drop-down menu with + new intent option highlighted
In Display name type confirmation.yes.
In Training phrases enter yes then Enter (you can add more phrases like "correct", "yup", etc., to improve the NLU matching for this intent).
Training phrases section with yes and correct populated in the text field
Click Save.
After saving, scroll down to the Fulfillment section and under Agent responses enter Great, your flight is booked! for Agent says.
Highlighted Agent says field displaying the message: Great, your flight is booked!
Then click Save.

Click the back arrow, next to Route.

Back arrow highlighted
Negative confirmation route
Now add logic to route an end-user to recollect their flight parameters if they say the information is incorrect.

Still on the Routes section select Add route.
Add route button highlighted
From the Intents drop-down choose + new Intent.
Name the intent confirmation.no in the Display name field.
Highlighted Display name field with confirmation.no entered
Scroll down to the Training phrases section type no then click Enter.
Training phrases section with no highlighted
Click Save.
Next, scroll down to the section called Transition > Page, then choose Ticket information from the drop-down.
Transition section with Ticket information selected i the Page dropdown menu
Note: This is to prompt the user again for their flight information.
Scroll up to Parameter presets and click Add parameter . Enter the following 5 values and assign their value to null without the quotation marks.
Note: You will need to delete the quotation marks in the value column and type null. This is to delete the parameters collected from the end-user.
Parameter	Value
departure_city	null
destination_city	null
departure_date	null
return_date	null
passenger_name	null
Parameter presets section with null value for each of the five parameters
The purpose of this is to remove the value that was previously collected from the end user to allow them to submit a new value. If this step is missed, it might result in an infinite loop scenario in your bot!

Click Save.
Exit out of the window to return to the Build view, you will now see how all 3 pages flow. Note that the last page has two arrows between the Confirm trip and Ticket information page because the confirmation.no intent is linked back.
Build view displaying the flow diagram
Knowledge check


Select the agent response that’s best for casually confirming that tickets are booked.
close
You’re all set! Look for the tickets in your email later today! Now, are there any other events you’re interested in?
check
The transaction you requested has been completed. You will receive an electronic copy of your tickets within the next 6 hours. Now, would you like to continue browsing upcoming events?

Task 8. Testing
To test that your agent works as intended, click on Test Agent in the upper right corner of the screen.

Interact with the agent as if you were the end-user. As you move through the main flow, notice the pages, intents, and transitions you created.

Depending on how you arranged your parameter collection, you can try typing in the following sample dialogue:

I'd like to book a flight
Austin
Tomorrow
Boston
Next Friday
Mickey Mouse
Yes
This should result in a successful transaction through your agent, commonly known as the “happy path”.

Here is an example of the above agent testing in the Test Agent console:

Test Agent console with simulator flow example

Click Check my progress to verify the objective.
Assessment Completed!
Test the agent
Assessment Completed!

Task 9. Exporting your agent
When you build an agent for one project, you can export it to use in a different project. You can export your agent and save it to use in future labs or to continue building in your own personal project!

In the Agent drop down at the top of the Dialogflow CX console, click View all agents.
Expanded Agent dropdown with View all agents button highlighted
On the Agent list screen, click the context menu next to your agent and then click Export.
Expanded context menu with Export button highlighted
On the Export Agent screen, choose Download to local file, then click Export.
Export Agent page with Download option selected and highlighted
Congratulations!
You have built a Dialogflow CX Agent and become familiar with the foundational concepts.

Finish your quest
This self-paced lab is part of the Create Conversational AI Agents with Dialogflow CX quest. A quest is a series of related labs that form a learning path. Completing this quest earns you a badge to recognize your achievement. You can make your badge or badges public and link to them in your online resume or social media account. Enroll in this quest to get immediate credit. See the Google Cloud Skills Boost catalog to see all available quests.

Next steps / learn more