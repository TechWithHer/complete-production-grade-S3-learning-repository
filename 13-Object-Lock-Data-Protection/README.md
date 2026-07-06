# Chapter 13 — Amazon S3 Object Lock & Data Protection

> **Difficulty:** Intermediate
> **AWS Services Used:** Amazon S3, AWS IAM
> **Lab Type:** Hands-on
> **Production Relevance:** ★★★★☆

---

# Scenario

CloudMart stores critical business documents in Amazon S3.

These include:

* Financial Statements
* Tax Records
* Employee Payroll
* Legal Contracts
* Compliance Documents

One day, an administrator accidentally deletes an important legal file.

Fortunately, Versioning is enabled, and the object is recovered.

A month later, the Security Team raises a more serious concern:

> "What if a malicious user intentionally deletes every version of an object?"

Management now requires a solution where important files **cannot be deleted or modified**, even by administrators, until a specified retention period has expired.

Amazon S3 provides this capability through **Object Lock**.

---

# Learning Objectives

By the end of this chapter, you will be able to:

* Understand Amazon S3 Object Lock.
* Differentiate Versioning and Object Lock.
* Understand Governance Mode and Compliance Mode.
* Configure Legal Holds.
* Understand retention periods.
* Protect critical business data.
* Troubleshoot common Object Lock issues.

---

# What is Object Lock?

Object Lock prevents an object from being deleted or overwritten for a specified period.

Think of it as placing a document inside a secure vault.

Even users with administrative permissions cannot simply remove or modify it until the retention policy allows it.

This feature is commonly used to satisfy regulatory and legal requirements.

---

# Versioning vs Object Lock

Many engineers confuse these two features.

| Versioning                           | Object Lock                                          |
| ------------------------------------ | ---------------------------------------------------- |
| Creates multiple versions            | Prevents deletion or modification                    |
| Helps recover deleted objects        | Prevents deletion from happening                     |
| Protects against accidental deletion | Protects against accidental and intentional deletion |
| Optional                             | Must be enabled when the bucket is created           |

Versioning helps you recover data.

Object Lock helps prevent data loss in the first place.

---

# Important Requirement

Object Lock **cannot be enabled on an existing bucket**.

It must be selected **during bucket creation**.

If forgotten, you must create a new bucket with Object Lock enabled and migrate your data.

This is one of the most common interview questions.

---

# Hands-on Lab 1 — Create an Object Lock Bucket

Create a new bucket.

Suggested Name:

```text id="g71k3f"
cloudmart-legal-archive
```

During bucket creation:

Enable:

```text id="z7x9ku"
Object Lock
```

Versioning will be enabled automatically.

Complete the bucket creation.

---

# Retention Period

A retention period specifies how long an object must remain protected.

Example:

```text id="tk9h1m"
Retention Period

7 Years
```

Until this period expires:

* The object cannot be deleted.
* The object cannot be overwritten.
* The object remains protected.

This is commonly used for:

* Financial audits
* Healthcare records
* Government data
* Legal evidence

---

# Governance Mode

Governance Mode protects objects while allowing authorized administrators to bypass protection if necessary.

Typical use cases:

* Internal business records
* Corporate documents
* Financial reports

Characteristics:

* Prevents accidental deletion.
* Authorized users can remove protection with special permissions.
* Suitable for most enterprise environments.

---

# Hands-on Lab 2 — Apply Governance Mode

Upload:

```text id="q5fw89"
financial-report.pdf
```

Choose:

* Governance Mode
* Retention Period: 30 Days

Upload the object.

Attempt to delete it.

Observe that Amazon S3 prevents the operation unless appropriate bypass permissions are granted.

---

# Compliance Mode

Compliance Mode provides the highest level of protection.

Once applied:

* No user can shorten the retention period.
* No administrator can delete the object.
* No root user can bypass the protection.

This mode is intended for strict regulatory requirements.

Typical industries:

* Banking
* Healthcare
* Government
* Insurance

---

# Governance vs Compliance

| Feature                        | Governance            | Compliance |
| ------------------------------ | --------------------- | ---------- |
| Prevents accidental deletion   | Yes                   | Yes        |
| Administrator can bypass       | Yes (with permission) | No         |
| Suitable for legal regulations | Sometimes             | Yes        |
| Highest level of protection    | No                    | Yes        |

---

# Hands-on Lab 3 — Explore Compliance Mode

Upload another object.

Suggested Name:

```text id="2y8d0n"
tax-record.pdf
```

Configure:

* Compliance Mode
* Retention: 30 Days

Review the object properties.

Notice the configured retention settings.

**Note:** Do not test this in a production account unless you fully understand the implications, as the protection cannot be bypassed until the retention period expires.

---

# Legal Hold

Sometimes you don't know how long a document must be retained.

For example:

A company becomes involved in a legal investigation.

Lawyers may instruct:

> "Do not delete this document until further notice."

Instead of specifying a retention period,

apply a **Legal Hold**.

The object remains protected until the Legal Hold is manually removed.

---

# Hands-on Lab 4 — Apply a Legal Hold

Select an uploaded object.

Navigate to:

**Properties**

Locate:

**Legal Hold**

Enable it.

Attempt to delete the object.

Observe that deletion is prevented while the Legal Hold remains active.

---

# Production Use Cases

| Industry   | Example             |
| ---------- | ------------------- |
| Banking    | Transaction records |
| Healthcare | Patient records     |
| Government | Public records      |
| Legal      | Court evidence      |
| Insurance  | Claim documents     |
| Finance    | Audit reports       |

Object Lock is primarily used where regulations require data to remain unchanged for a defined period.

---

# Object Lock Architecture

```text id="0v6x1m"
Users
   │
   ▼
Amazon S3 Bucket
(Object Lock Enabled)
   │
   ▼
Protected Objects
```

Every protected object follows the configured retention or Legal Hold policy.

---

# Best Practices

* Enable Object Lock only for buckets that require immutable storage.
* Use Governance Mode for most enterprise workloads.
* Reserve Compliance Mode for regulatory requirements.
* Test retention settings in non-production environments first.
* Combine Object Lock with Versioning and Encryption.
* Restrict who can modify retention settings using IAM.

---

# Common Mistakes

* Forgetting that Object Lock must be enabled when creating the bucket.
* Confusing Versioning with Object Lock.
* Applying Compliance Mode without understanding its impact.
* Assuming administrators can always delete protected objects.
* Using Object Lock for temporary application data.

---

# Troubleshooting

### Problem

I cannot enable Object Lock.

Possible Cause:

The bucket was created without Object Lock enabled.

---

### Problem

My object cannot be deleted.

Possible Cause:

The object is protected by a retention period or Legal Hold.

---

### Problem

I cannot shorten the retention period.

Possible Cause:

The object is in Compliance Mode.

---

### Problem

Versioning is enabled automatically.

Explanation:

Object Lock requires Versioning to track protected object versions.

---

# Interview Questions

1. What is Amazon S3 Object Lock?
2. What is the difference between Versioning and Object Lock?
3. Can Object Lock be enabled on an existing bucket?
4. What is Governance Mode?
5. What is Compliance Mode?
6. What is a Legal Hold?
7. Why does Object Lock require Versioning?
8. Which industries commonly use Object Lock?
9. When would you choose Governance Mode over Compliance Mode?
10. Can the AWS account root user delete an object protected by Compliance Mode?

---

# Challenge Lab

CloudMart's Legal Department has the following requirements:

| Requirement                                 | Solution                             |
| ------------------------------------------- | ------------------------------------ |
| Store contracts for seven years             | Object Lock with retention period    |
| Prevent accidental deletion                 | Governance Mode                      |
| Protect documents under legal investigation | Legal Hold                           |
| Ensure regulatory compliance                | Compliance Mode for selected records |

Tasks:

1. Create a bucket with Object Lock enabled.
2. Upload sample legal documents.
3. Configure Governance Mode for one object.
4. Configure Compliance Mode for another object.
5. Apply a Legal Hold to a third object.
6. Attempt deletion and observe the behavior.
7. Document the differences between the three protection methods.

---

# Cleanup

Keep the following resources:

* Object Lock bucket
* Uploaded objects
* Retention settings
* Legal Holds

These resources can be used later to understand long-term data protection and compliance strategies.

---

# Chapter Summary

Amazon S3 Object Lock provides immutable storage by preventing objects from being deleted or modified until specific conditions are met. It builds upon Versioning by protecting object versions against both accidental and intentional deletion.

In this chapter, you explored Governance Mode, Compliance Mode, Legal Holds, and retention periods, along with the business scenarios that require each feature. These capabilities are widely used in regulated industries where preserving data integrity is a legal or compliance requirement.

---

# Key Takeaways

* Object Lock protects objects from deletion or modification.
* Versioning and Object Lock serve different purposes and work together.
* Object Lock must be enabled **when the bucket is created**.
* Governance Mode is suitable for most enterprise environments because authorized users can bypass protection when necessary.
* Compliance Mode offers the strongest protection and cannot be bypassed until the retention period expires.
* Legal Holds protect objects indefinitely until they are explicitly removed.
* Object Lock is an essential feature for organizations that require immutable storage for compliance, auditing, and long-term data retention.
