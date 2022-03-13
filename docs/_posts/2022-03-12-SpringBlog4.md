---
layout: post
title: 'Terraform Lambda'
date: '2022-03-12 '
Categories: Terraform
---

 ![CodeDeploy](https://news-assets.spot.io/app/uploads/2018/03/17184004/aws-codedeploy-1292x800.png)

Our last Blog was on the topic of LAMBDA. I figured a good next step would be to talk about CodeDeploy. What is CodeDeploy, you ask? CodeDeploy is a deployment service that automates the application and software deployment.You can use it to deploy on Amazon EC2, AWS Fargate, AWS Lambda, and your on-premises servers. Some of the advantages of CodeDeploy is that it allows for lower downtimes during application deployments,makes it easy to track application status, and once set up allows for constant and quick redeployment. 


The[terraform documentation on how to set up CodeDeploy will be here. website](https://registry.terraform.io/modules/terraform-aws-modules/lambda/aws/latest/submodules/deploy). It will have  a few different resource code blocks, focus on the one that says LAMBDA application. 

Code Block for lambda resource: It defines the compute_platfrom (ec2,lambda,server etc) and we give it a name. 

	resource "aws_codedeploy_app" "example" {
 	compute_platform = "Lambda"
    name             = "example"
    }

The next step would be the deployment configuration.This will be conposed of two resource blocks the first is the codeploy_deployment_config and the second one is the deployment_group reseouce block. The first one is setting up traffic configurations, we made ours a TimeBasedLinear set up which shifts traffic from one lambda function to another in equal increments with equal periods of time between intervals.The Second resource block is setting up a few different things. The fist and maybe most important is the service role which is essentially referencing one of our IAM roles and the permissions that come with it. The second thing it does it that it set up an automatic roll back in case our deployment fails, well just go back to our previous running version reducing our downtime.

    resource "aws_codedeploy_deployment_config" "foo" {
    deployment_config_name = "test-deployment-config"
    compute_platform       = "Lambda"

    traffic_routing_config {
      type = "TimeBasedLinear"

      time_based_linear {
        interval   = 10
        percentage = 10
        }
      }
    }

    resource "aws_codedeploy_deployment_group" "foo" {
    app_name               = aws_codedeploy_app.foo_app.name
    deployment_group_name  = "bar"
    service_role_arn       = aws_iam_role.foo_role.arn
    deployment_config_name = aws_codedeploy_deployment_config.foo.id

    auto_rollback_configuration {
      enabled = true
      events  = ["DEPLOYMENT_STOP_ON_ALARM"]
    }

      alarm_configuration {
      alarms  = ["my-alarm-name"]
      enabled = true
    }
    }

