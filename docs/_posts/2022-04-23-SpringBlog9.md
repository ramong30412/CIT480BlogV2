---
layout: post
title: 'Kubernetes'
date: '2022-04-23 '
Categories: Docker  
---


![Kubernetes  Logo ](https://www.shapeblue.com/wp-content/uploads/2020/12/Kubernetes-logo.png)

In some of our previous blogs we have talked about Docker, today we’re discussing a tool that pairs really well with it and that's kubernetes. Kubernetes is an open source system for managing containerized applications, scaling, and deployment. Two terms we need to know to talk about kubernetes: the first is nodes, a node(minion) is a machine where containers are deployed, each node runs a container and nodes can be grouped to form clusters, think of a group of containers linked together through ports. The second term is pods, pods are the most basic objects in kubernetes and represent a single instances but can be one or more containers. To learn more about kubernetes read more about them [here](https://en.wikipedia.org/wiki/Kubernetes)

We will now go over how to run a simple kubernetes deployment. We will go off the documentation from [Kubernetes themselves](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). They teach us how to create three pods that deploy an nginx app. The code for it looks like this:
  
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deployment
      labels:
        app: nginx
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:1.14.2
            ports:
            - containerPort: 80

Some important things to note. Well split into three sections apiversion to first spect is section one, spec to the second spec is section 2 and second spec to the end of the code is section three. Section one of the code tell us what kind of stuff the fille will be doing as well as metadata, so in this case the file is deploying an app called nginx and were name the file nginx-deploymet. In section two of the code were say hey makre three pods that deploy nginx as well as setting a standard to keep track of the pods we see this under template any pod with the label nginx belongs to this. The last spec section tells us which version of nginx to pull from dockerhub and the port well be using. To deploy run this command **kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml**
