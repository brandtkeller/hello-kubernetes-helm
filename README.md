# Hello Kubernetes With Helm
This repository is meant to be a learning exercise for converting static kubernetes (k8s) `*.yaml` resources into a templated package that helm can manage.

Note: This tutorial is paired-down to remove excess "fluff" that comes when creating a chart from scratch with `helm create <appname>`. I wanted to focus on the core concepts of templating, k8s resources, and chart values in a meaningful manner.

## Tiered Learning
The course is broken down into these tiers:
- Basics
    - migrate static configmap resource
    - migrate static deployment resource
    - migrate static service resource
    - Check, deploy, and test the chart
- Intermediate
    - (Under construction - TBD)
    - Creating a more dynamic deployment
        - replicas
        - resources
        - labels, annotations
        - volumes/ volumeMounts
    - Ingress resources
    - Multiple non-conflicting resources
    - Understanding the NOTES.txt file and what it offers the consumers
    - Introduction to writing chart tests

## Getting Started
In the [Instructions](./instructions) directory there are step-by-step guides for working through this tutorial.
    * The files in the directory are in markdown formatting. If working through this tutorial in an IDE such as VSCode:
        * Click in the file contents and hit `ctrl + shift + v` to enter preview for markdown.
        * This will hide the hints, tips and finished content from displaying by default.

## Objective
The objective for this tutorial is to further your understanding of Helm through the conversion of static kubernetes manifests into a packaged helm chart that is extensible and portable.

This will require some prior-knowledge of kubernetes resources such as pods, deployments, services, ingress etc. I will be working to cater this tutorial to provide any kubernetes context that may be helpful in learning both Kubernetes and Helm.

## Deployment
When you are ready to deploy this against a kubernetes cluster
* `helm upgrade --install <release-name> ../hello-kubernetes/ -n <namespace>`
    * IE `helm upgrade --install hello ../hello-kubernetes -n test`

## Testing
The test suite will run tests against the items you converted and deployed to a cluster to ensure you have met all the required criteria for this tutorial.

To run the test suite:
* `helm test <release-name> -n <namespace>`
