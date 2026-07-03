# Chapter 2 — Building Your First Amazon S3 Bucket

## Lab 01 — Designing Storage for a Growing Company

> **Difficulty:** Beginner
>
> **Estimated AWS Cost:** Free Tier Eligible (approximately $0.00–$0.01)
>
> **AWS Services Used:** Amazon S3
>
> **Lab Type:** Hands-on
>
> **Production Relevance:** ★★★★★

---

# Scenario

Congratulations!

You have just joined **CloudMart**, a fast-growing e-commerce company, as a Cloud Engineer.

The company currently stores product images, invoices, employee documents, application logs, and customer uploads on a local file server. As the business grows, the infrastructure team decides to migrate its storage platform to Amazon S3.

Your first assignment is simple—but critical.

You must create the company's first production-ready S3 bucket while following AWS best practices from day one.

Throughout this workshop, this bucket will evolve into an enterprise-grade storage platform as additional AWS services are introduced.

---

# Learning Objectives

By the end of this lab, you will be able to:

* Create an Amazon S3 bucket.
* Understand bucket naming conventions.
* Select the appropriate AWS Region.
* Configure Object Ownership.
* Configure Block Public Access.
* Understand bucket uniqueness.
* Upload your first object.
* Inspect object metadata.
* Delete objects safely.
* Understand how these settings affect production workloads.

---

# Architecture

```
                    User
                      │
                      ▼
              AWS Management Console
                      │
                      ▼
                Amazon S3 Bucket
                      │
          ┌───────────┴────────────┐
          │                        │
      Product Images          Documents
```

Although the architecture is simple today, this same bucket will later integrate with:

* CloudFront
* IAM
* AWS KMS
* EventBridge
* Lambda
* SNS
* CloudTrail
* AWS Config
* Amazon Macie
* Terraform
* GitHub Actions

By the end of this workshop, this diagram will evolve into a complete production architecture.

---

# Before You Begin

Ensure that you have:

* An active AWS account
* MFA enabled on your root user
* An IAM user with administrative access (recommended instead of using the root user)
* Access to the AWS Management Console

---

# Understanding the Bucket Before Creating It

Before clicking **Create Bucket**, let's understand what we're actually creating.

A bucket is a logical container that stores objects.

Think of it as a globally unique namespace.

Unlike folders on your laptop:

```
C:\Users\Ayushi\Documents
```

an S3 bucket exists as a globally unique resource.

This means no two AWS customers anywhere in the world can own buckets with the same name.

For example:

```
cloudmart-storage
```

may already belong to another AWS customer.

AWS will reject duplicate bucket names.

---

# Step 1 — Open Amazon S3

1. Sign in to the AWS Management Console.
2. Search for **S3**.
3. Open the Amazon S3 Dashboard.
4. Click **Create bucket**.

Take a moment to observe the interface. Notice that S3 is organized around buckets rather than disks, folders, or volumes.

---

# Step 2 — Choose a Bucket Name

Enter a globally unique name.

Example:

```
cloudmart-dev-storage-2026
```

A good production bucket name should:

* Be globally unique.
* Use lowercase letters only.
* Contain numbers or hyphens if needed.
* Avoid spaces and underscores.
* Reflect the workload or environment.

Good examples:

```
cloudmart-dev-assets
cloudmart-prod-images
cloudmart-backups
cloudmart-log-archive
```

Poor examples:

```
My Bucket
Bucket1
Test
Images
```

---

# Why Bucket Naming Matters

Bucket names appear in:

* Object URLs
* API requests
* CloudTrail logs
* IAM policies
* Terraform configurations
* CloudFront origins

Changing a bucket name later is not supported.

In production, bucket names are considered permanent architectural decisions.

---

# Step 3 — Select the AWS Region

Choose the AWS Region closest to your users or workloads.

Example:

* Asia Pacific (Singapore)
* Asia Pacific (Mumbai)
* US East (N. Virginia)

### Production Considerations

Choose a Region based on:

* User latency
* Regulatory compliance
* Data residency requirements
* Disaster recovery strategy
* Integration with existing infrastructure

Changing the Region after bucket creation is not possible.

---

# Step 4 — Configure Object Ownership

Select:

**ACLs Disabled (Bucket owner enforced)**

This is the AWS-recommended setting for nearly all new workloads.

### Why?

Historically, Amazon S3 used Access Control Lists (ACLs) to manage permissions.

Today, AWS recommends managing access using IAM policies and bucket policies because they are easier to audit, scale, and maintain.

Disabling ACLs reduces complexity and minimizes the risk of accidental misconfigurations.

---

# Step 5 — Block Public Access

Leave all **Block Public Access** settings enabled.

This prevents accidental exposure of sensitive data to the internet.

Even if someone later adds a public bucket policy, these settings help protect the bucket until you intentionally decide otherwise.

Public access should always be a deliberate decision—not the default.

---

# Step 6 — Bucket Versioning

Leave Versioning disabled for now.

We will enable and explore Versioning in a dedicated chapter where we will examine object history, delete markers, recovery, and rollback scenarios.

---

# Step 7 — Default Encryption

Enable default encryption.

Select:

**Server-side encryption with Amazon S3 managed keys (SSE-S3).**

This ensures that every object uploaded to the bucket is automatically encrypted at rest without requiring changes to your applications.

Later chapters will compare SSE-S3, SSE-KMS, and customer-managed encryption keys.

---

# Step 8 — Create the Bucket

Review the configuration.

Click **Create bucket**.

Congratulations!

You have just provisioned your first production-ready Amazon S3 bucket.

---

# Hands-on Exercise

Create the following logical structure by uploading files into these prefixes:

```
documents/
images/
videos/
logs/
backups/
```

Remember:

These are **not real folders**.

Amazon S3 creates these prefixes automatically based on the object key names.

---

# Upload Your First Objects

Upload one file into each prefix.

Suggested files:

```
documents/
    resume.pdf

images/
    laptop.jpg

videos/
    demo.mp4

logs/
    app.log

backups/
    backup.zip
```

Observe for each object:

* Object key
* Size
* Last modified
* Storage class
* ETag
* Owner
* Encryption status

These properties provide valuable information about how Amazon S3 stores and manages your objects.

---

# Production Insight

A common misconception is that Amazon S3 behaves like Windows Explorer or Linux directories.

It does not.

For example:

```
images/products/laptop.jpg
```

is stored as a single object with the key:

```
images/products/laptop.jpg
```

The console displays prefixes as folders purely for convenience.

Understanding this concept is essential when designing lifecycle rules, replication configurations, access policies, and application logic.

---

# Challenge

Without using the AWS Console:

* Upload another object using the AWS CLI.
* Create a new prefix named `archive/`.
* Delete one object.
* Restore it if possible.
* Record what happens.

You will revisit these observations in later chapters.

---

# Common Mistakes

* Choosing a bucket name that is already in use.
* Selecting the wrong AWS Region.
* Disabling Block Public Access without understanding the implications.
* Assuming prefixes are actual folders.
* Uploading objects without enabling default encryption.

---

# Cleanup

Do **not** delete the bucket.

This bucket will be reused throughout the remaining chapters as you progressively implement versioning, lifecycle policies, encryption, replication, event notifications, logging, monitoring, automation, and advanced security controls.

Instead, leave the bucket intact and proceed to the next chapter.

---

# Key Takeaways

You have now created a production-ready Amazon S3 bucket following AWS best practices. More importantly, you understand the reasoning behind each configuration decision rather than simply accepting default settings. This foundation will support every advanced feature explored throughout the remainder of this workshop.

