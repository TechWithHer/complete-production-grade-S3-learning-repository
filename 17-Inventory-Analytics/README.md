# Chapter 17 — Amazon S3 Inventory & Storage Analytics

> **Difficulty:** Intermediate
> **AWS Services Used:** Amazon S3, Amazon S3 Inventory, Amazon S3 Storage Lens
> **Lab Type:** Hands-on
> **Production Relevance:** ★★★★☆

---

# Scenario

CloudMart has been using Amazon S3 for over three years.

The company now stores:

* 15 million objects
* 120 TB of data
* Multiple storage classes
* Several production buckets

During a quarterly review, management asks:

* How many objects do we have?
* Which objects are not encrypted?
* Which objects are using Glacier?
* Which buckets are growing the fastest?
* Which buckets contain old object versions?

Checking millions of objects manually is impossible.

Amazon S3 provides **Inventory** and **Storage Lens** to answer these questions.

---

# Learning Objectives

By the end of this chapter, you will be able to:

* Understand Amazon S3 Inventory.
* Generate Inventory reports.
* Understand Amazon S3 Storage Lens.
* Analyze storage usage.
* Identify optimization opportunities.
* Use analytics to support operational decisions.

---

# Why Do We Need Inventory?

Imagine a bucket containing:

```text
15 Million Objects
```

Opening the bucket in the console only displays a small portion of the objects.

Questions like these become difficult:

* Which objects are encrypted?
* Which objects are versioned?
* Which objects use Glacier?
* Which objects are older than one year?

Amazon S3 Inventory solves this problem by generating a report of your objects.

---

# What is Amazon S3 Inventory?

Amazon S3 Inventory generates a report containing metadata about the objects stored in a bucket.

The report is automatically delivered to another Amazon S3 bucket on a scheduled basis.

Supported formats include:

* CSV
* ORC
* Parquet

---

# Information Included in an Inventory Report

Depending on your configuration, an Inventory report can include:

* Bucket Name
* Object Key
* Object Size
* Last Modified Date
* Storage Class
* Encryption Status
* Version ID
* Delete Marker
* Replication Status

This information is useful for auditing, reporting, and automation.

---

# Hands-on Lab 1 — Enable S3 Inventory

Create a bucket to store reports.

Suggested Name:

```text
cloudmart-inventory-reports
```

Open your primary bucket.

Navigate to:

**Management → Inventory Configurations**

Click:

**Create Inventory Configuration**

Configuration:

* Report Name: `weekly-inventory`
* Destination Bucket: `cloudmart-inventory-reports`
* Output Format: CSV
* Frequency: Weekly

Save the configuration.

---

# Viewing Inventory Reports

Inventory reports are generated automatically based on the selected schedule.

Once available:

Open the destination bucket.

Review the generated CSV report.

Notice that each row represents one object.

Example:

```text
Bucket,Object,StorageClass,Encrypted

cloudmart-data,logo.png,STANDARD,Yes
cloudmart-data,payroll.xlsx,STANDARD,Yes
cloudmart-data,archive.zip,GLACIER,Yes
```

---

# Production Use Cases for Inventory

Organizations use Inventory reports to:

* Audit encryption compliance.
* Identify objects stored in Glacier.
* Find objects missing tags.
* Prepare Batch Operations manifests.
* Review storage growth over time.

---

# What is Amazon S3 Storage Lens?

While Inventory focuses on individual objects,

**Storage Lens** provides high-level analytics across buckets.

It helps answer questions such as:

* Which bucket is growing fastest?
* Which bucket contains the most objects?
* How much data is stored?
* How many non-current versions exist?

Think of Storage Lens as a dashboard for your S3 environment.

---

# Hands-on Lab 2 — Explore Storage Lens

Navigate to:

**Amazon S3 → Storage Lens**

Open the default dashboard.

Review available metrics such as:

* Total Storage
* Object Count
* Bucket Count
* Current Versions
* Non-current Versions
* Delete Markers

Observe how the dashboard summarizes your storage usage.

---

# Inventory vs Storage Lens

| Amazon S3 Inventory                  | Amazon S3 Storage Lens               |
| ------------------------------------ | ------------------------------------ |
| Object-level report                  | Bucket and account-level analytics   |
| Generates CSV, ORC, or Parquet files | Interactive dashboard                |
| Used for audits and automation       | Used for monitoring and optimization |
| Scheduled report                     | Periodically updated metrics         |

Both services complement each other and are commonly used together.

---

# Storage Optimization Example

Suppose Storage Lens shows:

| Bucket  | Growth          |
| ------- | --------------- |
| Images  | Stable          |
| Logs    | Growing rapidly |
| Backups | Very large      |

You might decide to:

* Apply Lifecycle Rules to logs.
* Move backups to Glacier.
* Review unnecessary object versions.

Analytics help drive these operational decisions.

---

# Hands-on Lab 3 — Review Your Buckets

Open each bucket created throughout this course.

Review:

* Estimated object count.
* Storage used.
* Versioning status.
* Lifecycle configuration.
* Storage class.

Think about how Inventory and Storage Lens would help if these buckets contained millions of objects.

---

# Best Practices

* Enable Inventory for production buckets.
* Store Inventory reports in a dedicated bucket.
* Review Storage Lens dashboards regularly.
* Use Inventory reports before running Batch Operations.
* Combine Inventory with Lifecycle Rules for storage optimization.

---

# Common Mistakes

* Assuming Inventory updates in real time.
* Confusing Inventory with Storage Lens.
* Ignoring analytics until storage costs become high.
* Storing Inventory reports in the same bucket being analyzed.
* Forgetting to review generated reports.

---

# Troubleshooting

### Problem

Inventory report has not appeared.

Possible Causes:

* The scheduled report has not run yet.
* Destination bucket is incorrect.
* Required permissions are missing.

---

### Problem

Storage Lens metrics seem outdated.

Possible Cause:

Storage Lens updates periodically and does not provide real-time metrics.

---

### Problem

Inventory report is incomplete.

Possible Cause:

Some metadata fields were not selected during configuration.

---

# Interview Questions

1. What is Amazon S3 Inventory?
2. What information can an Inventory report contain?
3. How often are Inventory reports generated?
4. What is Amazon S3 Storage Lens?
5. What is the difference between Inventory and Storage Lens?
6. Why would you use Inventory before running Batch Operations?
7. What formats are supported for Inventory reports?
8. Does Storage Lens provide object-level information?
9. How can Storage Lens help reduce storage costs?
10. When would you choose Inventory over Storage Lens?

---

# Challenge Lab

CloudMart wants better visibility into its storage.

Requirements:

| Requirement                  | Solution           |
| ---------------------------- | ------------------ |
| Weekly object reports        | S3 Inventory       |
| Storage growth dashboard     | Storage Lens       |
| Review encryption status     | Inventory report   |
| Prepare for Batch Operations | Inventory manifest |

Tasks:

1. Create a destination bucket for Inventory reports.
2. Configure an Inventory report.
3. Explore the Storage Lens dashboard.
4. Compare the information provided by both services.
5. Document how each service supports storage management and optimization.

---

# Cleanup

Keep:

* Inventory configuration
* Destination bucket
* Inventory reports
* Storage Lens dashboard

These resources can be reused in later chapters for cost optimization and operational reviews.

---

# Chapter Summary

As Amazon S3 environments grow, understanding what is stored becomes just as important as storing the data itself. Amazon S3 Inventory provides detailed object-level reports that support auditing, compliance, and automation, while Amazon S3 Storage Lens delivers high-level insights into storage usage, object counts, and growth trends.

Together, these services help Cloud Engineers monitor storage, identify optimization opportunities, and make informed operational decisions in large-scale production environments.

---

# Key Takeaways

* **Amazon S3 Inventory** generates scheduled reports containing metadata for objects stored in a bucket.
* **Amazon S3 Storage Lens** provides dashboards and analytics across buckets and accounts.
* Inventory is useful for audits, Batch Operations, and compliance reporting.
* Storage Lens helps monitor storage growth and identify optimization opportunities.
* Using Inventory and Storage Lens together improves visibility into large Amazon S3 environments and supports better operational decision-making.
