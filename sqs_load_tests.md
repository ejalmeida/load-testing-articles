Load Testing Simple Queue Service (SQS)
=======================================

Simple Notification Service (SNS)
---------------------------------
SNS is a publisher and subscriber service. Messages are sent to
topics and delivered to specified entities which include
mobile push, SMS, email, AWS Lambda, HTTP/S posts and SQS.

SQS
----
[DESCRIBE SQS]

We'll create a dummy SQS queue through the AWS CLI.
```
$

```

### Create Topic
Topics can be created through the AWS web console, the CLI or through the
Amazon SDKs.

Below is an example of creating a topic in the CLI.
```
$ aws sns create-topic --name testtopic
  {
      "TopicArn": "arn:aws:sns:eu-west-1:123XXXXXXXXX:testtopic"
  }

```

### Create Subscription
Lets, list our topics
```
$ aws sns list-topics
{
    "Topics": [
      {
        "TopicArn": "arn:aws:sns:eu-west-1:123XXXXXXXXX:testtopic"
      }
    ]
}
```
And create a subscription using aforementioned ARN value and protocol we wish
to use.
```
$ aws sns subscribe --topic-arn arn:aws:sns:eu-west-1:123XXXXXXXXX:testtopic --protocol sqs
```

Notes
-----
