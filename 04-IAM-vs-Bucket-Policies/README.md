# Chapter 4 — Securing Amazon S3: IAM, Bucket Policies, Object Ownership, and Public Access

> **Difficulty:** Beginner → Intermediate
>
> **Estimated AWS Cost:** Free Tier Eligible (approximately $0.00–$0.02)
>
> **AWS Services Used:** Amazon S3, AWS IAM
>
> **Lab Type:** Hands-on
>
> **Production Relevance:** ★★★★★

---

# Scenario

CloudMart has successfully migrated its files to Amazon S3.

Initially, only the Cloud Engineering team managed the storage platform.

As the company grows, multiple teams now require access:

* HR needs access to employee documents.
* Marketing needs access to product images.
* Developers need access to application logs.
* Finance needs access to invoices.
* Customers should only access publicly available product images.

Giving everyone full access to the bucket is not acceptable.

As the Cloud Engineer, your responsibility is to secure the storage platform by implementing the principle of **least privilege** while ensuring each team can access only the data they require.

In this chapter, you will learn how Amazon S3 permissions work and implement production-ready access controls.

---

# Learning Objectives

By the end of this chapter, you will be able to:

* Understand how Amazon S3 authorization works.
* Explain the difference between IAM Policies and Bucket Policies.
* Understand Object Ownership.
* Understand Block Public Access.
* Create IAM users.
* Grant least-privilege permissions.
* Test permissions using different identities.
* Identify common permission-related issues.
* Troubleshoot Access Denied errors.

---

# Understanding S3 Security

When a request is made to Amazon S3, AWS evaluates several security mechanisms before allowing access.

These include:

* IAM Policies
* Bucket Policies
* Block Public Access
* Object Ownership
* Access Control Lists (ACLs) *(legacy)*
* Explicit Deny statements

Every request must pass AWS's authorization evaluation before access is granted.

---

# Understanding IAM

**AWS Identity and Access Management (IAM)** controls **who** can access AWS resources and **what actions** they can perform.

Think of IAM as controlling **identities**.

Examples include:

* Users
* Groups
* Roles

IAM answers questions such as:

* Who can upload objects?
* Who can delete objects?
* Who can create buckets?
* Who can list bucket contents?

---

# Understanding Bucket Policies

A Bucket Policy is attached directly to an Amazon S3 bucket.

Instead of asking:

> Who has permission?

Bucket Policies ask:

> Who is allowed to access this bucket?

Bucket Policies are commonly used for:

* Cross-account access
* Public website hosting
* Restricting access by IP address
* Restricting access to specific AWS accounts
* Enforcing HTTPS-only access

---

# IAM Policy vs Bucket Policy

| IAM Policy                                   | Bucket Policy                                            |
| -------------------------------------------- | -------------------------------------------------------- |
| Attached to a User, Group, or Role           | Attached directly to a Bucket                            |
| Controls identity permissions                | Controls bucket access                                   |
| Used across AWS services                     | Specific to Amazon S3                                    |
| Commonly used for employees and applications | Commonly used for public access and cross-account access |

In production, both are often used together.

---

# Understanding Object Ownership

When you created your bucket in Chapter 2, you selected:

**ACLs Disabled (Bucket owner enforced)**

This setting ensures that the bucket owner automatically owns every object uploaded into the bucket.

AWS recommends this configuration because it eliminates many permission issues caused by legacy Access Control Lists (ACLs).

---

# Understanding Block Public Access

Amazon S3 includes four Block Public Access settings designed to prevent accidental exposure of data.

These settings override public access configurations unless intentionally disabled.

For most production workloads, all four settings should remain enabled.

They provide an important safeguard against accidental data leaks.

---

# Hands-on Lab 1 — Review Current Security Settings

1. Open the Amazon S3 Console.
2. Select your CloudMart bucket.
3. Navigate to the **Permissions** tab.

Review:

* Object Ownership
* Block Public Access
* Bucket Policy
* Access Control List (ACL)

Notice that your bucket currently has:

* No Bucket Policy
* ACLs disabled
* Block Public Access enabled

This is the recommended starting point for most workloads.

---

# Hands-on Lab 2 — Create IAM Users

Open the IAM Console.

Create two IAM users:

| User           | Purpose               |
| -------------- | --------------------- |
| developer-user | Application Developer |
| intern-user    | Summer Intern         |

Grant each user console access.

Do **not** attach AdministratorAccess.

We will create custom permissions instead.

---

# Hands-on Lab 3 — Create an IAM Policy for the Developer

Create a customer-managed IAM policy that allows the developer to:

* List the bucket
* Upload objects
* Download objects
* Delete objects

Attach the policy only to:

```text
developer-user
```

Test by signing in as the developer.

Verify:

✅ Upload works

✅ Download works

✅ Delete works

---

# Hands-on Lab 4 — Create a Read-Only Policy for the Intern

Create another IAM policy.

Requirements:

The intern should only:

* List the bucket
* Download files

The intern must **not**:

* Upload objects
* Delete objects
* Modify metadata

Attach the policy to:

```text
intern-user
```

Sign in using the intern account.

Verify:

| Operation   | Expected Result |
| ----------- | --------------- |
| List Bucket | ✅ Allowed       |
| Download    | ✅ Allowed       |
| Upload      | ❌ Access Denied |
| Delete      | ❌ Access Denied |

Observe how AWS enforces permissions.

---

# Hands-on Lab 5 — Create Your First Bucket Policy

Your Marketing team wants customers to view product images directly from Amazon S3.

Instead of making the entire bucket public, only the following prefix should be publicly accessible:

```text
images/
```

Before implementing the policy:

* Review your Block Public Access settings.
* Understand why public access is currently blocked.

Then:

* Temporarily disable Block Public Access for this lab.
* Create a Bucket Policy that grants **read-only** access to objects stored under the `images/` prefix.
* Save the policy.

Test:

* Open the URL of an image stored in `images/`.
* Verify that the image is publicly accessible.
* Attempt to access a file stored under `documents/`.

Expected Result:

* Images are accessible.
* Documents remain private.

**Important:** At the end of the exercise, remove the public policy and re-enable Block Public Access.

---

# Hands-on Lab 6 — Observe Access Evaluation

While signed in as:

* Administrator
* developer-user
* intern-user

Attempt the following operations:

| Operation       | Admin | Developer | Intern |
| --------------- | ----- | --------- | ------ |
| List Bucket     | ?     | ?         | ?      |
| Upload Object   | ?     | ?         | ?      |
| Delete Object   | ?     | ?         | ?      |
| Download Object | ?     | ?         | ?      |

Complete the table using your observations.

Understanding permission differences is essential for troubleshooting production issues.

---

# Production Example

CloudMart stores three categories of data:

```text
images/
```

Public product images

```text
documents/
```

Internal HR records

```text
logs/
```

Application logs

Customers should never access:

* HR documents
* Application logs
* Financial reports

Only product images should be publicly available.

This design follows the principle of least privilege and minimizes the impact of accidental exposure.

---

# Common Mistakes

* Attaching AdministratorAccess to every user.
* Making the entire bucket public instead of a specific prefix.
* Disabling Block Public Access permanently.
* Confusing IAM Policies with Bucket Policies.
* Relying on ACLs for new workloads.
* Forgetting to test permissions using different users.

---

# Troubleshooting

### Problem

Access Denied while uploading objects.

Possible Causes:

* Missing IAM permissions.
* Explicit Deny in a Bucket Policy.
* Block Public Access restrictions.

---

### Problem

Public image cannot be accessed.

Possible Causes:

* Block Public Access is still enabled.
* Incorrect Bucket Policy.
* Incorrect object key.

---

### Problem

Developer cannot delete objects.

Possible Causes:

* Missing `DeleteObject` permission.
* Explicit Deny in another policy.

---

### Problem

Intern can upload files unexpectedly.

Possible Cause:

* Incorrect IAM policy attached.

Review the attached policies and test again.

---

# Interview Questions

1. What is IAM?
2. What is the difference between IAM Policies and Bucket Policies?
3. What is Object Ownership?
4. Why does AWS recommend disabling ACLs?
5. What is Block Public Access?
6. When should Bucket Policies be used?
7. What is the principle of least privilege?
8. What happens when an explicit Deny exists?
9. Why should AdministratorAccess rarely be assigned to users?
10. How would you securely expose only product images to the public?

---

# Challenge Lab

CloudMart is expanding.

Create three new IAM users:

* hr-user
* finance-user
* marketing-user

Design permission policies so that:

* HR can access only `documents/`
* Marketing can access only `images/`
* Finance can access only `invoices/`

No team should be able to access another department's data.

Test every policy by signing in as each user and attempting to access objects outside their assigned prefix.

---

# Cleanup

Before continuing to the next chapter:

* Delete all test IAM users.
* Delete all customer-managed IAM policies created for this lab.
* Remove any Bucket Policies created during testing.
* Re-enable all Block Public Access settings.
* Verify that your bucket is private again.

Do **not** delete the bucket or its objects.

---

# Key Takeaways

You have implemented the foundational security controls for Amazon S3.

You learned how IAM Policies and Bucket Policies complement each other, why Object Ownership simplifies permission management, and how Block Public Access protects against accidental data exposure. You also applied the principle of least privilege by creating users with different levels of access and verifying those permissions through practical testing.

In the next chapter, you will explore **Amazon S3 Versioning**, where you will learn how to protect data from accidental deletion, recover previous object versions, understand delete markers, and implement one of the most important data protection features used in production environments.

