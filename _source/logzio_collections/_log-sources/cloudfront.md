---
title: Ship CloudFront logs
logo:
  logofile: aws-cloudfront.svg
  orientation: vertical
data-source: CloudFront
logzio-app-url: https://app.logz.io/#/dashboard/data-sources/CloudFront
contributors:
  - idohalevi
  - imnotashrimp
shipping-tags:
  - aws
---

## Setup

When you set Logz.io to fetch CloudFront logs, Logz.io will periodically read logs from the configured S3 bucket.
CloudFront logs are useful for auditing/security monitoring and business intelligence.

**You'll need**:
`s3:ListBucket` and `s3:GetObject` [permissions](https://support.logz.io/hc/en-us/articles/209486129-Troubleshooting-AWS-IAM-Configuration-for-retrieving-logs-from-a-S3-Bucket) for the required S3 bucket

###### Configuration

1.  Send your logs to an S3 bucket

    Logz.io fetches your CloudFront logs from an S3 bucket.
    CloudFront access logs are not enabled by default, so you'll need to set this up.

    For help with this, see [Configuring and Using CloudFront Access Logs](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html) from AWS.

2.  Add the S3 bucket information

    {% include log-shipping/in-app-configuration.html toolId="s3-config" %}

    * **S3 bucket**: Name of the bucket
    * **Prefix**: The directory where the logs are stored
    * **S3 access key** and **S3 secret key**: Your S3 bucket credentials
    * **Region**: AWS region of the bucket

    <!-- logzio-inject:s3-config -->

      Logz.io fetches logs that are generated after configuring an S3 bucket.
      Past logs are not sent to Logz.io.
      {:.info-box.important}

3.  Check Logz.io for your logs

    Give your logs a few minutes to get from your system to ours, and then open [Kibana](https://app.logz.io/#/dashboard/kibana).

    If you still don't see your logs, see [log shipping troubleshooting]({{site.baseurl}}/user-guide/log-shipping/log-shipping-troubleshooting.html).
{:.tasklist.firstline-headline}