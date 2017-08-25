---
title: "AWS Config Compliance Notifications"
date: 2017-02-22T17:14:19-04:00
draft: false
disqusIdentifier: 58ade2d578e558a2540114cc
description: "AWS Config Compliance Notifications"
keywords:
- AWS-Config
- AWS-Lambda
- AWS
- NodeJS
- Notifications
tags:
- AWS
- Lambda
- NodeJS
---
[AWS Config](https://aws.amazon.com/config/) is becoming a very handy service for monitoring compliance rules within your 
AWS account. I find it especially useful in my current job for verifying all EBS volumes and RDS instances have encryption 
enabled to meet HIPAA compliance requirements. My one frustration with this service is the lack of user-friendly 
notifications when a compliance issue is discovered. You either have to check the web console regularly or have your email 
inbox blown up with a constant stream of AWS Config notifications. So today I took the time to create an AWS Lambda function 
that will process AWS Config notifications and only email you the ones that indicate a change in compliance status.
<!--more-->

I have published this Lambda function to GitHub [here](https://github.com/markbaird/aws-config-compliance-notifications) 
along with setup instructions. The current version uses the Amazon 
SES service to send notification emails. The function is written in NodeJS. I've used [the Apex framework](https://github.com/apex/apex) to build and deploy 
the Lambda function, but it is so simple you could easily copy/paste the function code into your AWS Lambda console. The 
function uses two Lambda environment variables to determine the from address and the to addresses in the notification emails.

It would be fairly easy to modify the Lambda function to send notifications elsewhere. For example the function could post metrics to CloudWatch, or send the notifications to a Slack channel. If you use this I would love to hear about it, and pull requests are welcome.

-------------

Project link on GitHub: [AWS Config Compliance Notifications](https://github.com/markbaird/aws-config-compliance-notifications)
