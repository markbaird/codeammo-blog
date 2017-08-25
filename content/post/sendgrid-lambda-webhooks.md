---
title: "Sendgrid Lambda Webhooks"
date: 2015-12-07T17:13:59-04:00
draft: false
disqusIdentifier: 566615585b2f765752483383
description: "How to receive SendGrid events in AWS Lambda"
keywords:
- AWS
- SendGrid
- NodeJS
- Lambda
- Webhooks
tags:
- AWS
- Lambda
- NodeJS
- SendGrid
- Webhooks
---
[SendGrid](https://sendgrid.com/) can be configured to send events like bounces and click tracking to a URL of your choice via their 
[Webhooks](https://sendgrid.com/docs/API_Reference/Webhooks/index.html) feature. This post describes how to configure an 
[AWS Lambda](https://aws.amazon.com/lambda/) function to process these SendGrid events. 
By using Lambda you can easily handle the large amount of traffic SendGrid Webhooks can generate, while keeping costs and
system administration work to a minimum.
<!--more-->

First you want to create a Lambda function on AWS and expose it via an API Gateway endpoint. The simplest way to do this is to choose a function blueprint such as 
`microservice-http-endpoint` so that the endpoint will be configured for you during the Lambda function creation. Alternatively you can go to the 
`API Endpoints` tab in the Lambda console later to create an endpoint. The important endpoint settings to ensure it can be accessed via SendGrid Webhooks are:

- API endpoint type: API Gateway
- Method: POST
- Security: Open


For the Lambda function, I've provided a trivial NodeJS example that simply verifies the SendGrid event type is one it is interested in, 
and then logs the event. If all you want is a log of all SendGrid events in [AWS CloudWatch Logs](https://aws.amazon.com/about-aws/whats-new/2014/07/10/introducing-amazon-cloudwatch-logs/) then this would suffice.


{{< codeblock lang="javascript" >}}
console.log('Loading function');

exports.handler = function(event, context) {
  console.log('Received event:', JSON.stringify(event, null, 2));
  var eventTypes = ["delivered", "open", "click", "bounce", 
                    "dropped", "spamreport", "unsubscribe"];

  for (var i = 0; i < event.length; i++) {
    var message = event[i];

    if (eventTypes.indexOf(message.event) > -1) {
      continue;
    }

    console.log('Received event:', JSON.stringify(message, null, 2));
  }

  context.done();
};
{{< /codeblock >}}


Now copy the API endpoint URL provided in the Lambda console and login to your SendGrid account. In the Webhooks section 
of your SendGrid account settings paste the endpoint URL and enable the events you are interested in. Now your Lambda 
function should begin to receive SendGrid Webhook events.
