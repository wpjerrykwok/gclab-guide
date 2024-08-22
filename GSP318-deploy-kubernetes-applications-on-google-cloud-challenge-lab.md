Deploy Kubernetes Applications on Google Cloud: Challenge Lab
experiment
Lab
schedule
1 hour 30 minutes
universal_currency_alt
5 Credits
show_chart
Intermediate
info
This lab may incorporate AI tools to support your learning.
Lab instructions and tasks
GSP318
Introduction
Challenge scenario
Task 1. Create a Docker image and store the Dockerfile
Task 2. Test the created Docker image
Task 3. Push the Docker image to the Artifact Registry
Task 4. Create and expose a deployment in Kubernetes
Congratulations!
GSP318
Google Cloud self-paced labs logo

Introduction
In a challenge lab you’re given a scenario and a set of tasks. Instead of following step-by-step instructions, you will use the skills learned from the labs in the course to figure out how to complete the tasks on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

When you take a challenge lab, you will not be taught new Google Cloud concepts. You are expected to extend your learned skills, like changing default values and reading and researching error messages to fix your own mistakes.

To score 100% you must successfully complete all tasks within the time period!

This lab is recommended for students who have enrolled in the Deploy Kubernetes Applications on Google Cloud skill badge course. Are you ready for the challenge?

Before you click the Start Lab button
Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click Start Lab, shows how long Google Cloud resources will be made available to you.

This hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

To complete this lab, you need:

Access to a standard internet browser (Chrome browser recommended).
Note: Use an Incognito or private browser window to run this lab. This prevents any conflicts between your personal account and the Student account, which may cause extra charges incurred to your personal account.
Time to complete the lab---remember, once you start, you cannot pause a lab.
Note: If you already have your own personal Google Cloud account or project, do not use it for this lab to avoid extra charges to your account.
Challenge scenario
You have just completed training on containers and their creation and management and now you need to demonstrate to the Jooli Inc. development team your new skills. You have to help with some of their initial work on a new project around an application environment utilizing Kubernetes. Some of the work was already done for you, but other parts require your expert skills.

You are expected to create container images, store the images in a repository, and expose a deployment in Kubernetes. Your know that Kurt, your supervisor, will ask you to complete these tasks:

Create a Docker image and store the Dockerfile.
Test the created Docker image.
Push the Docker image into the Artifact Registry.
Use the image to create and expose a deployment in Kubernetes
Your challenge
As soon as you sit down at your desk and open your new laptop you receive the following request to complete these tasks. Good luck!

Task 1. Create a Docker image and store the Dockerfile
Open Cloud Shell and run the following command. This will install the marking scripts you will use to help check your progress.
source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking_v2.sh)
Copied!
Use Cloud Shell to clone the valkyrie-app source code repository into the ~/marking directory. You can use the following command:
gcloud source repos clone valkyrie-app
Copied!
The app source code is in valkyrie-app/source.

Create valkyrie-app/Dockerfile and add the configuration below:
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app","-single=true","-port=8080"]
Copied!
Use valkyrie-app/Dockerfile to create a Docker image called valkyrie-app with the tag v0.0.3.

Once you have created the Docker image, and before clicking Check my progress, run the following command to perform the local check of your work:

bash ~/marking/step1_v2.sh
Copied!
After you get a successful response from the local marking you can check your progress.

Click Check my progress to verify the objective.
Create a Docker image and store the Dockerfile

Task 2. Test the created Docker image
Launch a container using the image valkyrie-app:v0.0.3.
You need to map the host’s port 8080 to port 8080 on the container.
Add & to the end of the command to cause the container to run in the background.
When your container is running you will see the page by Web Preview.

Once you have your container running, and before clicking Check my progress, run the following command to perform the local check of your work.
bash ~/marking/step2_v2.sh
Copied!
After you get a successful response from the local marking you can check your progress.

Click Check my progress to verify the objective.
Test the created Docker image

Task 3. Push the Docker image to the Artifact Registry
Create a repository named valkyrie-docker-repo in Artifact Registry. Use Docker as the format and use the us-east4 region as the location.

Before you can push or pull images, configure Docker to use the Google Cloud CLI to authenticate requests to Artifact Registry. You will need to set up authentication to Docker repositories. You can use the following command.

gcloud auth configure-docker us-east4-docker.pkg.dev
Copied!
Re-tag the container to be able push it to the repository. The format should resemble the following: LOCATION-docker.pkg.dev/PROJECT-ID/REPOSITORY/IMAGE.

Push the Docker image to the Artifact Registry.

Click Check my progress to verify the objective.
Push the Docker image to Artifact Registry

Task 4. Create and expose a deployment in Kubernetes
Kurt created the deployment.yaml and service.yaml to deploy your new container image to a Kubernetes cluster (called valkyrie-dev). The two files are in valkyrie-app/k8s.

Get the Kubernetes credentials using us-east4-a zone before you deploy the image onto the Kubernetes cluster.

Before you create the deployments, Make sure you check and replace some placeholder values in the deployment.yaml file and the format should be LOCATION-docker.pkg.dev/PROJECT-ID/REPOSITORY/IMAGE.

Create the deployments from the deployment.yaml and service.yaml files.

From the Navigation Menu, select Kubernetes Engine > Gateways, Services & Ingress. Click on the load balancer IP Address of the valkyrie-dev service to verify your services are up and running.

Click Check my progress to verify the objective.
Create and expose a deployment in Kubernetes

Congratulations!
Congratulations! In this lab you verified your skills on creating Docker container images, storing them in Artifact Registry, and using the stored image to create and expose a deployment in Kubernetes.

Deploy Kubernetes Applications on Google Cloud skill badge

Earn your next skill badge
This self-paced lab is part of the Deploy Kubernetes Applications on Google Cloud skill badge course. Completing this skill badge course earns you the badge above, to recognize your achievement. Share your badge on your resume and social platforms, and announce your accomplishment using #GoogleCloudBadge.

This skill badge course is part of Google's Cloud Hybrid and Multi-Cloud Cloud Architect learning path. If you have already completed the other skill badge course in this learning path, search the Google Cloud Skills Boost catalog for 20+ other skill badge course in which you can enroll.

Google Cloud training and certification
...helps you make the most of Google Cloud technologies. Our classes include technical skills and best practices to help you get up to speed quickly an