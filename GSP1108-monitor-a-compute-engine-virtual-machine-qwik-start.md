Monitor a Compute Engine Virtual Machine: Qwik Start
experiment
Lab
schedule
1 hour
universal_currency_alt
1 Credit
show_chart
Introductory
info
This lab may incorporate AI tools to support your learning.
Lab instructions and tasks
GSP1108
Overview
Objectives
Task 1. Create a Compute Engine VM instance
Task 2. Install an Apache Web Server
Task 3. Install and configure the Ops Agent
Task 4. Generate traffic and view metrics
Task 5. Create an alerting policy
Task 6. Test the alerting policy
Congratulations!
GSP1108
Google Cloud self-paced labs logo

Overview
In this lab you use Cloud Monitoring to generate traffic and view metrics on the predefined Apache dashboard in the Google Cloud console.

Objectives
In this lab, you learn how to perform the following tasks:

Create a Compute Engine VM instance.
Install an Apache Web Server.
Install and configure the Ops Agent for the Apache Web Server.
Generate traffic and view metrics on the predefined Apache dashboard.
Create an alerting policy.
Task 1. Create a Compute Engine VM instance
In the Google Cloud console, select Navigation menu > Compute Engine > VM Instances.

To create a VM instance, click Create instance.

Fill in the fields for your instance as follows:

In the Name field, enter quickstart-vm.
In the Machine type field, select e2-small.
Ensure the Boot disk is configured for Debian GNU/Linux 11 (bullseye).
In the Firewall field, select both Allow HTTP traffic and Allow HTTPS traffic.
Leave the rest of the fields at their default values.

Click Create. When your VM is ready, it appears in the list of instances in the Instances tab.
Click Check my progress to verify the objective.

Create a Compute Engine VM instance

gcloud compute instances create quickstart-vm --project=qwiklabs-gcp-01-0aac7ab58e9c --zone=us-central1-c --machine-type=e2-small --network-interface=network-tier=PREMIUM,stack-type=IPV4_ONLY,subnet=default --metadata=enable-oslogin=true --maintenance-policy=MIGRATE --provisioning-model=STANDARD --service-account=1008141584564-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/trace.append --tags=http-server,https-server --create-disk=auto-delete=yes,boot=yes,device-name=quickstart-vm,image=projects/debian-cloud/global/images/debian-11-bullseye-v20240815,mode=rw,size=10,type=pd-balanced --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --labels=goog-ec-src=vm_add-gcloud --reservation-affinity=any

Task 2. Install an Apache Web Server
To deploy an Apache Web Server on your Compute Engine VM instance, do the following:

To open a terminal to your instance, in the Connect column, click SSH.

To update the package lists on your instance, run the following command:

sudo apt-get update
Copied!
To install an Apache2 HTTP Server, run the following command:
sudo apt-get install apache2 php7.0
Copied!
Note: If the previous command fails, then use sudo apt-get install apache2 php. If asked to continue the installation, enter Y.
Open your browser and connect to your Apache2 HTTP server by using the URL http://EXTERNAL_IP, where EXTERNAL_IP is the external IP address of your VM. You can find this address in the External IP column of your VM instance.
Apache2 message: It works!

Click Check my progress to verify the objective.

Install an Apache Web Server
Task 3. Install and configure the Ops Agent
To collect logs and metrics from your Apache Web Server, install the Ops Agent by using the terminal:

To open a terminal to your VM instance, in the Connect column, click SSH.

To install the Ops Agent, run the following command:

curl -sSO https://dl.google.com/cloudagents/add-google-cloud-ops-agent-repo.sh
sudo bash add-google-cloud-ops-agent-repo.sh --also-install
Copied!
You should notice the google-cloud-ops-agent installation succeeded.

Copy the following command, then paste it into the terminal:
# Configures Ops Agent to collect telemetry from the app and restart Ops Agent.

set -e

# Create a back up of the existing file so existing configurations are not lost.
sudo cp /etc/google-cloud-ops-agent/config.yaml /etc/google-cloud-ops-agent/config.yaml.bak

# Configure the Ops Agent.
sudo tee /etc/google-cloud-ops-agent/config.yaml > /dev/null << EOF
metrics:
  receivers:
    apache:
      type: apache
  service:
    pipelines:
      apache:
        receivers:
          - apache
logging:
  receivers:
    apache_access:
      type: apache_access
    apache_error:
      type: apache_error
  service:
    pipelines:
      apache:
        receivers:
          - apache_access
          - apache_error
EOF

sudo service google-cloud-ops-agent restart
sleep 60
Copied!
The previous command creates the configuration to collect and ingest logs and metrics from the Apache Web Server. For more information about ingesting logs from the Apache Web Server, see Configure the Ops Agent for Apache Web Server.

Click Check my progress to verify the objective.

Install the Ops Agent
Task 4. Generate traffic and view metrics
Monitoring dashboards let you view and analyze metrics related to your services. In this quickstart, you generate metrics on your Apache Web Server and view metric data on the automatically created Apache Overview dashboard.

To generate metrics on your Apache Web Server, do the following:

In Google Cloud console, select Navigation menu > Compute Engine.

In the Connect column, click SSH to open a terminal to your VM instance.

To generate traffic on your Apache Web Server, run the following command:

timeout 120 bash -c -- 'while true; do curl localhost; sleep $((RANDOM % 4)) ; done'
Copied!
The previous command generates traffic by making a request to the Apache Web Server every four seconds.

To view the Apache Overview dashboard, do the following:

In the Google Cloud console, search for Monitoring in the top search bar and navigate to the Monitoring service.

In the navigation pane, select Dashboards.

In All Dashboards, select the Apache GCE Overview dashboard. The dashboard opens.

In the dashboard, there are several charts that contain information about your Apache and Compute Engine integration:

Apache Dashboard

Task 5. Create an alerting policy
To set up an email notification channel, do the following:

In Google Cloud console, select Navigation menu > Monitoring select Alerting and then click Edit notification channels.
Scroll down the page and click on Add new for Email.
Name the Email Channel: An email address you have access to
Enter the Display name.
Click Save.
Note: If you enter your own email address, you might get alerts until all the resources in the project have been deleted.
To create an alerting policy that monitors a metric and sends an email notification when the traffic rate on your Apache Web Server exceeds 4 KiB/s, do the following:

In Google Cloud console, select Navigation menu > Monitoring > Alerting and then click Create policy.

Select the time series to be monitored:

Click Select a metric and enter VM instance into the filter bar.
In the Active metric categories list, select Apache.
In the Active metrics list, select workload/apache.traffic. Click Apply.
The chart for Apache traffic is shown.

In the Transform data section, select the following values and click Next:

Rolling window: 1 min
Rolling window function: rate
In the Configure alert trigger section, select the following values and click Next:

Alert trigger: Any time series violates
Threshold position: Above threshold
Threshold value: 4000
In the Configure notifications and finalize alert section, select the following values:

Notification channels: Select the Display name you have created earlier and click OK.
Incident autoclose duration: 30 min
Name the alert policy: Apache traffic above threshold
Click Create policy. Your alerting policy is now active.

Click Check my progress to verify the objective.

Create an alerting policy
Task 6. Test the alerting policy
To test the alerting policy you just created, do the following:

In Google Cloud console, select Navigation menu > Compute Engine.

In the Connect column, click SSH to open a terminal to your VM instance.

In the terminal, enter the following command:

timeout 120 bash -c -- 'while true; do curl localhost; sleep $((RANDOM % 4)) ; done'
Copied!
The previous command generates traffic in your Apache Web Server.

After the traffic rate threshold value of 4 KiB/s is exceeded in your Apache Web Server, an email notification is sent. It might take several minutes for this process to complete.

The email notification you receive looks similar to the following:

Notification Alert

Congratulations!
In this lab, you learned how to install Ops Agent on a VM and use it to set an alerting policy to notify a recipient of potential issues with the instance.

Manual Last Updated March 20, 2024