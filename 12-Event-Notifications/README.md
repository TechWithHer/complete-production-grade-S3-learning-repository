# Chapter 12 — Amazon S3 Event Notifications

> **Difficulty:** Intermediate
> **AWS Services Used:** Amazon S3, AWS Lambda, Amazon SNS, Amazon SQS
> **Lab Type:** Hands-on
> **Production Relevance:** ★★★★★

---

# Scenario

CloudMart is growing rapidly.

Every day, customers upload:

* Product Images
* Invoices
* Profile Pictures
* CSV Reports
* Documents

Currently, employees manually monitor the bucket for new uploads.

This process is slow and doesn't scale.

The Solution Architect proposes an event-driven architecture.

Whenever an object is uploaded to Amazon S3:

* Resize images automatically.
* Notify the Finance Team.
* Send messages to downstream applications.
* Trigger automation without human intervention.

As the Cloud Engineer, your task is to configure **Amazon S3 Event Notifications** to automate these workflows.

---

# Learning Objectives

By the end of this chapter, you will be able to:

* Understand event-driven architecture.
* Configure Amazon S3 Event Notifications.
* Trigger AWS Lambda functions.
* Send notifications to Amazon SNS.
* Send messages to Amazon SQS.
* Use Prefix and Suffix Filters.
* Design production-ready event-driven workflows.
* Troubleshoot common notification issues.

---

# What Are Event Notifications?

Normally, an application checks the bucket repeatedly to see if new files have been uploaded.

```text
Application
      │
Check Bucket?
      │
Check Again?
      │
Check Again?
```

This approach wastes resources.

Instead, Amazon S3 can notify other AWS services immediately after an event occurs.

```text
Upload File
      │
      ▼
Amazon S3
      │
      ▼
Notification
      │
      ▼
AWS Service
```

This is called **Event-Driven Architecture**.

---

# Supported Destinations

Amazon S3 can send notifications to:

| Destination | Purpose                                    |
| ----------- | ------------------------------------------ |
| AWS Lambda  | Execute code automatically                 |
| Amazon SNS  | Send notifications to multiple subscribers |
| Amazon SQS  | Queue messages for applications            |

Each destination serves a different business need.

---

# Common S3 Events

| Event              | Description                                |
| ------------------ | ------------------------------------------ |
| Object Created     | A new object is uploaded                   |
| Object Removed     | An object is deleted                       |
| Restore Completed  | Glacier restore completed                  |
| Replication Events | Replication status changes                 |
| Lifecycle Events   | Lifecycle transitions (selected scenarios) |

The most commonly used event is:

```text
Object Created
```

---

# Production Example

CloudMart uploads a product image.

```text
product-image.jpg
```

Amazon S3 immediately triggers a Lambda function.

The Lambda function:

* Resizes the image.
* Creates a thumbnail.
* Stores it in another bucket.

No manual intervention is required.

---

# Hands-on Lab 1 — Explore Event Notifications

Open your bucket.

Navigate to:

**Properties**

Scroll to:

**Event Notifications**

Click:

**Create Event Notification**

Observe the available destination types.

Do not save the configuration yet.

---

# Event Notifications Using AWS Lambda

Lambda allows Amazon S3 to run code automatically.

Example Workflow:

```text
User Uploads Image
        │
        ▼
Amazon S3
        │
Object Created
        ▼
AWS Lambda
        │
Resize Image
        ▼
Thumbnail Bucket
```

Common use cases:

* Image resizing
* Virus scanning
* File validation
* Metadata extraction
* Watermarking

---

# Hands-on Lab 2 — Configure Lambda Notification

Prerequisite:

Create a simple Lambda function.

Example Name:

```text
image-processing-function
```

Return to your S3 bucket.

Create an Event Notification.

Configuration:

Event Name:

```text
ImageUpload
```

Event Type:

```text
Object Created
```

Destination:

```text
AWS Lambda
```

Choose your Lambda function.

Save.

Upload a test image.

Verify that the Lambda function is invoked.

---

# Event Notifications Using Amazon SNS

Amazon SNS broadcasts notifications to multiple subscribers.

Example:

```text
Amazon S3
      │
      ▼
Amazon SNS
      │
 ┌────┴────┐
 ▼         ▼
Email    SMS
```

Use cases:

* Notify administrators
* Send alerts
* Trigger multiple applications
* Incident notifications

---

# Hands-on Lab 3 — Configure SNS Notification

Create an SNS Topic.

Example:

```text
cloudmart-file-upload-topic
```

Subscribe using your email.

Return to the S3 bucket.

Create another Event Notification.

Destination:

```text
Amazon SNS
```

Upload a file.

Confirm that you receive an email notification.

---

# Event Notifications Using Amazon SQS

Sometimes applications cannot process events immediately.

Instead of sending notifications directly,

Amazon S3 places messages into a queue.

```text
Amazon S3
      │
      ▼
Amazon SQS
      │
      ▼
Application
```

Benefits:

* Reliable processing
* Retry capability
* Decoupled architecture

Common use cases:

* Order processing
* Data ingestion
* Batch processing
* Report generation

---

# Hands-on Lab 4 — Configure SQS Notification

Create an SQS Queue.

Example:

```text
cloudmart-upload-queue
```

Create an Event Notification.

Destination:

```text
Amazon SQS
```

Upload a file.

Open the queue.

Verify that a message has been received.

---

# Prefix Filters

Sometimes you only want notifications for specific folders.

Example Bucket:

```text
images/
documents/
logs/
videos/
```

You only want image uploads to trigger Lambda.

Configure:

```text
Prefix

images/
```

Now uploads to:

```text
images/profile.jpg
```

trigger the notification.

Uploads to:

```text
documents/report.pdf
```

do not.

---

# Suffix Filters

Suffix filters work with file extensions.

Example:

```text
.jpg
.png
.pdf
.csv
```

If you configure:

```text
.jpg
```

Only JPEG uploads generate notifications.

---

# Hands-on Lab 5 — Configure Prefix & Suffix Filters

Create an Event Notification.

Configure:

Prefix:

```text
images/
```

Suffix:

```text
.jpg
```

Test:

Upload:

```text
images/photo.jpg
```

Notification should trigger.

Upload:

```text
documents/report.pdf
```

Notification should not trigger.

---

# Production Architecture

```text
                 Users
                    │
                    ▼
             Amazon S3 Bucket
                    │
     ┌──────────────┼──────────────┐
     ▼              ▼              ▼
 AWS Lambda      Amazon SNS     Amazon SQS
     │              │              │
 Image Resize     Email Alert    Processing Queue
```

This architecture is common in production because each service performs a specific task independently.

---

# Best Practices

* Use Prefix and Suffix filters to reduce unnecessary notifications.
* Keep Lambda functions lightweight.
* Use SQS for workloads requiring reliable processing.
* Use SNS when multiple systems need the same notification.
* Monitor failed Lambda invocations using CloudWatch.
* Follow the principle of least privilege when granting permissions.

---

# Common Mistakes

* Forgetting to grant S3 permission to invoke Lambda.
* Configuring overlapping Prefix and Suffix filters incorrectly.
* Triggering Lambda recursively by writing output back to the same monitored folder.
* Using SNS when guaranteed message processing is required (use SQS instead).
* Creating too many notifications for the same bucket without a clear design.

---

# Troubleshooting

### Problem

Lambda is not triggered.

Possible Causes:

* Missing invoke permission.
* Incorrect event type.
* Incorrect Prefix or Suffix filter.

---

### Problem

No email received from SNS.

Possible Causes:

* Subscription not confirmed.
* Incorrect topic selected.

---

### Problem

No messages appear in SQS.

Possible Causes:

* Wrong queue configured.
* Incorrect event type.
* Queue permissions prevent S3 from sending messages.

---

### Problem

Notifications trigger unexpectedly.

Possible Cause:

Filters are too broad and match more objects than intended.

---

# Interview Questions

1. What are Amazon S3 Event Notifications?
2. Which AWS services can Amazon S3 send notifications to?
3. What is the difference between Lambda, SNS, and SQS?
4. When would you choose SNS instead of SQS?
5. What is a Prefix Filter?
6. What is a Suffix Filter?
7. How would you automatically resize uploaded images?
8. How can you avoid Lambda recursion with S3 events?
9. What event would you use to process newly uploaded files?
10. How do Event Notifications support event-driven architectures?

---

# Challenge Lab

CloudMart wants to automate document processing.

Requirements:

| Folder    | Action                                     |
| --------- | ------------------------------------------ |
| images/   | Trigger Lambda to resize images            |
| invoices/ | Send an SNS email notification             |
| reports/  | Send a message to SQS for later processing |

Tasks:

1. Configure three Event Notifications.
2. Apply appropriate Prefix Filters.
3. Upload test files.
4. Verify each destination receives the expected event.
5. Document your architecture and explain why each AWS service was selected.

---

# Cleanup

Do not delete:

* S3 bucket
* Lambda function
* SNS Topic
* SQS Queue
* Event Notifications

These resources can be reused when building larger event-driven architectures in future projects.

---

# Chapter Summary

Amazon S3 Event Notifications enable applications to respond automatically whenever objects are created, deleted, or modified. Instead of continuously checking for changes, Amazon S3 immediately notifies downstream services, making applications more efficient, scalable, and responsive.

Throughout this chapter, you configured Event Notifications for **AWS Lambda**, **Amazon SNS**, and **Amazon SQS**, learned how Prefix and Suffix Filters control event flow, and explored real-world automation patterns used in production environments.

---

# Key Takeaways

* Amazon S3 Event Notifications are a core building block for event-driven architectures.
* **AWS Lambda** is ideal for automatic processing.
* **Amazon SNS** is best for broadcasting notifications.
* **Amazon SQS** is designed for reliable asynchronous processing.
* Prefix and Suffix Filters help ensure that only relevant events trigger automation.
* Proper permissions and careful architecture design are essential to building scalable, production-ready workflows.
