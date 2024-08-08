App Dev: Setting up a Development Environment - Python experiment Lab schedule 50 minutes universal_currency_alt No cost show_chart Introductory info This lab may incorporate AI tools to support your learning. GSP183 Google Cloud self-paced labs logo

Overview Compute Engine is just one resource provided on Google Cloud.

Objectives In this lab, you set up a Python development environment on Google Cloud, using Compute Engine to create a virtual machine (VM) and installing software libraries for software development.

You perform the following tasks:

Provision a Compute Engine instance. Connect to the instance using SSH. Install a Python library on the instance. Verify the software installation. Google Cloud Google Cloud consists of a set of physical assets, such as computers and hard disk drives, and virtual resources, such as virtual machines (VMs), that are contained in Google's data centers around the globe. Each data center location is in a global region. Regions include Central US, Western Europe, and East Asia. Each region is a collection of zones, which are isolated from each other within the region. Each zone is identified by a name that combines a letter identifier with the name of the region. For example, zone a in the East Asia region is named asia-east1-a.

This distribution of resources provides several benefits, including redundancy in case of failure and reduced latency by locating resources closer to clients. This distribution also introduces some rules about how resources can be used together.

Projects Any Google Cloud resources that you allocate and use must belong to a project. You can think of a project as the organizing entity for what you're building.

A project is made up of the settings, permissions, and other metadata that describe your applications.

Resources within a single project can work together easily, for example by communicating through an internal network, subject to the regions-and-zones rules. The resources that each project contains remain separate across project boundaries; you can only interconnect them through an external network connection.

Each Google Cloud project has a:

Project name, which you provide. Project ID, which you can provide or Google Cloud can provide for you. Project number, which Google Cloud provides. As you work with Google Cloud, you'll use these identifiers in certain command lines and API calls. The following screenshot shows a project name, its ID, and number:

The Cloud Console displays project ID and name

In this example:

Example Project is the project name. example-id is the project ID. 123456789012 is the project number. Each project ID is unique across Google Cloud. Once you have created a project, you can delete the project but its ID can never be used again.

When billing is enabled, each project is associated with one billing account. Multiple projects can have their resource usage billed to the same account.

A project serves as a namespace. This means every resource within each project must have a unique name, but you can usually reuse resource names if they are in separate projects. Some resource names must be globally unique. Refer to the documentation for the resource for details.

In this lab, you provision a Compute Engine virtual machine (VM) and install software libraries for Python software development on Google Cloud.

Ways to interact with the services Google Cloud gives you three basic ways to interact with the services and resources.

Cloud Console: a web-based, graphical user interface that you can use to manage your Google Cloud projects and resources.

Command-line interface:

Cloud SDK: provides the gcloud command-line tool, which gives you access to the commands you need. Cloud Shell: a browser-based, interactive shell environment for Google Cloud. You can access Cloud Shell from the Google Cloud console. If you prefer to work in a terminal window, the Cloud SDK provides the gcloud command-line tool, which gives you access to the commands you need. The gcloud tool can be used to manage both your development workflow and your Google Cloud resources. See the gcloud reference for the complete list of available commands. Client libraries: The Cloud SDK includes client libraries that enable you to easily create and manage resources. Google Cloud client libraries expose APIs to provide access to services and resource management functions. You also can use the Google API client libraries to access APIs for products such as Google Maps, Google Drive, and YouTube.

Setup Before you click the Start Lab button Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources will be made available to you.

This hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended). Note: Use an Incognito or private browser window to run this lab. This prevents any conflicts between your personal account and the Student account, which may cause extra charges incurred to your personal account. Time to complete the lab---remember, once you start, you cannot pause a lab. Note: If you already have your own personal Google Cloud account or project, do not use it for this lab to avoid extra charges to your account. How to start your lab and sign in to the Google Cloud console Click the Start Lab button. If you need to pay for the lab, a pop-up opens for you to select your payment method. On the left is the Lab Details panel with the following:

The Open Google Cloud console button Time remaining The temporary credentials that you must use for this lab Other information, if needed, to step through this lab Click Open Google Cloud console (or right-click and select Open Link in Incognito Window if you are running the Chrome browser).

The lab spins up resources, and then opens another tab that shows the Sign in page.

Tip: Arrange the tabs in separate windows, side-by-side.

Note: If you see the Choose an account dialog, click Use Another Account. If necessary, copy the Username below and paste it into the Sign in dialog.

student-01-b59b3f768cf9@qwiklabs.net Copied! You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

gJ49xiHpb7Dx Copied! You can also find the Password in the Lab Details panel.

Click Next.

Important: You must use the credentials the lab provides you. Do not use your Google Cloud account credentials. Note: Using your own Google Cloud account for this lab may incur extra charges. Click through the subsequent pages:

Accept the terms and conditions. Do not add recovery options or two-factor authentication (because this is a temporary account). Do not sign up for free trials. After a few moments, the Google Cloud console opens in this tab.

Note: To view a menu with a list of Google Cloud products and services, click the Navigation menu at the top-left. Navigation menu icon Task 1. Create a Compute Engine Virtual Machine instance

gcloud compute instances create dev-instance --project=qwiklabs-gcp-04-d61bb835bc43 --zone=us-east4-a --machine-type=e2-medium --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default --metadata=enable-oslogin=true --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=168373340959-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/cloud-platform --tags=http-server --create-disk=auto-delete=yes,boot=yes,device-name=dev-instance,image=projects/debian-cloud/global/images/debian-12-bookworm-v20240709,mode=rw,size=10,type=pd-balanced --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --labels=goog-ec-src=vm_add-gcloud --reservation-affinity=any

In this section, you use the Cloud Console to provision a new Compute Engine (VM) instance.

Create and connect to a virtual machine In the Console, click Navigation menu > Compute Engine > VM Instances.

Expanded Navigation menu highlighting the Compute Engine submenu and VM instances option

On the VM Instances page, click Create Instance.

On the Create an instance page, for Name type dev-instance, and select a Region as us-east4 and Zone as us-east4-a.

Note: Regions and zones Google Cloud offers products and services in multiple distinct geographic locations, called regions. Each region has multiple distinct zones. Each zone is isolated from other zones in terms of power and internet connectivity. In the Machine configuration section, for Series select E2. In the Identity and API access section, select Allow full access to all Cloud APIs. In the Firewall section, enable Allow HTTP traffic. Leave the remaining settings as their defaults, and click Create. Note: It takes about 20 seconds for the VM to be provisioned and started. Test completed task Click Check my progress to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Create a Compute Engine Virtual Machine Instance On the VM instances page, in the dev-instance row, click SSH. This launches a browser-hosted SSH session. If you have a popup blocker, you may need to click twice.

There's no need to configure or manage SSH keys.

Install software on the VM instance In the SSH session, to update the Debian package list, execute the following command:

sudo apt-get update Copied! To install Git, execute the following command:

sudo apt-get install git Copied! When prompted, enter Y to continue, accepting the use of additional disk space.

To install Python, execute the following command:

sudo apt-get install python3-setuptools python3-dev build-essential Copied! Again, when prompted, enter Y to continue, accepting the use of additional disk space.

To install pip, execute the following command:

curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py Copied! sudo python3 get-pip.py --break-system-packages Copied! Test completed task Click Check my progress to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Install software and configure the VM instance Task 2. Configure the VM to run application software In this section, you verify the software installation on your VM and run some sample code.

Verify Python installation Still in the SSH window, verify the installation by checking the Python and pip version:

python3 --version Copied! pip3 --version Copied! The output provides the version of Python and pip that you installed.

Clone the class repository:

git clone https://github.com/GoogleCloudPlatform/training-data-analyst Copied! Change the working directory:

cd ~/training-data-analyst/courses/developingapps/python/devenv/ Copied! Run a simple web server:

sudo python3 server.py Copied! Return to the Cloud Console VM instances list (Navigation menu > Compute Engine > VM Instances), and click on the External IP address for the dev-instance.

Instances tab with External IP 35.184.33.34 highlighted

A browser opens and displays a Hello GCP dev! message from Python.

Test completed task Click Check my progress to verify your performed task. If you have completed the task successfully you will granted with an assessment score.

Run application software to get a success response Return to the SSH window, and stop the application by pressing Ctrl+C.

Install the Python packages needed to enumerate Compute Engine VM instances:

sudo pip3 install -r requirements.txt --break-system-packages Copied! Now list your instance in Cloud Shell. Enter the following command to run a simple Python application that lists Compute Engine instances. Replace <PROJECT_ID> with your Project ID and <YOUR_VM_ZONE> is the region you specified when you created your VM. Find these values on the VM instances page of the console:

Cloud console displaying Project ID qwiklabs-gcp-bcdd9ef8f952, and Region us-central1-a

python3 list-gce-instances.py <PROJECT_ID> --zone=<YOUR_VM_ZONE> Copied! Your instance name should appear in the SSH terminal window.

Example output:

Instance in project qwiklabs-gcp-04-bcdd9ef8f952 and zone "us-east4-a":

dev-instance Test your understanding Below are multiple choice-questions to reinforce your understanding of this lab's concepts. Answer them to the best of your abilities.
pip is a package management system used to install and manage software packages written in Python.

True

False

Firewall rules can be shared among networks.

True

False Congratulations! This concluded the self-paced lab, App Dev: Setting Up a Development Environment - Python. You set up a Python development environment on Google Cloud!