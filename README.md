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

## 2.The user is created with the following command:

```bash
aws iam create-user --user-name miguel-cloudfront-user
```

## 2.1 The user is created with the following command:

```json
{
    "User": {
        "Path": "/",
        "UserName": "miguel-cloudfront-user",
        "UserId": "AIDATYM7WFJRDJS3CNRDF",
        "Arn": "arn:aws:iam::258569808482:user/miguel-cloudfront-user",
        "CreateDate": "2026-04-07T06:58:02+00:00"
    }
}

We create a JSON file where we will store a policy that grants access to S3 and CloudFront.
---


