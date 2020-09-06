# LAB: Google Cloud Fundamentals: Getting Started with App Engine

## Objectives

In this lab, I learnt how to perform the following tasks:

* Initialize App Engine.
* Preview an App Engine application running locally in Cloud Shell.
* Deploy an App Engine application, so that others can reach it.
* Disable an App Engine application, when you no longer want it to be visible.

## Steps

1. Initialize App Engine

* Initialize your App Engine app with your project and choose its region:
    ```
    gcloud app create --project=$DEVSHELL_PROJECT_ID --region=us-central
    ```
* Clone the source code repository for a sample application in the hello_world directory:
    ```
    git clone https://github.com/GoogleCloudPlatform/python-docs-samples
    ```
* Navigate to the source directory:
    ```
    cd python-docs-samples/appengine/standard_python3/hello_world
    ```
2. Run Hello World application locally

* Execute the following command to download and update the packages list:
    ```
    sudo apt-get update
    ```
* Set up a virtual environment in which you will run your application.
    ```
    sudo apt-get install virtualenv
    ```
    *If prompted [Y/n], press Y and then Enter.*
    ```
    virtualenv -p python3 venv
    ```
* Activate the virtual environment:
    ```
    source venv/bin/activate
    ```
* Navigate to your project directory and install dependencies.
    ```
    pip install  -r requirements.txt
    ```
* Run the application:
    ```
    python main.py
    ```
* In Cloud Shell, click Web preview (Web Preview) > Preview on port 8080 to preview the application.

* To end the test, return to Cloud Shell and press Ctrl+C to abort the deployed service.

* Verify that the app is not deployed using any of the following:
    ```
    gcloud app describe
    gcloud app services list
    gcloud app versions list
    ```
3. Deploy and run Hello World on App Engine

* Navigate to the source directory:
    ```
    cd ~/python-docs-samples/appengine/standard_python3/hello_world
    ```
* Deploy your Hello World application.
    ```
    gcloud app deploy
    ```
* Launch your browser to view the app at http://YOUR_PROJECT_ID.appspot.com
    ```
    gcloud app browse
    ```
* Copy and paste the URL into a new browser window to view the result.

4. Disable the application

* There is no command to undeploy an application, but one of the ways to disable an app that is currently serving is to delete the project running the app or use the console.
    ```
    gcloud projects delete $DEVSHELL_PROJECT_ID
    ```

## The End.
---