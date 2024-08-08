
# A Tour of Firebase

## GSP1132

### Overview

If you are new to cloud computing or looking for an overview of Google Cloud and Firebase, you are in the right place. 

Read on to learn about the specifics of this lab and areas that you will get hands-on practice with.

#### What you'll learn

In this lab, you will learn about the following:

- The lab platform, and how to identify key features of a lab environment

- How to access the Firebase Cloud console with specific credentials

- How to use the Firebase Cloud Navigation menu to identify types of Firebase services

#### Prerequisites

This is an introductory-level lab and the first lab you should take if you're unfamiliar with Firebase. 

If you are already experienced with Firebase Cloud console, consider taking one of the following labs:

- Getting Started with Firebase Web

- Getting started with Firebase Authentication

- Getting started with Cloud Firestore

### Setup and Requirements

#### Understanding Regions and Zones

Certain Compute Engine resources live in regions or zones. 

A region is a specific geographical location where you can run your resources. 

Each region has one or more zones. 

For example, the `us-central1` region denotes a region in the Central United States that has zones `us-central1-a`, `us-central1-b`, `us-central1-c`, and `us-central1-f`.

Regions|Zones
Western US|us-west1-a, us-west1-b
Central US|us-central1-a, us-central1-b, us-central1-d, us-central1-f
Eastern US|us-east1-b, us-east1-c, us-east1-d
Western Europe|europe-west1-b, europe-west1-c, europe-west1-d
Eastern Asia|asia-east1-a, asia-east1-b, asia-east1-c

Resources that live in a zone are referred to as zonal resources. 

irtual machine Instances and persistent disks live in a zone. 

To attach a persistent disk to a virtual machine instance, both resources must be in the same zone. 

Similarly, if you want to assign a static IP address to an instance, the instance must be in the same region as the static IP.

### Task 1. Initialize the demo project

The Firebase suite of tools is linked to a Google Cloud project, so you will see a project identifier and project name in the Firebase information.

Firebase projects are accessed via the Firebase console. Take a moment to open the Firebase console in a new Incognito window.

The first step to access a Firebase project is to select or create a project. Firebase provides a demo project for users to experience the environment.

### Task 2. Firebase console

In the Firebase user interface, the project **Overview** > **Project** settings menu option will display information about the project. 

A Firebase Project has a name, number, and ID. 

These identifiers are frequently used when interacting with Google Cloud services. 

You are working with one project to get experience with a specific service or feature of Firebase.

From the project setting page, you will also see any apps that have been registered under the current project.

Firebase includes support for a number of different language runtimes including:

- iOS
- Android
- Web
- Unity
- Flutter

### Task 3. Authentication

Firebase Authentication provides backend services, easy-to-use SDKs, and ready-made UI libraries to authenticate users to your app. 

It supports authentication using passwords, phone numbers, popular federated identity providers like Google, Facebook and Twitter, and more.

### Task 4. Hosting

Firebase Hosting is production-grade web content hosting for developers. 

With a single command, you can quickly deploy web apps and serve both static and dynamic content to a global CDN (content delivery network). 

You can also pair Firebase Hosting with Cloud Functions or Cloud Run to build and host microservices on Firebase.

Firebase Hosting can be used to deploy static and dynamic web content. 

It provides a global CDN distribution, automatic SSL certificates, and custom domain support.

### Task 5. Storage

Cloud Storage for Firebase is a powerful, simple, and cost-effective object storage service built for Google scale. 

The Firebase SDKs for Cloud Storage add Google security to file uploads and downloads for your Firebase apps, regardless of network quality.

Firebase Storage is a scalable, durable, and highly available object storage service for storing user-generated content. 

It is a great way to store images, videos, audio files, and other types of files in the cloud.

### Task 6. Cloud Firestore

Cloud Firestore is a flexible, scalable database for mobile, web, and server development from Firebase and Google Cloud. 

Firebase Cloud Firestore is a NoSQL document database. 

This means your data is stored in documents organized in collections. 

Documents can contain a variety of data types, including strings, numbers, arrays, objects, and geopoints. 

Like Firebase Realtime Database, it keeps your data in sync across client apps through real time listeners and offers offline support for mobile and web so you can build responsive apps that work regardless of network latency or Internet connectivity. 

Cloud Firestore also offers seamless integration with other Firebase and Google Cloud products, including Cloud Functions.

Firebase Cloud Firestore provides eventual consistency by default. 

This means that your data may not be immediately available to all clients after it is written. 

However, you can use transactions to ensure your data is always consistent.

### Task 7. Emulator Suite

The Emulator Suite consists of Firebase service emulators built to accurately mimic the behavior of Firebase services. 

This means you can connect your app directly to these emulators to perform integration testing or QA without touching production data.

For example, you could connect your app to the Cloud Firestore emulator to safely read and write documents in testing. 

These writes may trigger functions in the Cloud Functions emulator. 

However your app will still continue to communicate with production Firebase services when emulators are not available or configured.

### Congratulations

In just 30 minutes, you developed a solid understanding of the Firebase console and the platform's key features. 
