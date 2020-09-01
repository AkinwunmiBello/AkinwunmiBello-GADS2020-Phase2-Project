# LAB: Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL

## Objectives

In this lab, I learnt how to perform the following tasks:

* Create a Cloud Storage bucket and place an image into it.
* Create a Cloud SQL instance and configure it.
* Connect to the Cloud SQL instance from a web server.
* Use the image in the Cloud Storage bucket on a web page.

## Steps

1. Deploy a web server VM instance
    ```
    gcloud compute instances create bloghost --zone=us-central1-a --machine-type=e2-medium --subnet=default --metadata=startup-script=apt-get update$'\n'apt-get\ install\ apache2\ php\ php-mysql\ -y$'\n'service\ apache2\ restart --tags=http-server --image=debian-9-stretch-v20200805 --image-project=debian-cloud //to create the VM

    gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server //to configure firewall rule
    ```

2. Create a Cloud Storage bucket using the gsutil command line
    ```
    LOCATION=US //creating an environment variable for bucket storage location

    gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID //to create a bucket with project ID as name

    gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png //to retrieve a banner image from a publicly accessible Cloud Storage location

    gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png //to copy the banner image to the newly created Cloud Storage bucket

    gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png //to make the object publicly available
    ```

3. Create the Cloud SQL instance
    ```
    gcloud sql instances create blog-db --zone=us-central1-a  --database-version=MySQL_5_7 --tier=db-n1-standard-1 --root-password=123 //to create a cloud SQL instance

    gcloud sql users create blogdbuser --instance=blog-db --password=123  //to create a MySQL user account

    gcloud sql instances patch blog-db --authorized-networks=35.193.253.87/32 //to configure access from the webserver VM to the SQL instance
    ```

4. Configure an application in a Compute Engine instance to use Cloud SQL and a Cloud Storage object
    ```
    gcloud compute ssh bloghost --zone=us-central1-a //to SSH into the web server VM

    cd /var/www/html //to change the working directory to the document root of the web server

    sudo nano index.php //using the nano text editor to edit the index.php file

    sudo service apache2 restart //to restart the web server
    ```

All that is left will be to test the URL `35.193.253.87/index.php` to get a `Database connection succeeded.` response and to see a banner image on the page.