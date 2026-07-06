# Chapter 5 — Amazon S3 Versioning: Protecting Data from Accidental Deletion

> **Difficulty:** Beginner → Intermediate
>
> **Estimated AWS Cost:** Free Tier Eligible (approximately $0.00–$0.02)
>
> **AWS Services Used:** Amazon S3
>
> **Lab Type:** Hands-on
>
> **Production Relevance:** ★★★★★

---

# Scenario

CloudMart has now been using Amazon S3 for several months.

The Engineering team stores application configuration files, Marketing stores product images, HR stores employee documents, and Finance stores invoices.

One morning, a developer accidentally replaces the production configuration file with an outdated version.

A few hours later, another engineer accidentally deletes an important document.

The application begins to fail because critical files have been overwritten or removed.

Fortunately, CloudMart had previously enabled **Amazon S3 Versioning**.

Instead of permanently losing data, every previous version of every object is still available and can be restored.

As the Cloud Engineer, your next responsibility is to implement Versioning and understand how it protects production workloads from accidental deletion and unintended changes.

---

# Learning Objectives

By the end of this chapter, you will be able to:

* Explain what Amazon S3 Versioning is.
* Understand why Versioning is one of the most important production features.
* Enable Versioning on an existing bucket.
* Observe how multiple object versions are created.
* Understand Version IDs.
* Understand Delete Markers.
* Restore previous versions.
* Permanently delete specific object versions.
* Explain why Versioning increases storage costs.
* Identify production use cases for Versioning.

---

# Why Versioning Exists

Without Versioning:

```text
config.json
```

Upload a new version →

Old version is permanently replaced.

Delete the object →

The object is permanently removed.

Mistakes become permanent.

---

With Versioning enabled:

Every upload creates a completely new version of the object.

Deleting an object does **not** remove previous versions.

Instead, Amazon S3 adds a **Delete Marker**, making the object appear deleted while preserving all historical versions.

Versioning acts as a safety net for your data.

---

# Understanding Version IDs

Every object version receives a unique identifier called a **Version ID**.

Example:

| Object      | Version ID |
| ----------- | ---------- |
| config.json | v1         |
| config.json | v2         |
| config.json | v3         |

Although the object key remains the same:

```text
config.json
```

Amazon S3 internally stores multiple versions.

Applications normally retrieve the **latest version** unless a specific Version ID is requested.

---

# Versioning States

An S3 bucket can exist in one of three states.

| State                | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| Unversioned          | Default state for new buckets                                |
| Versioning Enabled   | Every object version is retained                             |
| Versioning Suspended | Existing versions remain, but new uploads behave differently |

For production workloads, Versioning should generally remain enabled once activated.

---

# Important Notes

Before enabling Versioning, understand these behaviors:

* Versioning cannot be partially enabled.
* It applies to the entire bucket.
* Once enabled, it cannot be completely disabled.
* It can only be suspended.
* Every object version consumes storage.
* Lifecycle policies are commonly used with Versioning to control storage costs.

---

# Architecture

```text
                    User
                      │
             Upload config.json
                      │
                      ▼
              Amazon S3 Bucket
                      │
      ┌───────────────┼───────────────┐
      │               │               │
 Version 1       Version 2      Version 3
```

Instead of replacing objects, Amazon S3 stores every version independently.

---

# Hands-on Lab 1 — Check Current Bucket Status

Open your S3 bucket.

Navigate to:

**Properties**

Locate:

**Bucket Versioning**

Observe that Versioning is currently:

```text
Disabled
```

---

# Hands-on Lab 2 — Enable Versioning

Select:

**Edit**

Enable:

**Bucket Versioning**

Save your changes.

Congratulations!

Your bucket is now protected by Versioning.

Remember:

This setting affects every object inside the bucket.

---

# Observe

Return to the bucket Properties page.

Verify that Bucket Versioning now displays:

```text
Enabled
```

---

# Hands-on Lab 3 — Upload the First Version

Create a file named:

```text
application-config.txt
```

Contents:

```text
Environment: Development

Application Version: 1.0
```

Upload the file into:

```text
documents/
```

After uploading:

Open the object.

Observe:

* Object size
* Last modified
* Storage class

At this point only one version exists.

---

# Hands-on Lab 4 — Replace the Object

Edit the local file.

New contents:

```text
Environment: Production

Application Version: 2.0
```

Upload the file again using the **same object name**.

Do not rename the file.

Amazon S3 asks whether you want to replace the existing object.

Proceed with the upload.

---

# Observe

Although the AWS Console appears to show only one object,

Amazon S3 has now stored **two independent versions**.

Enable:

**Show Versions**

Observe:

* Latest Version
* Previous Version

Each version has a unique Version ID.

---

# Hands-on Lab 5 — Upload More Versions

Modify the file several more times.

Example:

Version 3

```text
Application Version: 3.0
```

Version 4

```text
Application Version: 4.0
```

Upload after every modification.

Enable:

**Show Versions**

Observe:

How many versions exist?

Compare:

* Upload Time
* Version IDs
* Size

Notice that every upload creates another version.

---

# Hands-on Lab 6 — Download an Older Version

Select:

Version 2

Download it.

Open the file.

Compare it with:

Latest Version.

Observe that both versions still exist independently.

This allows engineers to recover historical configurations.

---

# Hands-on Lab 7 — Delete the Object

Delete:

```text
application-config.txt
```

Return to your bucket.

At first glance,

the object appears to be deleted.

Now enable:

**Show Versions**

Observe carefully.

Do you notice:

A new item called:

```text
Delete Marker
```

The object has **not** actually been removed.

Instead,

Amazon S3 hides the latest version behind a Delete Marker.

---

# Understanding Delete Markers

Delete Markers are unique to Versioning.

When you delete an object without specifying a Version ID:

Amazon S3 creates:

```text
Delete Marker
```

Instead of deleting previous versions.

This means recovery is possible.

---

# Hands-on Lab 8 — Restore the Object

Delete only the Delete Marker.

Refresh the bucket.

The object reappears.

Nothing was re-uploaded.

You simply removed the marker hiding the object.

This demonstrates why Versioning protects against accidental deletion.

---

# Hands-on Lab 9 — Permanently Delete a Version

Enable:

Show Versions

Select:

Version 1

Delete only Version 1.

Observe:

Remaining versions still exist.

Versioning allows individual versions to be permanently removed without affecting newer versions.

---

# Hands-on Lab 10 — Experiment

Create another file.

Suggested name:

```text
employee-policy.pdf
```

Upload it.

Replace it three times.

Delete it.

Restore it.

Delete one specific version.

Repeat until you are comfortable navigating object versions.

Production engineers frequently perform these recovery operations.

---

# Production Example

CloudMart updates:

```text
pricing.json
```

every morning.

At 10:15 AM,

an engineer accidentally uploads:

Yesterday's pricing.

Customers begin seeing incorrect prices.

Without Versioning:

The correct pricing file is lost.

With Versioning:

Operations simply restore the previous version within seconds.

Business continues with minimal disruption.

---

# Cost Considerations

Versioning improves data protection,

but it also increases storage usage.

Example:

Original file:

100 MB

Uploaded 10 times

Total Storage:

Approximately:

1 GB

Even though only one object appears in the bucket.

Lifecycle Rules are commonly used to automatically remove old versions after a specified retention period.

You will implement these policies in the next chapter.

---

# Common Mistakes

* Assuming deleted objects are permanently removed.
* Forgetting to enable **Show Versions**.
* Deleting the latest version instead of the Delete Marker.
* Forgetting that every version consumes storage.
* Enabling Versioning without planning lifecycle policies.

---

# Troubleshooting

### Problem

I deleted an object but storage usage did not decrease.

Possible Cause:

Previous versions still exist.

---

### Problem

I cannot find my deleted object.

Possible Cause:

Enable:

Show Versions.

---

### Problem

I deleted the wrong version.

Possible Cause:

Specific versions cannot be recovered once permanently deleted.

Always verify the Version ID before deleting.

---

### Problem

Why can't I disable Versioning?

Amazon S3 does not support turning Versioning off after it has been enabled.

You may only suspend it.

---

# Interview Questions

1. What is Amazon S3 Versioning?

2. Why is Versioning important?

3. What is a Version ID?

4. What is a Delete Marker?

5. Can Versioning be permanently disabled?

6. Why does Versioning increase storage costs?

7. What happens when an object is deleted in a versioned bucket?

8. How do you restore a deleted object?

9. Can individual versions be deleted?

10. Why do production environments almost always enable Versioning?

---

# Challenge Lab

CloudMart wants to retain every version of HR documents but automatically remove older versions of application logs after a retention period.

Think about:

* Why different data types require different retention strategies.
* Which folders should retain all versions.
* Which folders can safely discard older versions.

Do not implement the solution yet.

Document your proposed retention strategy.

You will implement it in the next chapter using Amazon S3 Lifecycle Rules.

---

# Cleanup

Do **not** suspend Versioning.

Do **not** delete the bucket.

Leave all object versions intact.

These historical versions will be required in the next chapter when configuring Lifecycle Rules to automatically manage current and non-current object versions.

---

# Key Takeaways

You have implemented one of the most valuable data protection features available in Amazon S3.

You learned that Versioning preserves every change made to an object, allowing accidental deletions and overwrites to be reversed with minimal effort. You also explored Version IDs, Delete Markers, and the storage implications of retaining multiple object versions.

In the next chapter, you will build on this foundation by implementing Amazon S3 Lifecycle Rules to automatically transition and expire both current and non-current object versions, balancing data protection with long-term cost optimization.

