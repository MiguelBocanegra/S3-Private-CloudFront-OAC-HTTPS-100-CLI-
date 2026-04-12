![Status](https://img.shields.io/badge/Status-In%20Progress-yellow?style=flat-square)

![Difficulty](https://img.shields.io/badge/Difficulty-Beginner-green?style=for-the-badge&logo=speedtest&logoColor=white)

--- 

![AWS](https://img.shields.io/badge/AWS-Cloud-orange)
![S3](https://img.shields.io/badge/Amazon-S3-blue)
![CloudFront](https://img.shields.io/badge/Amazon-CloudFront-purple)
![IAM](https://img.shields.io/badge/AWS-IAM-yellow)
![CLI](https://img.shields.io/badge/AWS-CLI-black)
![Security](https://img.shields.io/badge/Security-Private%20Bucket-red)
![CDN](https://img.shields.io/badge/CDN-CloudFront-green)
![Project](https://img.shields.io/badge/Project-Cloud%20Deployment-brightgreen)

# AWS S3 + CloudFront + OAC (Secure Static Website)

This project demonstrates how to deploy a **secure static website** using:

- Amazon S3 (private bucket)
- CloudFront (CDN)
- Origin Access Control (OAC)
- AWS CLI (100% CLI-based setup)

---

## 📌 What I Learned

In this project, I focused not only on deploying a website, but on implementing real security best practices:

- Secure an S3 bucket by disabling all public access
- Use CloudFront with Origin Access Control (OAC)
- Connect services using least privilege access
- Deploy a static website using a CDN
- Work entirely with AWS CLI instead of the console

# 🚀 Project Overview

In this project, I built a secure architecture where:

- The S3 bucket is **completely private**
- Only CloudFront can access the content using **OAC**
- The website is delivered globally through a CDN
- All resources are created using the **AWS CLI**

---

## 🔐 Security Design

This architecture follows best practices:

- The S3 bucket is completely private (no public access)
- Only CloudFront can access the bucket using OAC
- Access is restricted using a bucket policy with SourceArn
- IAM permissions follow least privilege

This prevents direct access to S3 and protects the content.

### 🛠️ Tech Stack & Architecture

| Component | Service / Tool |
| :--- | :--- |
| **Cloud Provider** | AWS (Region: us-east-1) |
| **Storage** | Amazon S3 (Private Bucket) |
| **Content Delivery** | Amazon CloudFront (CDN) |
| **Security & Access** | Origin Access Control (OAC) & IAM |
| **Infrastructure** | AWS CLI (100% Terminal-based) |
| **Deployment Flow** | CLI S3 Sync & Distribution Config |


# 🧱 Architecture

User (Browser) → CloudFront (CDN) → S3 Bucket (Private)

---

## 🧱 Architecture Details

Flow:

1. User sends HTTPS request
2. CloudFront receives the request
3. CloudFront uses OAC to access S3
4. S3 returns the content securely
5. CloudFront delivers content to the user

# ⚙️ Step-by-Step Implementation

## 1.We access CloudShell

![enter](./imagenescloud/1.png)

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

## 6.1. The following output will show the Access Key and Secret Access Key

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

## 8. We connect the created user to the CLI with the following command

```bash
aws configure
```

## 8.1. We enter the Access Key, Secret Access Key, region, and output format

```bash
AWS Access Key ID [None]: +++++++++++++++++++++++++++++
AWS Secret Access Key [None]: ++++++++++++++++++++++++++++++++++
Default region name [None]: us-east-1
Default output format [None]: json
```

## 9.We confirm the configuration with the following command:

```bash
aws sts get-caller-identity
```

## 9.1.If everything is correct, we get the following output:

```json
{
"UserId": "******************",
"Account": "!!!!!!!!!!!!!!!!!!",
"Arn": "arn:aws:iam::**************/miguel-cloudfront-user"
}
```

## 10.We create a bucket with the following command:

```bash
aws s3api create-bucket
--bucket <bucket_name>
--region
```

## 10.1.We will get the following output

```json
{
"Location": "/s3-private-miguel-001",
"BucketArn": "arn:aws:s3:::s3-private-miguel-001"
}
```
## 11.We validate that the bucket has public access blocked with following command: 

```bash
aws s3api get-public-access-block
--bucket <bucket_name>
```

## 11.1. If all is well the system show the following output

```json
{
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": true,
        "IgnorePublicAcls": true,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    }
}
```
## 12.We upload a website by cloning a repository

```bash
git clone https://github.com/startbootstrap/startbootstrap-creative.git website
```

## 13.From the folder where is the website archive , we upload files to S3 with the following command:

```bash
aws s3 sync ./website s3://<bucket_name>/
```
## 14.we validate  that the archives uploaded to the bucket with the following command:

```bash
aws s3 ls s3://<bucket_name>/
```

## 15. We create OAC file with the following command:

```bash
nano oac.json
```

## 14.1.we add the following Content in the file 

```json
{
"Name": "miguel-oac",
"Description": "OAC for private S3 bucket",
"OriginAccessControlOriginType": "s3",
"SigningBehavior": "always",
"SigningProtocol": "sigv4"
}
```
## 15. we create OAC with the following command 

```bash
aws cloudfront create-origin-access-control
--origin-access-control-config file://oac.json
```

## 15.1 – the befor command generated the following output

```json
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
```

## 16.we validate  the  OAC connection with the following command

```bash
aws cloudfront list-origin-access-controls
```
## 16.1.In the output we store the id 

```json
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
```
In the following steps we generate the distribution between OAC and CloudFront, we will connect CloudFront with S3  with OAC
---

## 17.Create the archive distribution.json with the following command: 

```bash
nano distribution.json
```

## 17.1.We Add the following information in the new file, but before we should add the id that we stored in the step 16.1  in the key "OriginAccessControlId":   

```json
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
```
## 18.We create distribution with the following command:

``` bash
aws cloudfront create-distribution
--distribution-config file://distribution.json
```

## 19.We run the following command for get DomainName

``` bash
aws cloudfront list-distributions
```

## 20.We run the following command for get the ARN

``` bash
aws cloudfront list-distributions
```
## 21.We create a new file for add a policy with the following command: 

``` bash
nano bucket-policy.json
```

## 22.we add in file .json the policy and we replace the ARN and the domainame 

```json
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
```
23 – we apply policy with the following command 

```bash
aws s3api put-bucket-policy
--bucket <buckect_name>
--policy file://bucket-policy.json
```
24 – Final step: open in browser and load the coudfront domain 
https://d2f0yjlgg96yl7.cloudfront.net

![website](./imagenescloud/2.png)

## ✅ Final Result

- Fully private S3 bucket
- Secure access using CloudFront + OAC
- Static website delivered globally
- Infrastructure created using CLI

---

## 👨‍💻 Author

Miguel Bocanegra
