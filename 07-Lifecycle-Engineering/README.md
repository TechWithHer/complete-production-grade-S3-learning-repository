# Chapter 7 — Amazon S3 Server-Side Encryption: Protecting Data at Rest

> **Difficulty:** Intermediate
>
> **Estimated AWS Cost:** Free Tier Eligible (approximately $0.00–$0.05 (AWS KMS charges may apply beyond the Free Tier))
>
> **AWS Services Used:** Amazon S3, AWS Key Management Service (AWS KMS)
>
> **Lab Type:** Hands-on
>
> **Production Relevance:** ★★★★★

---

# Scenario

CloudMart has successfully implemented:

* Secure access using IAM and Bucket Policies
* Versioning to protect against accidental deletion
* Lifecycle Rules to optimize storage costs

The storage platform is now secure from unauthorized users and optimized for long-term storage.

However, another security requirement remains.

CloudMart stores sensitive information, including:

* Employee records
* Financial reports
* Customer invoices
* Application configuration files
* Internal business documents

The company's security team introduces a new policy:

> **Every object stored in Amazon S3 must be encrypted at rest.**

As the Cloud Engineer, your responsibility is to ensure that all current and future objects are encrypted using AWS best practices.

In this chapter, you will learn how Amazon S3 encrypts data, explore different server-side encryption options, and implement encryption for your production bucket.

---

# Learning Objectives

By the end of this chapter, you will be able to:

* Explain why encryption at rest is important.
* Understand how Amazon S3 encrypts objects.
* Differentiate between SSE-S3, SSE-KMS, and SSE-C.
* Configure default bucket encryption.
* Create and use a customer-managed AWS KMS key.
* Upload encrypted objects.
* Verify encryption settings.
* Understand KMS permissions.
* Design encryption strategies for production environments.

---

# Understanding Encryption at Rest

Encryption converts readable information into an unreadable format using cryptographic algorithms.

Without the correct decryption key, encrypted data cannot be interpreted.

When data is stored in Amazon S3, encryption protects the contents of the object even if the underlying storage media is accessed.

Encryption at rest protects against risks such as:

* Lost or decommissioned storage devices
* Unauthorized physical access
* Compliance requirements
* Internal security policies

It is important to understand that encryption protects the **data**, while IAM and Bucket Policies protect **access** to the data.

Both are required for a secure storage solution.

---

# Encryption in Transit vs Encryption at Rest

| Encryption Type       | Protects Data                   |
| --------------------- | ------------------------------- |
| Encryption in Transit | While moving across the network |
| Encryption at Rest    | While stored on disk            |

When you upload an object using HTTPS:

* The connection is encrypted while the file travels to AWS.
* Amazon S3 can also encrypt the object before storing it.

These are separate layers of protection.

Production workloads almost always use both.

---

# Server-Side Encryption Options

Amazon S3 supports several server-side encryption methods.

## SSE-S3

Server-SSide Encryption using Amazon S3 managed keys.

Characteristics:

* Managed entirely by AWS.
* No key management required.
* Enabled with a few clicks.
* Recommended for most workloads.

---

## SSE-KMS

Server-Side Encryption using AWS Key Management Service (AWS KMS).

Characteristics:

* Keys managed through AWS KMS.
* Detailed audit logging.
* Fine-grained IAM permissions.
* Key rotation support.
* Compliance-friendly.

Large enterprises commonly use SSE-KMS.

---

## SSE-C

Server-Side Encryption with Customer-Provided Keys.

Characteristics:

* Customer provides the encryption key with every request.
* AWS never stores the key.
* Customer is fully responsible for key management.

This option is less common and is typically used only when organizations have strict external key management requirements.

---

# Architecture

```text id="m5xapw"
              Upload Object
                     │
                     ▼
              HTTPS Connection
                     │
                     ▼
             Amazon S3 receives object
                     │
             Encrypts the object
                     │
                     ▼
           Stores encrypted object
```

Applications continue reading and writing objects normally.

Encryption and decryption occur transparently.

---

# Reviewing Current Bucket Encryption

In Chapter 2, you enabled:

```text id="rwzrdn"
SSE-S3
```

as the bucket's default encryption.

Today, you will verify this configuration and explore additional encryption methods.

---

# Hands-on Lab 1 — Verify Default Encryption

Open your bucket.

Navigate to:

**Properties**

Locate:

**Default Encryption**

Observe:

* Encryption status
* Encryption type

Verify that:

```text id="1smtx4"
Server-side encryption with Amazon S3 managed keys (SSE-S3)
```

is enabled.

---

# Hands-on Lab 2 — Upload a New Object

Upload a new file.

Suggested name:

```text id="gnw3xw"
financial-report.pdf
```

After the upload completes:

Open the object.

Navigate to:

**Properties**

Locate:

**Server-side Encryption**

Observe:

The object is automatically encrypted.

Notice that no application changes were required.

This demonstrates the advantage of bucket-level default encryption.

---

# Hands-on Lab 3 — Verify Existing Objects

Review several previously uploaded objects.

Examples:

```text id="1kk2zq"
documents/
```

```text id="nvsp70"
images/
```

```text id="sqmrw6"
logs/
```

Compare:

* Storage Class
* Encryption
* Versioning

Observe whether every newly uploaded object is encrypted.

Document your observations.

---

# Understanding AWS KMS

AWS Key Management Service (AWS KMS) allows organizations to create, manage, rotate, and audit cryptographic keys.

Unlike SSE-S3,

AWS KMS gives administrators complete visibility into:

* Which key encrypted an object.
* Who used the key.
* When the key was used.
* Whether the key is enabled or disabled.

This level of auditing is essential for many compliance frameworks.

---

# Hands-on Lab 4 — Create a Customer Managed Key

Open the AWS KMS Console.

Create a new symmetric encryption key.

Suggested Alias:

```text id="xtt7bq"
alias/cloudmart-s3-key
```

During creation:

Review:

* Key Administrators
* Key Users
* Key Rotation

Leave default settings unless your organization has specific requirements.

Complete the key creation.

---

# Hands-on Lab 5 — Change Bucket Encryption

Return to Amazon S3.

Open:

Bucket Properties

Edit:

Default Encryption

Select:

```text id="8lkdms"
Server-side encryption with AWS Key Management Service (SSE-KMS)
```

Choose the customer-managed key created earlier.

Save the configuration.

From this point onward,

newly uploaded objects will use the selected KMS key.

Existing objects are **not** automatically re-encrypted.

---

# Observe

Upload another document.

Suggested file:

```text id="kgjlwm"
employee-salary.xlsx
```

Open its Properties page.

Observe:

* Encryption Type
* KMS Key
* Encryption Details

Compare this object with one uploaded before changing the encryption settings.

---

# Hands-on Lab 6 — Compare SSE-S3 and SSE-KMS

Create the following comparison table.

| Feature            | SSE-S3 | SSE-KMS |
| ------------------ | ------ | ------- |
| Managed By         | ?      | ?       |
| Audit Logging      | ?      | ?       |
| Key Rotation       | ?      | ?       |
| IAM Control        | ?      | ?       |
| Compliance Support | ?      | ?       |

Complete the table using your observations and AWS documentation.

Understanding the differences is more valuable than memorizing definitions.

---

# Hands-on Lab 7 — Observe KMS Permissions

Open the IAM Console.

Review the permissions attached to your administrative user.

Notice that using a KMS key requires additional permissions beyond standard Amazon S3 access.

Think about:

Would an IAM user with permission to upload objects automatically have permission to use every KMS key?

The answer depends on both:

* IAM Policies
* KMS Key Policies

You will revisit this concept later when implementing advanced security controls.

---

# Production Example

CloudMart stores payroll reports.

The HR department uploads salary information into:

```text id="jsr4nt"
documents/payroll/
```

Company policy requires:

* Encryption using a customer-managed KMS key.
* Audit logging for every decryption request.
* Automatic key rotation.
* Restricted access to only HR administrators.

SSE-KMS satisfies all of these requirements while integrating seamlessly with Amazon S3.

---

# Best Practices

For production workloads:

* Enable bucket default encryption.
* Prefer SSE-KMS for sensitive business data.
* Enable automatic KMS key rotation.
* Apply least-privilege access to KMS keys.
* Monitor KMS usage with AWS CloudTrail.
* Never rely on application developers to manually choose encryption during uploads.

Automate encryption wherever possible.

---

# Common Mistakes

* Assuming HTTPS alone encrypts stored data.
* Forgetting that existing objects remain encrypted with their original method.
* Giving every user unrestricted access to KMS keys.
* Confusing encryption with access control.
* Using multiple unnecessary customer-managed keys.

---

# Troubleshooting

### Problem

Upload fails after enabling SSE-KMS.

Possible Cause:

The IAM user lacks permission to use the KMS key.

---

### Problem

Object still shows SSE-S3.

Possible Cause:

The object was uploaded before changing the bucket's default encryption.

---

### Problem

Cannot select the KMS key.

Possible Cause:

The key is disabled or located in another AWS Region.

---

### Problem

Access Denied when decrypting an object.

Possible Cause:

KMS Key Policy or IAM permissions do not allow the operation.

---

# Interview Questions

1. What is encryption at rest?

2. What is the difference between encryption in transit and encryption at rest?

3. What is SSE-S3?

4. What is SSE-KMS?

5. What is SSE-C?

6. Why do enterprises commonly prefer SSE-KMS?

7. Does changing bucket encryption re-encrypt existing objects?

8. Why are KMS permissions separate from S3 permissions?

9. What is the purpose of key rotation?

10. How would you encrypt confidential HR records stored in Amazon S3?

---

# Challenge Lab

CloudMart is introducing a new security policy.

Design an encryption strategy for the following prefixes:

| Prefix         | Requirement              |
| -------------- | ------------------------ |
| documents/     | Customer-managed KMS key |
| images/        | SSE-S3                   |
| logs/          | SSE-KMS                  |
| backups/       | Customer-managed KMS key |
| public-assets/ | SSE-S3                   |

For each prefix, explain:

* Which encryption method you selected.
* Why it is appropriate.
* Whether audit logging is required.
* Whether access to the encryption key should be restricted.

Do not implement separate bucket-level encryption for each prefix.

Focus on designing an enterprise encryption strategy.

---

# Cleanup

Leave the bucket's default encryption enabled.

Keep the customer-managed KMS key.

Do not delete:

* Bucket
* Objects
* Previous object versions
* Lifecycle Rules

These resources will be used throughout the remaining chapters.

---

# Key Takeaways

You have implemented encryption at rest for Amazon S3 and explored the differences between AWS-managed and customer-managed encryption. You learned how default bucket encryption simplifies operations, how AWS KMS provides enhanced auditing and control, and why encryption complements—rather than replaces—identity and access management.

With secure access, Versioning, Lifecycle Rules, and encryption now in place, CloudMart's storage platform has reached the baseline security expected of a modern production environment.

In the next chapter, you will expand beyond a single AWS Region by implementing **Amazon S3 Replication**, enabling automatic copying of objects for disaster recovery, business continuity, and compliance with data residency requirements.
