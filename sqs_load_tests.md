Load Testing Simple Queue Service (SQS) with Simple Notification Service (SNS)
==============================================================================

SQS
----
SQS is a highly scalable managed queue service for storing, retrieving,
processing and deleting messages. It is temporary repository for messages
awaiting processing and allows the decoupling of software systems and
components. The default standard queue offers a "nearly unlimited" number of
transactions per second.

We'll create a dummy SQS queue (with the default attributes) using the AWS CLI.
```
$ aws sqs create-queue --queue-name testqueue
{
    "QueueUrl": "https://eu-west-1.queue.amazonaws.com/XXXXXXXXXXXX/testqueue"
}
```

SNS
---
SNS is a publisher and subscriber service. Messages are sent to topics and
delivered to specified entities which include mobile push, SMS, email, AWS
Lambda, HTTP/S posts and SQS. SNS is parallel and asynchronous.


### Create Topic
```
$ aws sns create-topic --name testtopic
{
    "TopicArn": "arn:aws:sns:eu-west-1:XXXXXXXXXXXX:testtopic"
}
```

### Create Subscription
Lets, list our topics
```
$ aws sns list-topics
{
    "Topics": [
      {
        "TopicArn": "arn:aws:sns:eu-west-1:XXXXXXXXXXXX:testtopic"
      }
    ]
}
```

And create a subscription using the aforementioned topic ARN value, protocol
and SQS endpoint in ARN format.

```
$ aws sns subscribe --topic-arn arn:aws:sns:eu-west-1:XXXXXXXXXXXX:testtopic --protocol sqs --notification-endpoint arn:aws:sqs:eu-west-1:XXXXXXXXXXXX:testqueue
{
  "SubscriptionArn": "arn:aws:sns:eu-west-1:XXXXXXXXXXXX:testtopic:XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

SNS Gateway
-----------
The [SNS Gateway](https://github.com/b3tyar/snsgateway) is a Go
application that enables us to send SNS messages though cURL.

### Download
```
$ wget https://raw.githubusercontent.com/b3tyar/snsgateway/master/snsgateway.go
```

### Run
Pass the topic arn and region.
```
$ go run snsgateway.go --snsarn arn:aws:sns:eu-west-1:XXXXXXXXXXXX:testtopic --region eu-west-1 --maxMessagesPerMinute 1000
```

Load Test Script
----------------
The below script sends 100 messages with an inteval of 10 seconds, for 3 times.
```
#!/bin/bash

for i in {1..3}
do
  for j in {1..100}
  do
    curl 127.0.0.1:8080 -d "{iteration$i: \"test$i\"}"
  done
  sleep 10
done
```

Issues
------
