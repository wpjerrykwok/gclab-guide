# Creating and Alerting on Logs-based Metrics

## GSP091

### Overview

Log-based metrics are Cloud Monitoring metrics that are based on the content of log entries. 

These metrics can help you identify trends, extract numeric values out of the logs, and set up an alert when a certain log entry occurs by creating a metric for that event. 

You can use both system and user-defined log-based metrics in Cloud Monitoring to create charts and alerting policies.google

The log-based metrics interface is divided into two metric-type panes: System metrics and User-defined metrics.

System-defined log-based metrics are provided by Cloud Logging for use by all Google Cloud projects.

They calculated only from logs that have been ingested by Logging. 

If a log has been explicitly excluded from ingestion, it isn't included in these metrics.

User-defined log-based metrics are created by you to track things in your Google Cloud project. 

For example, you might create a log-based metric to count the number of log entries that match a given filter.

Creating an alert from a metric lets you create an alerting policy based on the log-based metric.

#### Objectives

In this lab you will learn how to:

- Create a log-based alert

- Create a system-defined log-based metric

- Create a user-defined log-based metric

- Create an alert for the user-defined log-based metric

#### Setup and requirements

A virtual machine was created when this lab started. 

Make sure you see the green Lab Running light on the page where you started the lab before continuing.

Additionally, connect to a Google Kubernetes Engine cluster and validate that it's been created correctly.

Set the zone in `gcloud`

```bash
gcloud config set compute/zone us-east1-c
```

Then **Authorize** Cloud Shell.

Set the project ID:

```bash
export PROJECT_ID=$(gcloud info --format='value(config.project)')
```

Deploy a standard GKE cluster, which will prompt you to authorize and enable the GKE API.

```bash
gcloud container clusters create gmp-cluster --num-nodes=1 --zone us-east1-c
```

### Task 1. Log-based alert

Log-based alerts notify you whenever a specific message appears in your logs. 

Try it out by setting up a log-based alert to tell you when a VM stops running.

From Cloud Console, in the Search bar, type in “logs explorer”, then click on the **Logs Explorer** result.

Click the **Show Query** slide bar.

Enter the following parameters to create Log Based Alert:

```bash
resource.type="gce_instance" protoPayload.methodName="v1.compute.instances.stop"
```

Click **Create alert** link.

Add the following parameters, click **Next** to move to the next parameter.

- **Alert name**: `stopped vm`
- **Choose logs to include in the alert**: will auto-fill with the query you entered
- **Set notification frequency and autoclose duration**: Time between notifications is `5 min` and Incident autoclose duration is `1 hr`. Click **Next**.
- **Who should be notified (optional)**:
  - Click on the dropdown arrow next to **Notification Channels**, then click on **Manage Notification Channels**.
  - A Notification channels page will open in the new tab.
  - Scroll down the page and click on **ADD NEW** for **Email**.
  - Enter your personal email in the **Email Address** field and a **Display name**.
  - Click **Save**.
  - When done, return to the Logs Explorer tab you were in previously.
  - Refresh the Notification Channels, then select the channel you just created. Click **OK**.

Click **Save**.

You will now cause your VM to stop.

Go to the 2nd Cloud Console tab, and navigate to **Navigation menu** > **Compute Engine** > **VM instances**.

Check the box next to **instance1**, then click **Stop** at the top of the page, then click **Stop** again in the pop-up window. The green check mark will turn to a gray circle when the instance has been stopped.

In the Search bar, type "monitoring", then choose the **Monitoring** option.

Click on the **Alerting** tab. You'll see that your alert has registered. Under Alert Policies click the **See all policies** link and you'll see the log-based alert you created listed.

### Task 2. Log-based metric

Using log-based metrics you can define a metric that tracks errors in the logs to proactively respond to similar problems and symptoms before they are noticed by end users.

At the beginning of the lab you deployed a standard GKE cluster. Run the following command to ensure that the cluster named `gmp-cluster` has been created:

```bash
gcloud container clusters list
```

> If your cluster status says PROVISIONING, wait a moment and run the command above again. Repeat until the status is RUNNING.

Authenticate the cluster:

```bash
gcloud container clusters get-credentials gmp-cluster
```

You should see the following message:

`Fetching cluster endpoint and auth data.`
`kubeconfig entry generated for gmp-cluster.`

Create a namespace to work in:

```bash
kubectl create ns gmp-test
```

Now run the following to deploy a simple application that emits metrics at the `/metrics` endpoint

```bash
kubectl -n gmp-test apply -f https://storage.googleapis.com/spls/gsp091/gmp_flask_deployment.yaml
```

```bash
kubectl -n gmp-test apply -f https://storage.googleapis.com/spls/gsp091/gmp_flask_service.yaml
```

Verify that the namespace is ready and emitting metrics:

```bash
kubectl get services -n gmp-test
```

You should see the following:

```bash
NAME    TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)        AGE
hello   LoadBalancer   10.0.12.114   34.83.91.157   80:32058/TCP   71s
```

Re-run the command until you see the **External-IP** address populated.

Check that the Python Flask app is serving metrics with the following command:

```bash
curl $(kubectl get services -n gmp-test -o jsonpath='{.items[*].status.loadBalancer.ingress[0].ip}')/metrics
```

You should see the following:

```bash
# HELP flask_exporter_info Multiprocess metric
# TYPE flask_exporter_info gauge
flask_exporter_info{version="0.18.5"} 1.0
```

### Task 3. Create a log-based metric

Return to **Logs Explorer**.

Click **Create metric** link.

On the Create metric page, input the following:

- **Metric type**: leave the default setting, Counter

- **Log based metric name**: `hello-app-error`

- **Filter selection**: update the following into the Build filter:

```bash
severity=ERROR
resource.labels.container_name="hello-app"
textPayload: "ERROR: 404 Error page not found"
```

Click **Create metric**.

### Task 4. Create a metrics-based alert

In the left pane of **Logging** window select **Log-based Metrics**. Then in user-defined metrics click on **3 vertical dots** next to metrics and select **Create alert from metric**.

Under **Select a Metric**, the metric parameters will automatically fill in.

- Update the Rolling window to **2 min**.

- Accept the other default settings

- Click **Next**.

You will need to set Notifications. Feel free to re-use the channel you created earlier in the lab.

Name the alert policy `log based metric alert`.

Click **Create Policy**.

### Task 5. Generate some errors

Next you'll generate some errors to match the log-based metric you created and trigger the metric-based alert.

In Cloud Shell, run the following to generate some errors:

```bash
timeout 120 bash -c -- 'while true; do curl $(kubectl get services -n gmp-test -o jsonpath='{.items[*].status.loadBalancer.ingress[0].ip}')/error; sleep $((RANDOM % 4)) ; done'
```

Return to the **Logs Explorer** page, and go to the Severity section on the lower left side. Click on the **Error** severity. Now you can search for the `404 Error page not found` error. View more information by expanding one of the 404 Error messages.

Return to the **Monitoring** page, and click on **Alerting**. You will see the 2 policies you created.

Click on the **Alert policies** link, and you should see both alerts in the Incidents section. Click on an incident to see details.

> Note: The log-based metric alert will eventually resolve itself. If you need more time to investigate, run the errors script again and wait for the alert to be triggered again.

### Congratulations!

Congratulations! 

In this lab, you created a log-based alert, a system-defined log-based metric, a user-defined log-based metric, and a metric-based alert. 

You also generated some errors to trigger the alert. 

Lastly, you learned how to view the incidents and details of the alerts.
