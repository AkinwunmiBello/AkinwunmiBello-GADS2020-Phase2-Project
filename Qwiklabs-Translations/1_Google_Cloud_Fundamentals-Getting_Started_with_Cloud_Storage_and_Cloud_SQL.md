# LAB: Google Cloud Fundamentals: Getting Started with Cloud Storage and Cloud SQL

## Objectives

In this lab, I learnt how to perform the following tasks:

* Create a Cloud Storage bucket and place an image into it.
* Create a Cloud SQL instance and configure it.
* Connect to the Cloud SQL instance from a web server.
* Use the image in the Cloud Storage bucket on a web page.

## Steps

1. Deploy a web server VM instance

* Create an instance named ```bloghost``` in the region and zone assigned by Qwiklabs, accept the default machine type, select the boot disk ```Debian GNU/Linux 9 (stretch)```, and enter the web server installation commands for the startup script:
    ```
    gcloud compute instances create bloghost --zone=us-central1-a --machine-type=e2-medium --image=debian-9-stretch-v20200805 --image-project=debian-cloud --subnet=default --metadata=startup-script=apt-get update$'\n'apt-get install apache2 php php-mysql -y$'\n'service apache2 restart --tags=http-server
    ```
    *To setup the instance to Allow HTTP traffic:*
    ```
    gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
    ```
* Get the bloghost VM instance's internal and external IP addresses copied to a text editor:
    ```
    gcloud compute instances list
    ```

2. Create a Cloud Storage bucket using the gsutil command line

* Enter your chosen location into an environment variable called LOCATION:
    ```
    LOCATION=US
    ```
* Make a bucket named after your project ID in selected location:
    ```
    gsutil mb -l $LOCATION gs://$DEVSHELL_PROJECT_ID
    ```
* Retrieve a banner image from a publicly accessible Cloud Storage location:
    ```
    gsutil cp gs://cloud-training/gcpfci/my-excellent-blog.png my-excellent-blog.png
    ```
* Copy the banner image to your newly created Cloud Storage bucket:
    ```
    gsutil cp my-excellent-blog.png gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
    ```
* Modify the Access Control List of the object you just created so that it is readable by everyone:
    ```
    gsutil acl ch -u allUsers:R gs://$DEVSHELL_PROJECT_ID/my-excellent-blog.png
    ```

3. Create the Cloud SQL instance

* Create a MySQL instance with ID blog-db and enter the password of your choice:
    ```
    gcloud sql instances create blog-db --zone=us-central1-a  --database-version=MySQL_5_7 --tier=db-n1-standard-1 --root-password=123
    ```
* Get the instance's public IP address and save it in an editor.
    ```
    gcloud sql instances list
    ```
* Create a user account named blogdbuser with a password of your choice:
    ```
    gcloud sql users create blogdbuser --instance=blog-db --password=123
    ```
* Configure the SQL instance to accept traffic from the webserver VM
    ```
    gcloud sql instances patch blog-db --authorized-networks=35.193.253.87/32 
    #The Subnet is the VM's external IP.
    ```

4. Configure an application in a Compute Engine instance to use Cloud SQL and a Cloud Storage object

* SSH into the VM instance bloghost:
    ```
    gcloud compute ssh bloghost --zone=us-central1-a
    ```
* Change your working directory to the document root of the web server:
    ```
    cd /var/www/html
    ```
* Use the nano text editor to edit a file called index.php:
    ```
    sudo nano index.php
    ```

* Paste the below content into the file:
    ```
    <html>
    <head><title>Welcome to my excellent blog</title></head>
    <body>
    <h1>Welcome to my excellent blog</h1>
    <?php
    $dbserver = "CLOUDSQLIP";
    $dbuser = "blogdbuser";
    $dbpassword = "DBPASSWORD";
    // In a production blog, we would not store the MySQL
    // password in the document root. Instead, we would store it in a
    // configuration file elsewhere on the web server VM instance.

    $conn = new mysqli($dbserver, $dbuser, $dbpassword);

    if (mysqli_connect_error()) {
            echo ("Database connection failed: " . mysqli_connect_error());
    } else {
            echo ("Database connection succeeded.");
    }
    ?>
    </body></html>
    ```
* In the nano text editor, replace CLOUDSQLIP with the Cloud SQL instance Public IP address that you noted above. Leave the quotation marks around the value in place.

* In the nano text editor, replace DBPASSWORD with the Cloud SQL database password that you defined above. Leave the quotation marks around the value in place.

* Also, to configure the application in a Compute Engine instance to use a Cloud Storage object:
    * Copy the public URL of **my-excellent-blog.png**.
    * Use the arrow keys to move the cursor to the line that contains the h1 element. Press Enter to open up a new, blank screen line (above the h1 element line), and then paste the URL you copied earlier into the line.
    * Paste this HTML markup immediately before the URL:
        ```
        <img src='
        ```
    * Place a closing single quotation mark and a closing angle bracket at the end of the URL:
        ```
        '>
        ```
    * The resulting line will look like this:
        ```
        <img src='https://storage.googleapis.com/qwiklabs-gcp-30018e6fa57ba019/my-excellent-blog.png'>
        ```

* Press Ctrl+O, and then press Enter to save your edited file. Press Ctrl+X to exit the nano text editor.

* Restart the web server:
    ```
    sudo service apache2 restart
    ```
* Open a new web browser tab and paste into the address bar your bloghost VM instance's external IP address followed by /index.php. The URL will look like this:
    ```
    35.193.253.87/index.php
    ```
* When the page loads, you would see a banner image with the welcome message.

## The End.
---