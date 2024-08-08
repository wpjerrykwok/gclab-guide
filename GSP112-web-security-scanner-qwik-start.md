---
title: "Walkthrough... Monitoring Multiple Projects with Cloud Monitoring (GSP090)"
tags: [Google Cloud, how-to]
style: border
color: secondary
description: Leave notes and improve lab steps if possible
---

# Monitoring Multiple Projects with Cloud Monitoring

## GSP090

### Overview

Cloud Monitoring provides dashboards and alerts so you can review performance metrics for cloud services, virtual machines, and common open source servers such as MongoDB, Apache, Nginx, Elasticsearch, and more. 

You configure Cloud Monitoring in the Console.

In this hands-on lab you will have 2 projects to monitor in Cloud Monitoring. 

You'll add them both to a Cloud Monitoring account and monitor the metrics the virtual machines in the projects provide.

#### Objectives

In this lab, you will learn how to:

- Create a Cloud Monitoring account that has two Google Cloud projects.

- Monitor across both projects from the single Cloud Monitoring account.

#### Setup for two projects

For this lab you are given two Project IDs. 

When you logged in, by default you logged in to Project 1. 

You'll need to keep track of your projects, and you can return to this page to remind yourself which is which. 

The projects will change order, so knowing the last few digits of the name will help you identify them.

Project 1 already has a virtual machine (and you can look at it by going to **Compute Engine** > **VM instances**). 

You will create a virtual machine in Project 2, and then monitor both projects in Cloud Monitoring.

### Task 1. Create Project 2's virtual machine

At the top of the screen, click on the dropdown arrow next to Project 1's name.

Make sure that you're on the **All** tab, then click on the name of **Project 2** to go into it.

Select **Navigation menu** > **Compute Engine** to open the VM instances window.

Click **+Create instance** to create a new instance.

Name this instance **instance2**.

Select **Region** `us-west1` and **Zone** `us-west1-b`.

Leave all of the options at the default settings.

Click **Create**.

Now you have resources to monitor in both of your projects.

> Note: Make sure that you are in Project 2 to proceed further in the lab.

#### Create a Monitoring Metrics Scope

Set up a Monitoring Metrics Scope that's tied to your Google Cloud Project. The following steps create a new account that has a free trial of Monitoring.

In the Cloud Console, click **Navigation menu** > View All Products > **Monitoring**.

When the Monitoring **Overview** page opens, your metrics scope project is ready.

Now add both projects to Monitoring.

In the left panel, click **Monitoring Settings** and then in the **Settings** window, click **+Add GCP PROJECTS** in the GCP Projects section.

Click **Select Projects**.

Check Project ID 1 and click **Select**.

Click **Add projects**.

### Task 2. Monitoring Overview

Click on **Overview** in the left menu. 

You'll be adding a lot of good information here as the lab goes along. 

First, you'll create a Cloud Monitoring Group for visibility across both projects.

#### About Cloud Monitoring groups

Cloud Monitoring lets you define and monitor groups of resources, such as VM instances, databases, and load balancers. 

Groups can be based on names, tags, regions, applications, and other criteria. 

You can also create subgroups, up to six levels deep, within groups.

#### Create a Cloud Monitoring group

In the left menu, click **Groups**, then click **+Create group**.

Name your group `DemoGroup`.

The **Criteria** is a set of rules that will dynamically evaluate which resources should be part of this group.

Cloud Monitoring dynamically determines which resources belong to your group based on the filter criteria that you set up.

- In the first dropdown field (**Type**), **Name** is selected by default.

- In the second dropdown (**Operator**), **Contains** is selected by default.

- In the third field (**Value**), type in `instance` since both of the instance names in both of your projects start with the word `instance`.

Click **Done**, then click **Create**.

### Task 3. Uptime check for your group

Uptime checks let you quickly verify the health of any web page, instance, or group of resources. 

Each configured check is regularly contacted from a variety of locations around the world. 

Uptime checks can be used as conditions in alerting policy definitions.

In the left menu, click **Uptime checks**, then click **+Create uptime check**.

Create your uptime check with the following information:

- **Protocol**: `TCP`

- **Resource Type**: `Instance`

- **Applies To**: `Group`, and then select `DemoGroup`.

- **Port**: `22`

- **Check frequency**: `1 minute`, then click **Continue**.

Click **Continue** again.

Leave the slider **ON** state for **Create an alert** option in **Alert & notification** section, then click **Continue**.

For **Title**: enter `DemoGroup uptime check`.

Click **TEST** to verify that your uptime check can connect to the resource.

When you see a green check mark everything can connect, click **Create**.

### Task 4. Alerting policy for the group

Use Cloud Monitoring to create one or more alerting policies.

In the left menu, click **Uptime checks**.

Click the three dots at the far right of your Display Name and click **Add alert policy**.

Click **+Add alert condition**.

Select the previously created **Uptime health check on DemoGroup** condition from the left section and click **Delete alert condition**.

In your **New condition**, click **Select a metric**.

Uncheck the **Active**.

In the **Select a metric** field, search `check_passed` and click **VM Instance** > **Uptime_check** > **Check passed**. Click **Apply**.

Click **Add a filter**, set the **Filter** to `check_id` and select `demogroup-uptime-check-id` as the **Value**. Click **Done**.

> Note: If `demogroup-uptime-check-id` check_id is unavailable, please wait for a few seconds and try.

In left panel, click on the arrow button next to **VM Instance-Check passed**, then click on **Configure trigger**.

Select **Metric absence** as Condition type and click **Next**.

Turn off **Configure notifications**.

In the **Alert policy name** field, enter the **Name** as `Uptime Check Policy`. Click **Next**.

Click **Create policy**.

### Task 5. Custom dashboard for your group

Create a custom dashboard so you can monitor your group easily.

In the left menu, click **Dashboards**, then click **+Create dashboard**.

Name your dashboard.

Click **+Add Widget** and select **Line** option in **Visualization**.

In the **Metric** field, Uncheck the **Active**.

Search **uptime** (compute.googleapis.com/instance/uptime) and click **VM Instance** > **Instance** > **Uptime**. Click **Apply**.

Again click on **Apply**.

### Task 6. Remove one instance to cause a problem

In the console, select **Navigation menu** > **Compute Engine**.

Check the box next to **instance2**, then click on the 3 vertical dots at the top of the page and click **Stop**. Click **Stop** again to turn off the machine.

Wait a minute or 2 for the instance to stop and violate the uptime check you just set up. After a couple of minutes, turn your machine back on by clicking **Start/Resume**, then **Start**.

Click **Navigation menu** > **Monitoring** > **Alerting** and refresh your browser. It may take a few more minutes to show that you have issues in the Summary section. Refresh until you see an Incident.

> Optional: Using the left menu, look at **Dashboards** to view your custom dashboard. This provides details on both VMs. If you mouse over your chart, you can see which of your instances was stopped and restarted.

#### Incidents

When the alerting policy conditions are violated, an "incident" is created and displayed in the Incident section.

Responders can acknowledge receipt of the notification and can close the incident when it has been taken care of.

In the **Incidents** section, click on the name of the alerting policy that was violated to go into it.

You've already **fixed** your problem by turning the VM back on, so the incident was cleared and you no longer see an incident in the Incidents section.

To see the cleared incident, scroll down and click on the **Show closed incidents** link.

Your incident should have a **Closed** status. You can read through the incident details.

You can also click on the **Uptime Check Policy** link to explore the metrics it gives you.

In several more minutes the Monitoring Overview page will all go back to green when the instance in Project 2 passes the Uptime Check.

### (Optional) Remove your alerting policy

If you set up an email alert as part of your alerting policy, there is a chance that you will receive a few emails about your resources even after the lab is completed.

To avoid this, remove the alerting policy before you complete your lab.

### Congratulations!

Congratulations! 

In this lab, you have monitored two Google Cloud projects in Cloud Monitoring, and responded to an incident with one of the instances in the Group. 

You also created a custom dashboard to monitor your group easily.
