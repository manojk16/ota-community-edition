# OTA Community Edition In Google Kubernetes Engine

## Build container

~~~
  ./contrib/gke/docker-build.sh
~~~

## Initialze gcloud Environment

A few commands need to be run to set up gcloud credentials:
~~~
 EXTRA_ARGS="-it" ./contrib/gke/gcloud auth login

 ./contrib/gke/gcloud config set project <YOUR PROJECT>
 ./contrib/gke/gcloud config set compute/zone us-central1-c
~~~

## Create Cluster

Create the k8s cluster with:
~~~
 ./contrib/gke/gcloud container clusters create ota-ce --machine-type n1-standard-2
 ./contrib/gke/gcloud container clusters get-credentials ota-ce
~~~

## Deploy In Kubernetes

~~~
  # Give yourself the ability to manage RBAC stuff
  ./contrib/gke/kubectl create clusterrolebinding cluster-admin-binding \
      --clusterrole=cluster-admin --user $(gcloud config get-value account)

  # SERVER_NAME = DNS name of gateway as used by aktualizr (ie ota-ce.example.com)
  # DNS_NAME = DNS root of all services ie "example.com"
  ./contrib/gke/make SERVER_NAME="TODO" DNS_NAME="TODO" new-server
  ./contrib/gke/make start-infra
  ./contrib/gke/make VAULTS="tuf-vault" start-vaults
  ./contrib/gke/make SERVER_NAME="TODO" DNS_NAME="TODO" start-services
~~~

## Cleaning Up
The setup can be completed removed with:
~~~
  # delete the cluster
  ./contrib/gke/gcloud container clusters delete ota-ce --quiet
~~~
