# Soundless: Infrastructure Deployment Guide

This document outlines the steps required to clone and deploy the entire infrastructure for the Soundless project. Soundless is a mobile-cloud application designed to monitor sound levels and gather health data during sleep, ultimately helping to demonstrate and analyze nighttime noise pollution.

## Project Overview

The Soundless project comprises three main components:

1. **soundless-public-app:** The Android mobile application. This app records sound levels, integrates with Fitbit to collect health data, and transmits this information to a Google Cloud Pub/Sub topic.
2. **soundless-public-function:** A Google Cloud Function. This function acts as a receiver for the data sent by the mobile application and stores it in a designated Pub/Sub queue.
3. **soundless-public-batch:** A virtual machine (VM) script. This script runs daily, processing data from the Pub/Sub queue, transforming it into CSV files, and storing these files in Google Cloud Storage.

## Prerequisites

Before deploying the Soundless infrastructure, ensure you have the following:

*   **Google Cloud Platform (GCP) Account:** You will need an active GCP account with billing enabled to create and manage resources like Cloud Functions, Pub/Sub, and Virtual Machines.
*   **Firebase Project:**  A Firebase project is required for the Android application to function correctly. You will need to connect the app with a new package ID.
*   **Fitbit Developer Account:**  To access Fitbit health data, create a developer account on the Fitbit platform and obtain API credentials.
*   **Basic Understanding of GCP Services:** Familiarity with concepts like Pub/Sub, Cloud Functions, Cloud Storage, and VM instances will be beneficial.

## Deployment Steps

### 1. Mobile Application (soundless-public-app) Setup

1. **Clone the Repository:** Begin by cloning the `soundless-public-app` [repository](https://github.com/Soundless-URV/soundless-public-app) to your local machine.

    ```bash
    git clone https://github.com/Soundless-URV/soundless-public-app.git
    ```

2. **Firebase Integration:**
    *   Open the project in Android Studio.
    *   **Change the Application ID:** Modify the `package` in your `AndroidManifest` file to a unique package name different from the original URV-owned one. This is crucial for Firebase to recognize it as a distinct application.
    *   Connect the app to your Firebase project. Follow the Firebase documentation to add Firebase to your Android project. Download the `google-services.json` file and place it in your app module.

3. **Fitbit API Integration:**
    *   In your Fitbit developer account, create a new application.
    *   Obtain your Fitbit API credentials (Client ID, Client Secret, etc.).
    *   Replace the placeholder Fitbit credentials within the `soundless-public-app` code with your own credentials. Refer to the code comments and Fitbit API documentation for guidance on where to place these credentials.

4. **Build and Test:** Build and run the application on an Android device or emulator to ensure it compiles correctly and can connect to Firebase.

### 2. Google Cloud Function (soundless-public-function) Deployment

1. **Clone the Repository:** Clone the `soundless-public-function` [repository](https://github.com/Soundless-URV/soundless-public-function).

    ```bash
    git clone https://github.com/Soundless-URV/soundless-public-function.git
    ```

2. **Create a Pub/Sub Topic:**
    *   In the GCP console, navigate to Pub/Sub.
    *   Create a new topic. You can name it something like `soundless-data`. This topic will receive data from the mobile application.

3. **Deploy the Cloud Function:**
    *   You can deploy the Cloud Function directly from the code using the `gcloud` command-line tool or through the GCP console.
    *   Configure the environment variables within the Cloud Function, to specify the Pub/Sub topic name.

### 3. Batch Processing VM (soundless-public-batch) Setup

1. **Clone the Repository:** Clone the `soundless-public-batch` [repository](https://github.com/Soundless-URV/soundless-public-batch).

    ```bash
    git clone https://github.com/Soundless-URV/soundless-public-batch.git
    ```

2. **Create a Cloud Storage Bucket:**
    *   In the GCP console, go to Cloud Storage.
    *   Create a new bucket to store the generated CSV files.

3. **Create a VM Instance:**
    *   In the GCP console, navigate to Compute Engine and create a new VM instance.
    *   Choose an appropriate machine type and operating system for your needs.
    *   Consider setting up a startup script to automatically install dependencies and run the `soundless-public-batch` script.
    *   Or you can manually install the dependencies (e.g., Python, required libraries) and the script.

4. **Configure the Script:**
    *   Modify the `soundless-public-batch` script to:
        *   Subscribe to the correct Pub/Sub topic (`soundless-data`).
        *   Output CSV files to the Cloud Storage bucket you created.

5. **Schedule Script Execution:**
    *   Use `cron` (on Linux-based VMs) or Task Scheduler (on Windows-based VMs) to schedule the `soundless-public-batch` script to run daily.
    *   Alternatively, you can use Cloud Scheduler to trigger a Pub/Sub message that in turn triggers a Cloud Function that starts the VM, allowing for more complex scheduling logic if needed.

## Testing and Verification

After deploying all components, thoroughly test the entire system:

1. Run the mobile app and generate some test data (sound recordings and simulated or real Fitbit data).
2. Verify that messages are being published to the Pub/Sub topic.
3. Check that the Cloud Function is triggered and processes the messages.
4. Confirm that the VM script runs successfully and generates CSV files in the designated Cloud Storage bucket.

## Contributing

Contributions to the Soundless project are welcome! Please follow these steps:

1. Fork the repository.
2. Create a new branch for your feature or bug fix.
3. Commit your changes with clear and concise commit messages.
4. Submit a pull request.

## Contact

Daniel Alejandro Coll Tejeda - danielalejandro.coll@urv.cat