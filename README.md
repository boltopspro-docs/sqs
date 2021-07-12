<!-- note marker start -->
**NOTE**: This repo contains only the documentation for the private BoltsOps Pro repo code.
Original file: https://github.com/boltopspro/sqs/blob/master/README.md
The docs are publish so they are available for interested customers.
For access to the source code, you must be a paying BoltOps Pro subscriber.
If are interested, you can contact us at contact@boltops.com or https://www.boltops.com

<!-- note marker end -->

# SQS Queue CloudFormation Blueprint

[![BoltOps Badge](https://img.boltops.com/boltops/badges/boltops-badge.png)](https://www.boltops.com)

This blueprint provisions an SQS Queue.  This is useful as an example to experiment and learn about SQS queues before cleaning up the resources cleanly.

* Several [SQS Queue](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-sqs-queues.html) properties are configurable with [Parameters](https://lono.cloud/docs/configs/params/). Additionally, properties that require further customization are configurable with [Variables](https://lono.cloud/docs/configs/shared-variables/).  The blueprint is quite flexible and configurable.
* Provisions at [AWS::SQS::QueuePolicy](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-sqs-policy.html) that can be can be adjust with the `@policy_document` variable.

## Usage

1. Add blueprint to Gemfile
2. Configure: configs/sqs values
3. Deploy

## Add

Add the blueprint to your lono project's `Gemfile`.

```ruby
gem "sqs", git: "git@github.com:boltopspro/sqs.git"
```

## Configure

First you want to configure the [configs](https://lono.cloud/docs/core/configs/) files. Use [lono seed](https://lono.cloud/reference/lono-seed/) to configure starter values quickly.

    LONO_ENV=development lono seed sqs

To deploy to additional environments:

    LONO_ENV=production  lono seed sqs

The generated files in `config/sqs` folder look something like this:

    configs/sqs/
    ├── params
    │   └── development.txt
    └── variables
        └── development.rb

Here's an example of some configs:

configs/sqs/params/development.txt:

    # Parameter Group: AWS::SQS::Queue
    # ContentBasedDeduplication=
    # DelaySeconds=
    # FifoQueue=
    # KmsDataKeyReusePeriodSeconds=
    # KmsMasterKeyId=
    # MaximumMessageSize=
    # MessageRetentionPeriod=
    # QueueName=
    # ReceiveMessageWaitTimeSeconds=
    # VisibilityTimeout=


## Deploy

Use the [lono cfn deploy](http://lono.cloud/reference/lono-cfn-deploy/) command to deploy. Example:

    lono cfn deploy sqs --blueprint sqs --sure

## Configure: Details

### Redrive Policy

You can configure the Redrive Policy with a `@redrive_policy` variable. Example:

configs/sqs/variables/development.rb

```ruby
@redrive_policy = <<~JSON
  {
    "maxReceiveCount": "5",
    "deadLetterTargetArn": "arn:aws:sqs:us-west-2:112233445566:MyDeadLetterQueue"
  }
JSON
```

In this case, the queue will allow the message to be retried 5 times before moving the message to a [Dead Letter Queue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)

### Policy Document

You can override and customize the Policy Document associated with queue with the `@policy_document` variable. Example:

configs/sqs/variables/development.rb

```ruby
@policy_document = {
  Version: "2012-10-17",
  Statement: [
    {
      Effect: "Allow",
      Principal: { AWS: "*" },
      Action: "SQS:*", # SQS:SendMessage
      Resource: get_att("Queue.Arn")
    }
  ]
}
```
