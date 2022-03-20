---
layout: post
title: 'IAM Role and Policy ‘'
date: '2022-03-19 08:42:00'
Categories: AWS 
--- 

 ![CodeDeploy](https://www.jdrf.org/wp-content/uploads/2020/12/AWS-logo-2.jpg)

Today we are going to cover how to establish an IAM Role and Policy. IAM stands for Identity and Access Management; this is essentially the act of creating users within and giving them access to different things. The act of writing out what access these users have is what's known as a security policy.Users need credentials attached to it. This blog is going to be based on this [terraform documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role)

Our Code will look something like the code below. We call a resource block for the iam_role then we grant it a name of test role. The next part converts our terraform code to JSON which is what aws policies are written in. This specific policy is allowing the user access to ec2 functions. 

    resource "aws_iam_role" "test_role" {
    name = "test_role"

    # Terraform's "jsonencode" function converts a
    # Terraform expression result to valid JSON syntax.
    assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Sid    = ""
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      },
    ]
    })

    tags = {
     tag-key = "tag-value"
    }
    }
