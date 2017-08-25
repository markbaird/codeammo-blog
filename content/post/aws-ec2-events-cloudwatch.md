---
title: "EC2 Scheduled Events as a CloudWatch Metric"
date: 2017-02-24T17:49:21-04:00
description: "EC2 Scheduled Events as a CloudWatch Metric"
disqusIdentifier: 58b0a24b5c90442954cfdf51
draft: false
tags:
- AWS
- CloudWatch
- EC2
- Lambda
- NodeJS
keywords:
- AWS
- EC2
- AWS-Lambda
- CloudWatch
- event
- notification
- NodeJS
---

AWS will sometimes [schedule an event to occur on your EC2 instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-instances-status-check_sched.html) 
in order to perform maintenance of the host servers. The most common event will be a scheduled stop/start sequence of your 
instance which allows AWS to move your instance onto different hardware. When an event is scheduled for one of your instances 
the only notification AWS generates is an email to your AWS root account. In my experience the AWS root account is usually 
associated with the CEO or CIO's email address who may assume others are copied on the notification email, or may simply 
misunderstand or ignore the notification all together. It is almost always best to perform the scheduled event yourself, 
at a time that is best for you instead of letting AWS automatically reboot your servers in the middle of a busy day. 
However, in order to do that you need to know about it ahead of time. I've written a Lambda function to periodically scan 
for EC2 scheduled events and post the result as a CloudWatch metric which can then be configured to alert the appropriate personnel. 

I have published this Lambda function to GitHub [here](https://github.com/markbaird/aws-ec2-events-cloudwatch) along with 
setup instructions. I've used [the Apex framework](https://github.com/apex/apex) to build and deploy the Lambda function, 
but it is so simple you could easily copy/paste the function code into your AWS Lambda console.

If you use this I would love to hear about it, and pull requests are welcome.

Project link on GitHub: [Expose AWS EC2 scheduled events as a CloudWatch Metric](https://github.com/markbaird/aws-ec2-events-cloudwatch)