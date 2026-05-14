---
title : "Custom Resources"
date: 2024-01-01
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

#### Custom Resources

- You can extend CloudFormation's usability with custom resources by delegating the work to be done to a specially designed Lambda function, to interact with the CloudFormation service.

- In the body of your code, you'll implement constructors, update, and delete, and the function that sends feedback about the state of the activity.

- In this first lab, you will create a custom resource that can generate an SSH key and store it in the SSM parameter store.

#### Content

1. [Create Lambda Function](4.1.1-createlambdafunction)
2. [Create Stack](4.1.2-createstack)
3. [Connect EC2 Instance](4.1.3-accessinstance)