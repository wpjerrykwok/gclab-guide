# APIs Explorer: Qwik Start

## GSP277

Overview
The Google APIs Explorer is a tool that lets you explore various Google API methods without writing code. With the APIs Explorer you can:

Browse quickly through available APIs and versions.
See methods available for each API and what parameters they support along with inline documentation.
Execute requests for any method and see responses in real time.
Make authenticated and authorized API calls.
Search across all services, methods, and your recent requests to quickly find what you are looking for.
The APIs Explorer uses its own API keys whenever it makes a request. When you use the APIs Explorer to make a request, it displays the request syntax, which includes a placeholder labeled {YOUR_API_KEY}. If you want to make the same request in your application, you need to replace this placeholder with your own API key.

Objectives
Create a Cloud Storage bucket.
Upload an image to Cloud Storage and make it public.
Make a request to the Vision API with that image.
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

student-01-c7aa6ba8c2e3@qwiklabs.net
Copied!
You can also find the Username in the Lab Details panel.

Click Next.

Copy the Password below and paste it into the Welcome dialog.

i3h5wBPiTCUw
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
Task 1. Create a Cloud Storage bucket
In the Cloud console, go to Cloud Storage > Buckets.
Click Create bucket.
Give your bucket a unique name. Do not include sensitive information in the bucket name - the bucket namespace is global and publicly visible. For example, qwiklabs-gcp-01-055ea60cedd0-bucket.
Click Choose how to control access to objects.
Uncheck Enforce public access prevention on this bucket.
Select Fine-grained Access control.
Click Create.
Test completed task
Click Check my progress to verify your performed task. If you have successfully created a Cloud Storage bucket, you will see an assessment score.

Create a Cloud Storage bucket.
Task 2. Upload an image
You'll be asking the Cloud Vision API to analyze an image through API Explorer. First, add an image to your bucket for it to analyze. You can use one of your own, or download the image below to your computer and save it as demo-image.jpg .

Dog Image

In your bucket, click Upload Files and select the demo-image.jpg file you saved.
Bucket details page in Cloud Storage with UPLOAD FILES text highlighted

Test completed task
Click Check my progress to verify your performed task. If you have successfully uploaded an image in a storage bucket, you will see an assessment score.

Upload an image in a storage bucket (demo-image.jpg).
After the file is uploaded and listed in your bucket, share the image publicly by following these steps:

Click the three vertical dot object overflow menu associated with your image.
Select Edit access from the drop-down menu.
In the overlay that appears, click the + Add entry button.
Add a permission for allUsers.
Select Public for the Entity.
Enter allUsers for the Name.
Select Reader for the Access.
Click Save.
Test completed task
Click Check my progress to verify your performed task. If you have successfully made the uploaded image publicly accessible, you will see an assessment score.

Make the uploaded image publicly accessible.
You should see that the image is now public. You're ready to use the API Explorer.

Task 3. Make a request to the Cloud Vision API service
Go to Navigation menu > APIs & Services.

Click + ENABLE APIS AND SERVICES, search for Cloud Vision, then select the Cloud Vision API from the results list and click on it.

Make sure that API is enabled, if not click Enable.

Open the Cloud Vision - Try this API link.

This will open a new tab with the APIs Explorer page loaded.

You will now be on the APIs Explorer page.

Note: In the section below the images.annotate method from Cloud Vision API is being used. You can view all API versions and its method on Cloud Vision API reference.
Click inside of the curly braces in the Request body field.
You'll be asked to select a property - choose "requests". This will generate the next level.
Click inside the brackets and click the blue plus sign icon, select [Add Item] - for your property select "features".
Inside "features" click inside the curly brace, click the blue plus icon and select [Add Item], select "type"; next to it select LABEL_DETECTION.
You should have the blue plus icon at the end of the "features" block where you can choose to add "image"; then add "source", and "imageUri".
Next to "imageUri" enter the path to the image file in your bucket. The path should look like this: gs://MY_BUCKET/demo-image.jpg
When you're done, your Request body field should look like this:

The Request body field, which includes the type: LABEL_DETECTION.

Make sure that Google OAuth 2.0 and API key checkboxes are selected under Credentials section.
Note: To view Credentials FAQs, click on question mark icon next to Credentials title.
On the right panel of an API Explorer console, you can see Cloud Vision API call with cURL, HTTP and JAVASCRIPT.

Now click Execute.

Select your student account.

On the next screen, click Allow to give APIs Explorer access.

The results of Cloud Vision API analysis of the image will be below to your right panel. The top part of the results should look like this:

The results of the Cloud Vision API analysis.

Task 4. Test your understanding
Try these multiple-choice questions to reinforce your understanding of this lab's concepts. Answer them to the best of your abilities.


Object names must be unique only within a given bucket.

True

False

When an object is shared publicly, any user with knowledge of the object URI can access the object for as long as the object is public.

True

False
Congratulations!
You've made your first images.annotate request to the Cloud Vision API service.

Finish your quest