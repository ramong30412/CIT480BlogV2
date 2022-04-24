---
layout: post
title: 'Terraform Lambda'
date: '2022-03-05 9:42:00'
Categories: Terraform
---

 ![Lambda Logo](https://static.wikia.nocookie.net/half-life/images/d/dc/Lambda_logo.svg/revision/latest/scale-to-width-down/365?cb=20100327174546&path-prefix=en)

I wanted to add to our terraform blog series, we’ve previously covered VPC and EC2. Today we will cover Lamda. What is Lambda and why use it? So Lambda is a serverless code computing service, essentially lets you run code functions. One advantage of Lambda apart from not having to worry about infrastructure is that it automatically manages the resources you are using so you only pay for what you use. You can use lambda with a couple different languages: Java, Go, PowerShell, Node. js, C#, Python, and Ruby code; with the option to run API’s for additional languages.All you have to do is write your code in one of these languages and put it on a zip file for the lambda service to read through. One of the most common uses for lambda is using it when you only need to run functions for a short amount of time. 

To set up the terraform for Lamda we’re going to want to visit the documentation for it at the [terraform website](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lambda_function). The basic example will be displayed and will show two important things the first is the **lambda resource block**  and the second is the **IAM Policy Attachment** resource block. These two are super important because they make up the two requirements you need for lambda function ( a function name and a role specification which falls under the policy attachment. 
Code Block for lambda resource:


    resource "aws_lambda_function" "test_lambda" {
    function_name = var.lambda_function_name

    # ... other configuration ...
    depends_on = [
    aws_iam_role_policy_attachment.lambda_logs,
    aws_cloudwatch_log_group.example,
    ]
    }
 Code BLock for Role
 
    resource "aws_iam_role_policy_attachment" "lambda_logs" {
    role       = aws_iam_role.iam_for_lambda.name
    policy_arn = aws_iam_policy.lambda_logging.arn
    }

In our example well create a lambda.tf file and a pytho file with our function. Some important things to note is that we specified a location ffor lambda to find out python file, we gave it a role, make sure that the IAM role we give does indeed exist, runtime which is you specifying what language its going to use a handler which is what allows us to execute the code.Well keep it simple our lambda.tf will look something like this.

    resource "aws_lambda_function" "blogexample" {
    source        = "./pythonfunctionfilelocation"
    filename      = "helloblog"
    role          = "${aws_iam_role.SomeRoleWeMade.arn}"
    runtime       = "python 2.7"
    handler       = "whateverwenamedthehandlerinthepythongfile"
    
  Then we will have a python file in a zip folder lets say helloblog.zip whith a python file inside lets say helloblog.py and a basic function to run. We're going to make a simple function that just prints a message.That's pretty much all there is to it, you can write functions that call upon other existing functions and link everyhting to an S3 bucket and get more and more complex if you need. 
  
     def handlernamewewant (event,context)
     return 'Hello Blog it works!'
  
  
    
