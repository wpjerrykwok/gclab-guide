Modular Load Balancing with Terraform - Regional Load Balancer
experiment
Lab
schedule
1 hour
universal_currency_alt
No cost
show_chart
Intermediate
info
This lab may incorporate AI tools to support your learning.
GSP191
Google Cloud self-paced labs logo

Overview
Load balancing on Google Cloud is different from other cloud providers. Google Cloud uses forwarding rules instead of routing instances. These forwarding rules are combined with backend services, target pools, URL maps and target proxies to construct a functional load balancer across multiple regions and instance groups.

Terraform is an open source infrastructure management tool that can simplify the provisioning of load balancers on Google Cloud by using modules.

Objectives
In this lab, you will learn how to:

Use load balancing modules for Terraform
Create a regional TCP load balancer
Create a regional internal TCP load balancer
Create a global HTTP load balancer with Kubernetes Engine
Create a global HTTPS content-based load balancer
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

student-01-7a4aa615f087@qwiklabs.net
Copied!
You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

Lu4CL94GtJQ0
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
When you are connected, you are already authenticated, and the project is set to your Project_ID, qwiklabs-gcp-01-158ac9567dd2. The output contains a line that declares the Project_ID for this session:

Your Cloud Platform project in this session is set to qwiklabs-gcp-01-158ac9567dd2
gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion.

(Optional) You can list the active account name with this command:
gcloud auth list
Copied!
Click Authorize.
Output:

ACTIVE: *
ACCOUNT: student-01-7a4aa615f087@qwiklabs.net

To set the active account, run:
    $ gcloud config set account `ACCOUNT`
(Optional) You can list the project ID with this command:
gcloud config list project
Copied!
Output:

[core]
project = qwiklabs-gcp-01-158ac9567dd2
Note: For full documentation of gcloud, in Google Cloud, refer to the gcloud CLI overview guide.
Terraform modules overview
The repository you'll use in this lab has some load balancer modules. First you'll learn what the modules are, then you'll clone the repository and use them.

terraform-google-lb (regional forwarding rule)
This module creates a TCP Network Load Balancer for regional load balancing across a managed instance group. You provide a reference to a managed instance group and the module adds it to a target pool. A regional forwarding rule is created to forward traffic to healthy instances in the target pool.

The load balancing and backend services, with a path leading from google_compute_firewall to google_compute_instance_template. 

Example snippet:

module "gce-lb-fr" {
  source       = "github.com/GoogleCloudPlatform/terraform-google-lb"
  region       = "${var.region}"
  name         = "group1-lb"
  service_port = "${module.mig1.service_port}"
  target_tags  = ["${module.mig1.target_tags}"]
}
terraform-google-lb-internal (regional internal forwarding rule)
This module creates an internal load balancer for regional load balancing of internal resources. You provide a reference to the managed instance group and the module adds it to a regional backend service. An internal forwarding rule is created to forward traffic to healthy instances.

The load balancing and backend services, with a path leading from google_compute_region_backend_service to google_compute_instance_group_manager.

Example snippet:

module "gce-ilb" {
  source         = "github.com/GoogleCloudPlatform/terraform-google-lb-internal"
  region         = "${var.region}"
  name           = "group2-ilb"
  ports          = ["${module.mig2.service_port}"]
  health_port    = "${module.mig2.service_port}"
  source_tags    = ["${module.mig1.target_tags}"]
  target_tags    = ["${module.mig2.target_tags}","${module.mig3.target_tags}"]
  backends       = [
    { group = "${module.mig2.instance_group}" },
    { group = "${module.mig3.instance_group}" },
  ]
}
terraform-google-lb-http (global HTTP(S) forwarding rule)
This module creates a global HTTP load balancer for multi-regional content-based load balancing. You provide a reference to the managed instance group, optional certificates for SSL termination, and the module creates the http backend service, URL map, HTTP(S) target proxy, and the global http forwarding rule to route traffic based on HTTP paths to healthy instances.

The load balancing and backend services, with a path leading from google_compute_backend_service to google_compute_instance_group_manager.

Example snippet:

module "gce-lb-http" {
  source            = "github.com/GoogleCloudPlatform/terraform-google-lb-http"
  name              = "group-http-lb"
  target_tags       = ["${module.mig1.target_tags}", "${module.mig2.target_tags}"]
  backends          = {
    "0" = [
      { group = "${module.mig1.instance_group}" },
      { group = "${module.mig2.instance_group}" }
    ],
  }
  backend_params    = [
    # health check path, port name, port number, timeout seconds.
    "/,http,80,10"
  ]
}
Now get started!

Task 1. Clone the examples repository
Open a new Cloud Shell tab by clicking on the Activate Cloud Shell icon in the top right corner of the Cloud Console.

Run the following command to clone the terraform-google-examples repository:

git clone https://github.com/GoogleCloudPlatform/terraform-google-lb
cd ~/terraform-google-lb/examples/basic
Copied!
Task 2. Create a TCP load balancer with a regional forwarding rule
This lab creates a managed instance group with two instances in the same region and a network TCP load balancer.

Start by exporting your Project ID:
export GOOGLE_PROJECT=$(gcloud config get-value project)
Copied!
The terraform init command is used to initialize a working directory containing Terraform configuration files. This command performs several different initialization steps in order to prepare a working directory for use. This command is always safe to run multiple times, to bring the working directory up to date with changes in the configuration.

Run the terraform init command:
terraform init
Copied!
Example output:

Initializing modules...
- module.mig1
- module.gce-lb-fr

Initializing provider plugins...

The following providers do not have any version constraints in configuration, so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking changes, it is recommended to add version = "..." constraints to the corresponding provider blocks in configuration, with the constraint strings suggested below.

- provider.google: version = "~> 1.13"
- provider.null: version = "~> 1.0"
- provider.template: version = "~> 1.0"

Terraform has been successfully initialized! ... 
In the terraform-google-lb/examples/basic/variables.tf file, run the following commands to replace the default region with the lab assigned region of us-east4.
export REGION=us-east4
sed -i 's/us-central1/'"$REGION"'/g' variables.tf
Copied!
The terraform plan command is used to create an execution plan. Terraform performs a refresh, unless explicitly disabled, and then determines what actions are necessary to achieve the desired state specified in the configuration files. This command is a convenient way to check whether the execution plan for a set of changes matches your expectations without making any changes to real resources or to the state. For example, terraform plan might be run before committing a change to version control, to create confidence that it will behave as expected.

Run the terraform plan command:
terraform plan
Copied!
When asked to enter a value for your Project ID, use qwiklabs-gcp-01-158ac9567dd2.
The terraform apply command is used to apply the changes required to reach the desired state of the configuration, or the predetermined set of actions generated by a terraform plan execution plan.

Run the terraform apply command:
terraform apply
Copied!
When asked to enter a value for your Project ID, use qwiklabs-gcp-01-158ac9567dd2.

Type yes to continue when the next prompt occurs.

Example output:

...
Apply complete! Resources: 10 added, 0 changed, 0 destroyed.
The instances and load balancer are ready after a few minutes.

Check the status of your load balancer in the Cloud Console by navigating to Navigation Menu (Navigation menu) > View All Products. Under Networking section, click Network services > Load Balancing.

To be able to open the URL of the load balancer in a browser, run the following:

EXTERNAL_IP=$(terraform output | grep load_balancer_default_ip | cut -d = -f2 | xargs echo -n)
Copied!
echo "http://${EXTERNAL_IP}"
Copied!
Click on the http://${EXTERNAL_IP} address in the output to open the link to the load balancer.

Refresh a few times to observe traffic being balanced across the two instances in the us-east4 region.

Click Check my progress to verify the objective.
TCP load balancer with regional forwarding rule

Congratulations!
In this lab, you learned how to use load balancing modules. Using Terraform, you deployed a regional TCP load balancer, a regional internal TCP load balancer, a global HTTPS load balancer with Kubernetes Engine, and a HTTPS content-based load balancer.

Google Cloud training and certification
...helps you make the most of Goog