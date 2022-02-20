---
layout: post
title: 'Setting up an Elastic Load Balancer'
date: '2022-02-19 9:42:00'
Categories: Terraform 
---

In a previous blog we went through how to [set up a VPC] (https://github.com/ramong30412/CIT480BlogV2/blob/main/docs/_posts/2021-12-10-Blog11.md) on terraform this will be a follow up on that blog showing how to set up an elastic load balancer. But first what is a load balancer and what does it do for us. An Elastic Load Balancer or ELB for short is a service provided by AWS that allows us to automatically distribute incoming traffic over multiple zones and ec2s. Another neat use for it is that it allows us to monitor the health and performance of our applications as well as automatically scale up or down as needed. 

![One example of ELB usage](https://media.amazonwebservices.com/blog/2014/elb_instances_1.png)

So how do we set up our terraform ELB? Well first we need to have a few things set up. We need a subnet and an ec2. We have already created the subnets in our previous blog, to create the ec2 we will need to call the ec2 resource from terraform. The code for that would look something like this: 

    resource "aws_instance" "cit480" {
    ami           = "ami-00f7e5c52c0f43726"
    subnet_id = aws_subnet.public[0].id
    instance_type = "t2.micro"
    key_name = "Terraform"
    tags = {
      Name = "cit480"
    }
      }
We won't really go into the meaning of it but essentially its creating an ec2 of type micro with the linux os flavor. Now that we have that we need to reference the [terraform documentation for ELB](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/elb) here we see that the basic structure is going to be the elb resource block where you give it a name and the regions you'll use. Then any listeners you want as well as a health check template and an ec2 segment where we attach it to that instance. Key word here is **listener**, which are what acknowledges client requests. 
  
    resource "aws_elb" "terraform-elb" {
    name = "terraform-elb"
    subnets = [aws_subnet.public[0].id]
      listener {
      instance_port = 8000
      instance_protocol = "http"
      lb_port = 80
      lb_protocol = "http"
    }
    health_check {
      healthy_threshold = 2
      unhealthy_threshold = 2
      timeout = 3
      target = "HTTP:8000/"
      interval = 30
    }
    instances = [aws_instance.cit480.id]
    cross_zone_load_balancing = false
    idle_timeout = 400
    connection_draining = true
    connection_draining_timeout = 400
    }

Here’s how our code would look if we followed the last block. We did a few changes from the terraform document, if you notice instead of the listening zone we listed a subnet that's already associated with a specific zone in our case us-west-2a. Our listener is checking http requests. Our health check pretty much got left on the default and our instance got attached to the elb in the last code block. Hopefully this has given you guys some insight on how to set up an ELB. 

