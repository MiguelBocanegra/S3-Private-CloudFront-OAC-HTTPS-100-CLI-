![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=flat-square)

# AWS S3 + CloudFront + OAC (Secure Static Website)

This project demonstrates how to deploy a **secure static website** using:

- Amazon S3 (private bucket)
- CloudFront (CDN)
- Origin Access Control (OAC)
- AWS CLI (100% CLI-based setup)

---

# 🚀 Project Overview

In this project, I built a secure architecture where:

- The S3 bucket is **completely private**
- Only CloudFront can access the content using **OAC**
- The website is delivered globally through a CDN
- All resources are created using the **AWS CLI**

---

# 🧱 Architecture

User (Browser) → CloudFront (CDN) → S3 Bucket (Private)

---

# ⚙️ Step-by-Step Implementation

## 1.We access CloudShell

## 2.We created the user with the following command:

```bash
aws iam create-user --user-name <your_username>
```

## 2.1.The user creation is confirmed with the following output

```json
{
    "User": {
        "Path": "/",
        "UserName": "<your_username>",
        "UserId": "xxxxxxxxxxxxx",
        "Arn": "arn:aws:iam::xxxxxxxxxx:user/miguel-cloudfront-user",
        "CreateDate": "2026-04-07T06:58:02+00:00"
    }
}
```

We create a JSON file where we will store a policy that grants access to S3 and CloudFront.
---
## 3.we run command to create the file where the policy will be added

```bash
nano policy.json
```
## 3.1.We add the following information to the file policy.json

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "S3Access",
      "Effect": "Allow",
      "Action": [
"s3:CreateBucket",
"s3:PutBucketPolicy",
"s3:PutBucketPublicAccessBlock",
"s3:GetBucketPublicAccessBlock",
"s3:PutObject",
"s3:GetObject",
"s3:ListBucket"
      ],
      "Resource": "*"
    },
    {
      "Sid": "CloudFrontAccess",
      "Effect": "Allow",
      "Action": [
        "cloudfront:CreateDistribution",
        "cloudfront:GetDistribution",
        "cloudfront:ListDistributions",
        "cloudfront:CreateOriginAccessControl"
      ],
      "Resource": "*"
    }
  ]
}
```
## 4. We create the policy with the following command.

```bash
aws iam create-policy
--policy-name <policy_name>
--policy-document file://policy.json
```

## 4.1.We will get the following output:

```json
{
    "Policy": {
        "PolicyName": "<your_username>",
        "PolicyId": "xxxxxxxxxxxx",
        "Arn": "arn:aws:iam::xxxxxxxxx:policy/miguel-cloudfront-policy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "CreateDate": "2026-04-07T07:09:31+00:00",
        "UpdateDate": "2026-04-07T07:09:31+00:00"
    }
}
```
## 5.Now we attach the policy to the user using the ARN from the previous step:

```bash
aws iam attach-user-policy
--user-name miguel-cloudfront-user
--policy-arn <Enter_your_arn>
```
## 6.We create an Access Key to connect from an external CLI:

```bash
aws iam create-access-key
--user-name <your_username>
```

## 6.1. The following command will show the Access Key and Secret Access Key

```json
{
    "AccessKey": {
        "UserName": "miguel-cloudfront-user",
        "AccessKeyId": "AKIATYM7WFJRIWP6TNW3",
        "Status": "Active",
        "SecretAccessKey": "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",
        "CreateDate": "2026-04-07T07:16:50+00:00"
    }
}
```
## 7. We install the CLI with the following commands:

*Update system

```bash
sudo apt update -y
```
*Install dependencies

```bash
sudo apt install -y unzip curl
```
*Download AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

*Unzip

```bash
unzip awscliv2.zip
```

*Install

```bash
sudo ./aws/install
```
*Verify

```bash
aws --version
```

8 – We connect the created user to the CLI with the following command
aws configure

8.1 – We enter the Access Key, Secret Access Key, region, and output format
AWS Access Key ID [None]: AKIATYM7WFJRIWP6TNW3
AWS Secret Access Key [None]: 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
Default region name [None]: us-east-1
Default output format [None]: json

9 – We confirm the configuration with the following command:
aws sts get-caller-identity

9.1 – If everything is correct, we get the following output:
{
"UserId": "AIDATYM7WFJRDJS3CNRDF",
"Account": "258569808482",
"Arn": "arn:aws:iam::258569808482/miguel-cloudfront-user"
}

9 – We create a bucket with the following command:
aws s3api create-bucket
--bucket <bucket_name>
--region

9.1 – We will get the following output
{
"Location": "/s3-private-miguel-001",
"BucketArn": "arn:aws:s3:::s3-private-miguel-001"
}

10 – We validate that the bucket has public access blocked
aws s3api get-public-access-block
--bucket <bucket_name>

10.1 – Output
{
"PublicAccessBlockConfiguration": {
"BlockPublicAcls": true,
"IgnorePublicAcls": true,
"BlockPublicPolicy": true,
"RestrictPublicBuckets": true
}
}

11 – We upload a website by cloning a repository
git clone https://github.com/startbootstrap/startbootstrap-creative.git website

12 – From the folder, we upload files to S3
aws s3 sync ./website s3://<bucket_name>/

13 – Validate files
aws s3 ls s3://<bucket_name>/

14 – We create OAC file
nano oac.json

14.1 – Content
{
"Name": "miguel-oac",
"Description": "OAC for private S3 bucket",
"OriginAccessControlOriginType": "s3",
"SigningBehavior": "always",
"SigningProtocol": "sigv4"
}

15 – Create OAC
aws cloudfront create-origin-access-control
--origin-access-control-config file://oac.json

15.1 – Output
{
"Location": "https://cloudfront.amazonaws.com/2020-05-31/origin-access-control/E88HMBR8Y95V3",
"ETag": "ETVPDKIKX0DER",
"OriginAccessControl": {
"Id": "E88HMBR8Y95V3",
"OriginAccessControlConfig": {
"Name": "miguel-oac",
"Description": "OAC for private S3 bucket",
"SigningProtocol": "sigv4",
"SigningBehavior": "always",
"OriginAccessControlOriginType": "s3"
}
}
}

15 – Validate OAC
aws cloudfront list-origin-access-controls

15.1 – Output
{
"OriginAccessControlList": {
"Items": [
{
"Id": "E88HMBR8Y95V3",
"Description": "OAC for private S3 bucket",
"Name": "miguel-oac",
"SigningProtocol": "sigv4",
"SigningBehavior": "always",
"OriginAccessControlOriginType": "s3"
}
]
}
}

16 – Create distribution.json
nano distribution.json

17 – Add configuration
{
"CallerReference": "miguel-cloudfront-001",
"Comment": "CloudFront with OAC",
"Enabled": true,
"Origins": {
"Quantity": 1,
"Items": [
{
"Id": "S3Origin",
"DomainName": "s3-private-miguel-001.s3.amazonaws.com",
"S3OriginConfig": {
"OriginAccessIdentity": ""
},
"OriginAccessControlId": "E88HMBR8Y95V3"
}
]
},
"DefaultRootObject": "index.html",
"DefaultCacheBehavior": {
"TargetOriginId": "S3Origin",
"ViewerProtocolPolicy": "redirect-to-https",

"AllowedMethods": {
"Quantity": 2,
"Items": ["GET", "HEAD"]
},

"ForwardedValues": {
"QueryString": false,
"Cookies": {
"Forward": "none"
}
},

"TrustedSigners": {
"Enabled": false,
"Quantity": 0
},

"MinTTL": 0,
"DefaultTTL": 86400,
"MaxTTL": 31536000
}
}

18 – Create distribution
aws cloudfront create-distribution
--distribution-config file://distribution.json

19 – Get DomainName
aws cloudfront list-distributions
DomainName: d2f0yjlgg96yl7.cloudfront.net

20 – Get ARN
aws cloudfront list-distributions
ARN: arn:aws:cloudfront::258569808482/E1GQA3AOZLLXBN

21 – Create bucket policy file
nano bucket-policy.json

22 – Add policy
{
"Version": "2012-10-17",
"Statement": [
{
"Sid": "AllowCloudFrontAccessOnly",
"Effect": "Allow",
"Principal": {
"Service": "cloudfront.amazonaws.com"
},
"Action": "s3",
"Resource": "arn:aws:s3:::s3-private-miguel-001/*",
"Condition": {
"StringEquals": {
"AWS": "REEMPLAZA_CON_TU_CLOUDFRONT_ARN"
}
}
}
]
}

23 – Apply policy
aws s3api put-bucket-policy
--bucket s3-private-miguel-001
--policy file://bucket-policy.json

24 – Final step: open in browser
https://d2f0yjlgg96yl7.cloudfront.net
