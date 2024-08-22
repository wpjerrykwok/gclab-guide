Monitoring in Google Cloud: Challenge Lab
experiment
Lab
schedule
45 minutes
universal_currency_alt
1 Credit
show_chart
Introductory
info
This lab may incorporate AI tools to support your learning.
Lab instructions and tasks
ARC115
Overview
Setup and requirements
Challenge scenario
Task 1. Install the Cloud Logging and Monitoring agents
Task 2. Add an uptime check for Apache Web Server on the VM
Task 3. Add an alert policy for Apache Web Server
Task 4. Create a dashboard and charts for Apache Web Server on the VM
Task 5. Create a log-based metric
Congratulations!
ARC115
Google Cloud self-paced labs logo

Overview
In a challenge lab you’re given a scenario and a set of tasks. Instead of following step-by-step instructions, you will use the skills learned from the labs in the course to figure out how to complete the tasks on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

When you take a challenge lab, you will not be taught new Google Cloud concepts. You are expected to extend your learned skills, like changing default values and reading and researching error messages to fix your own mistakes.

To score 100% you must successfully complete all tasks within the time period!

Setup and requirements
Before you click the Start Lab button
Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources will be made available to you.

This hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended).
Note: Use an Incognito or private browser window to run this lab. This prevents any conflicts between your personal account and the Student account, which may cause extra charges incurred to your personal account.
Time to complete the lab---remember, once you start, you cannot pause a lab.
Note: If you already have your own personal Google Cloud account or project, do not use it for this lab to avoid extra charges to your account.
Challenge scenario
As a junior cloud engineer, you are part of a team of people assigned to manage Google Cloud resources in your organization. One of your job requirements in this role is to use the Cloud Logging and Cloud Monitoring services to monitor Apache Web Server activities installed on a virtual machine (VM) instance.

You are expected to have the skills and knowledge for the tasks that follow.

Your challenge
For this challenge, you have been tasked with installing the Cloud Monitoring and Cloud Logging agents on the VM via SSH, create an alert policy for Apache Web Server, set uptime checks for the VM, configure a dashboard, and add a log-based metric for the server that's installed on the VM.

You need to:

Install the Cloud Monitoring and Cloud Logging agents.
Add an uptime check and alert policy for Apache Web Server.
Create a dashboard and chart for the VM and Apache Web Server each.
Create a log-based metric.
For this challenge lab, a virtual machine (VM) instance named apache-vm has been configured that has Apache Web Server installed for you to complete the tasks.

You can verify the server is running by clicking on the External IP of the VM.

Each task is described in detail below, good luck!

Task 1. Install the Cloud Logging and Monitoring agents
Note: With the Cloud Logging agent not yet installed, if you check for Apache Web Server logs in Cloud Logging, you will only see audit logs; no Apache Web Server logs (although Apache Web Server is configured on the VM instance). Similarly, if you try to check metrics for Apache Web Server in the Metric Explorer it will not show any data.
So for this task, you need to install the Cloud Logging and Cloud Monitoring agents.

Connect to the VM instance apache-vm provisioned for you via SSH and install the Cloud Logging and Cloud Monitoring agents.

Enable the Apache Web Server monitoring plugin using the following commands:

(cd /etc/stackdriver/collectd.d/ && sudo curl -O https://raw.githubusercontent.com/Stackdriver/stackdriver-agent-service-configs/master/etc/collectd.d/apache.conf)
Copied!
sudo service stackdriver-agent restart
Copied!
Click Check my progress to verify the objective.
Install the Cloud Logging and Cloud Monitoring agents

Task 2. Add an uptime check for Apache Web Server on the VM
For this task, you need to verify that your VM is up and running. To do this, create an uptime check with the resource type set to instance.
Note: The uptime check that you configure may take a while to become active. Continue with the lab, you can check the uptime results later.
Click Check my progress to verify the objective.
Add an uptime check for Apache Web Server on the VM

Task 3. Add an alert policy for Apache Web Server
Create an alert policy for Apache Web Server traffic that notifies you on your personal email account when the traffic rate exceeds 3 KiB/s.

Connect to the instance via SSH and run the following command to generate the traffic:

timeout 120 bash -c -- 'while true; do curl localhost | grep -oP ".*"; sleep .1s;done '
Copied!
Monitor the alert policy that you just created once the traffic rate exceeds 3 KiB/s you should receive an alert on your email id.
Click Check my progress to verify the objective.
Add an alert policy for Apache Web Server

Note: If you do not receive an email immediately, try to increase the load on the VM by executing the command in step 2 again.
Task 4. Create a dashboard and charts for Apache Web Server on the VM
For this task, you need to create a dashboard that's configured with charts.

Add the first line chart that has a Resource metric filter, CPU load (1m), for the VM.

Add a second line chart that has a Resource metric filter, Requests, for Apache Web Server.

Click Check my progress to verify the objective.
Create a dashboard and charts for Apache Web Server on the VM

Task 5. Create a log-based metric
Next, create a log based metric that filters for the following values:

Filter	Values
Resource type	VM
Logname	apache-access
Text Payload	textPayload:"200"
Explore the log-based metrics by selecting the metric VM Instance > Apache > Request.

Click Check my progress to verify the objective.
Create a log-based metric

Congratulations!
Monitoring in Google Cloud skill badge

You have successfully installed The Cloud Logging and Cloud Monitoring agents on a VM, created an alert policy, added a dashboard and set a log-based metric with Apache Web server for your project.

Google Cloud training and certification