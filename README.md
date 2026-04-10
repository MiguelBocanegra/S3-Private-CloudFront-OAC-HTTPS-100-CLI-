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

## 1. Access CloudShell

Access AWS CloudShell to start the configuration.

---

## 2. Create IAM User

```bash
aws iam create-user --user-name miguel-cloudfront-user
