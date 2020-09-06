# LAB: Google Cloud Fundamentals: Getting Started with GKE

## Objectives

In this lab, I learnt how to perform the following tasks:

* Provision a Kubernetes cluster using Kubernetes Engine.
* Deploy and manage Docker containers using kubectl.

## Steps

1. Confirm that needed APIs (Kubernetes Engine API and Container Registry API) are enabled:
    ```
    gcloud services list --filter "kubernetes engine api" --filter "Container Registry API"
    ```

2. Start a Kubernetes Engine cluster

* Place the zone that Qwiklabs assigned you to into an environment variable:
    ```
    zone=us-central1-a
    ```
* Start a Kubernetes cluster managed by Kubernetes Engine. Name the cluster ```webfrontend``` and configure it to run ```2``` nodes:
    ```
    gcloud container clusters create webfrontend --zone=$zone --num-nodes=2
    ```
* Check the installed version of Kubernetes:
    ```
    kubectl version
    ```
* View your running nodes in the GCP Console:
    ```
    gcloud compute instances list
    ```

3. Run and deploy a container

* Launch a single instance of the nginx container:
    ```
    kubectl create deployment nginx --image=nginx:1.17.10
    ```
* View the pod running the nginx container:
    ```
    kubectl get pods
    ```
* Expose the nginx container to the Internet:
    ```
    kubectl expose deployment nginx --port=80 --type=LoadBalancer
    ```
* View the new service:
    ```
    kubectl get services
    ```
    *You can use the displayed external IP address to test and contact the nginx container remotely.*

* Open a new web browser tab and paste your cluster's external IP address into the address bar. The default home page of the Nginx browser is displayed.

* Scale up the number of pods running on your service:
    ```
    kubectl scale deployment nginx --replicas=3
    ```
* Confirm that Kubernetes has updated the number of pods:
    ```
    kubectl get pods
    ```
* Confirm that your external IP address has not changed:
    ```
    kubectl get services
    ```
* Return to the web browser tab in which you viewed your cluster's external IP address. Refresh the page to confirm that the nginx web server is still responding.

## The End.
---