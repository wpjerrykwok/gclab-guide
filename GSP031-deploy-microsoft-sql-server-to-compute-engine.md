# Deploy Microsoft SQL Server to Compute Engine

## GSP031

## Overview

Compute Engine lets you create and run virtual machines on Google infrastructure. 

There is a catalog of public application images available, including a variety of Microsoft SQL Server and Windows versions.

In this lab, you will learn how to create a virtual machine with Microsoft SQL Server installed. 

You'll create a Windows user and password, and learn how to connect to the Windows Server via remote desktop.

### What you'll learn

- How to stand up a virtual machine running Microsoft SQL Server on Compute Engine.
- How to create a Windows user and password.
- How to remote desktop into a Windows Server virtual machine.

### Prerequisites

- Basic familiarity with Microsoft SQL Server and RDP Connections
- Either a Windows machine or a non-Windows machine with Chrome. Other RDP Clients should suffice but instructions for their use are not provided in this lab.

## Task 1. Deploy Microsoft SQL Server

Create a new Compute Engine virtual machine instance running Microsoft SQL Server from the Cloud Console.

In Cloud Console, click the Menu icon for **Navigation menu** in the top left of the screen:

Then click **Compute Engine**:

Then, click **CREATE INSTANCE**.

Next, name the new instance `sqlserver-lab`.

Choose a zone as `us-east4-b`.

Click **Change** to change the boot disk.

Remain in the **Public images** tab:

For the operating system, select **SQL Server on Windows Server**. For the version, select **SQL Server 2016 Web on Windows Server 2016 Datacenter**.

At the bottom of the window, click **Select**.

Click **Create**.

> Note: You will see a warning about being billed for this instance. You will not be billed for these resources because you are using credentials provided to run this lab. It's good to keep in mind that any instances you create in your personal or work accounts will be billed to you.

This will take you to a new page where you can see the new instance being created. A checked green circle appears when the instance is ready.

You will be able to remote desktop (RDP) into the machine from this page after you create a Windows user and password.

```bash
gcloud compute instances create sqlserver-lab / 
    --project=qwiklabs-gcp-02-b5bc803984f0 / 
    --zone=us-east4-b / 
    --machine-type=e2-medium / 
    --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default / 
    --metadata=enable-oslogin=true / 
    --maintenance-policy=MIGRATE / 
    --provisioning-model=STANDARD / 
    --service-account=537301723342-compute@developer.gserviceaccount.com / 
    --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/trace.append / 
    --create-disk=auto-delete=yes,boot=yes,device-name=sqlserver-lab,image=projects/windows-sql-cloud/global/images/sql-2016-web-windows-2016-dc-v20240711,mode=rw,size=50,type=pd-balanced / --no-shielded-secure-boot / 
    --shielded-vtpm / 
    --shielded-integrity-monitoring / 
    --labels=goog-ec-src=vm_add-gcloud / 
    --reservation-affinity=any
```

## Task 2. Create a Windows user and password

To RDP into the Windows instance, you must create a Windows user and password.

To create them, click the name of your instance, `sqlserver-lab`, to see the instance details.

Then, select **Set Windows password**.

This will open a new window where you create a user.

Copy and save the default username for later use and select **Set**.

After a few seconds, you should have a **New Windows password** dialog with the newly created password.

Click the rectangles alongside the password to copy the password.

## Task 3. Remote desktop (RDP) into the Windows Server

It's time to RDP into the Windows Server. 

There are two different ways to do this, depending on whether or not you are on Windows. 

Let's go through them both.

### Not running Windows

If you are not on Windows but are using Chrome, open Spark View extension page in normal window.

Click on Add to Chrome. Then, click Launch app button.

It opens up a login page, add your VM instance's External IP in Computer field.

Then enter your Windows user name and password and click Connect.

### Running Windows

If you are on a Windows machine, download the RDP file by selecting it from the RDP dropdown menu.

Double click the RDP file and log in using the Windows user and password.

## Task 4. Run Microsoft SQL Server Management Studio

Inside your remote desktop window, click the Start menu, and type **Microsoft SQL**.

Then **right** click **Microsoft SQL Server Management Studio** and **Run as administrator**.

> Note: If you are on a Mac trackpad, try placing two fingers on the trackpad and clicking with your thumb in order to emulate a right-click.

> Note: If you do not run SQL Server Management Studio as administrator the connection will not work.
Microsoft SQL Server Management Studio launches, showing you a **Connect to Server** window.

Click **Connect**.

Then, use the **Object Explorer** window to examine your new database.

## Congratulations

Compute Engine is the foundation to Google Cloud's Infrastructure-as-a-Service. 

As you've seen, application images make it easy to run Microsoft SQL Server.
