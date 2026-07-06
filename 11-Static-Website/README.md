# Chapter 11 — Amazon S3 Static Website Hosting

> **Difficulty:** Intermediate
> **AWS Services Used:** Amazon S3, AWS IAM (Optional), Amazon Route 53 (High-Level)
> **Lab Type:** Hands-on
> **Production Relevance:** ★★★★☆

---

# Scenario

CloudMart's development team has completed a new marketing website for an upcoming product launch.

Currently, the website files are stored locally:

```text
index.html
about.html
contact.html
css/
images/
js/
```

The developers need a simple, scalable, and cost-effective way to host the website without managing web servers.

Instead of launching an EC2 instance, installing Apache or Nginx, and maintaining operating systems, CloudMart decides to use **Amazon S3 Static Website Hosting**.

As the Cloud Engineer, your responsibility is to host the website securely, configure public access correctly, and understand when Amazon S3 is an appropriate choice for website hosting.

---

# Learning Objectives

By the end of this chapter, you will be able to:

* Understand Static Website Hosting.
* Configure an S3 bucket for website hosting.
* Upload website files.
* Configure index and error documents.
* Understand Bucket Policies for public websites.
* Understand Block Public Access settings.
* Troubleshoot common website issues.
* Understand production limitations of S3 websites.

---

# What is Static Website Hosting?

Amazon S3 can host websites that consist of static files.

Examples include:

* HTML
* CSS
* JavaScript
* Images
* Videos
* Fonts

Since these files do not require server-side processing, Amazon S3 can serve them directly to users.

---

# Static vs Dynamic Websites

| Static Website          | Dynamic Website                                    |
| ----------------------- | -------------------------------------------------- |
| HTML, CSS, JavaScript   | PHP, Python, Java, Node.js                         |
| No backend processing   | Backend logic required                             |
| Hosted directly from S3 | Requires EC2, ECS, Elastic Beanstalk, Lambda, etc. |
| Low cost                | Higher operational cost                            |

Examples of Static Websites:

* Portfolio
* Company Landing Page
* Documentation Site
* Product Catalog
* Resume Website

Examples of Dynamic Websites:

* Banking Portal
* E-commerce Checkout
* Social Media Platform
* Hospital Management System

---

# How S3 Website Hosting Works

```text
User
   │
   ▼
Amazon S3 Bucket
   │
index.html
images/
css/
js/
```

When a user opens the website URL, Amazon S3 serves the requested files directly.

No web server is involved.

---

# Website Endpoint

Once website hosting is enabled, Amazon S3 generates a **Website Endpoint** similar to:

```text
http://cloudmart-website.s3-website-ap-southeast-1.amazonaws.com
```

This endpoint is different from the normal S3 object URL and is specifically designed for website hosting.

---

# Hands-on Lab 1 — Create a Website Bucket

Create a new bucket.

Suggested Name:

```text
cloudmart-static-website
```

Configuration:

* Disable **Block All Public Access** (required for public websites)
* Leave Versioning disabled (optional for this lab)

Create the bucket.

---

# Hands-on Lab 2 — Upload Website Files

Create a simple website on your computer.

Example:

```text
index.html
about.html
error.html
images/
```

Upload all files to the bucket.

Maintain the folder structure.

---

# Hands-on Lab 3 — Enable Static Website Hosting

Open your bucket.

Navigate to:

**Properties**

Scroll to:

**Static Website Hosting**

Click:

**Edit**

Configuration:

Hosting Type:

```text
Host a Static Website
```

Index Document:

```text
index.html
```

Error Document:

```text
error.html
```

Save the changes.

Copy the generated Website Endpoint.

Do not open it yet.

---

# Why Doesn't the Website Work Yet?

If you open the Website Endpoint now, you'll most likely receive:

```text
403 Forbidden
```

Why?

Because the bucket is still private.

Even though website hosting is enabled, Amazon S3 still blocks public access to the objects.

This is an important security concept: **hosting is separate from permissions**.

---

# Hands-on Lab 4 — Configure Bucket Policy

Navigate to:

**Permissions → Bucket Policy**

Replace the bucket name with your own and apply the following policy:

```json
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Sid":"PublicRead",
      "Effect":"Allow",
      "Principal":"*",
      "Action":"s3:GetObject",
      "Resource":"arn:aws:s3:::cloudmart-static-website/*"
    }
  ]
}
```

After saving, update the bucket name in the policy to match your bucket exactly.

---

# Hands-on Lab 5 — Test Your Website

Open the Website Endpoint in a browser.

Verify:

* Home page loads successfully.
* Images display correctly.
* CSS is applied.
* Navigation links work.

Congratulations! You have successfully hosted a static website using Amazon S3.

---

# Custom Error Pages

Instead of showing a generic AWS error, Amazon S3 can display your own error page.

Example:

```text
404 Page Not Found

Oops!

The page you're looking for doesn't exist.

Return to Home
```

This improves user experience and gives your website a more professional appearance.

---

# Hands-on Lab 6 — Test Error Page

Open a URL that does not exist.

Example:

```text
website-url/products.html
```

Verify that your custom `error.html` page is displayed.

---

# Public Access Block

Amazon S3 enables **Block Public Access** by default to protect buckets from accidental exposure.

For static website hosting, you may need to disable specific settings to allow public read access.

Always understand **why** you are changing these settings.

Never disable public access on buckets that store confidential or internal data.

---

# Production Best Practices

In production environments:

* Host only public content in website buckets.
* Keep sensitive files in private buckets.
* Enable Versioning for website rollback.
* Enable Default Encryption.
* Enable Server Access Logging.
* Use CloudTrail for auditing.
* Restrict write permissions to administrators only.

---

# High-Level Architecture

Most enterprise websites use additional AWS services.

```text
Users
   │
   ▼
Amazon CloudFront (CDN)
   │
   ▼
Amazon S3 Website Bucket
```

Benefits include:

* Faster global performance
* HTTPS support
* Edge caching
* Better security
* Lower latency

For this course, S3 website hosting is sufficient. CloudFront will be covered separately in a future AWS networking or CDN module.

---

# Common Mistakes

* Forgetting to update the bucket name in the Bucket Policy.
* Leaving Block Public Access enabled.
* Using the S3 object URL instead of the Website Endpoint.
* Misspelling `index.html`.
* Forgetting to upload CSS, JavaScript, or image folders.
* Granting public write access instead of read-only access.

---

# Troubleshooting

### Problem

**403 Forbidden**

Possible Causes:

* Bucket Policy is incorrect.
* Block Public Access is still enabled.
* Objects are not publicly readable.

---

### Problem

Images do not load.

Possible Causes:

* Incorrect folder path.
* Image files were not uploaded.
* Object names do not match the HTML references.

---

### Problem

Website loads but styling is missing.

Possible Causes:

* CSS file path is incorrect.
* CSS files are not publicly accessible.

---

### Problem

The wrong page opens.

Possible Cause:

The configured Index Document does not match the uploaded file name.

---

# Interview Questions

1. What is Amazon S3 Static Website Hosting?
2. What types of websites can be hosted on S3?
3. Why do we need a Bucket Policy for a public website?
4. What is the purpose of the Index Document?
5. What is the Error Document?
6. Why is Block Public Access enabled by default?
7. What is the difference between the Website Endpoint and the S3 Object URL?
8. Can Amazon S3 host a PHP or Node.js application? Why or why not?
9. Why is CloudFront commonly placed in front of an S3 website?
10. What security considerations should you keep in mind when hosting a public website on S3?

---

# Challenge Lab

CloudMart wants to launch a product landing page.

Requirements:

* Create a bucket for website hosting.
* Upload a simple website with at least three pages.
* Configure `index.html` and `error.html`.
* Apply the correct Bucket Policy.
* Test the Website Endpoint.
* Verify that images and CSS load correctly.
* Test the custom error page.
* Enable Versioning to prepare for future website updates.

Document the steps and explain why each configuration is required.

---

# Cleanup

Keep the following resources:

* Website bucket
* Website files
* Bucket Policy
* Versioning configuration (if enabled)

These resources will be reused in future chapters, where you will integrate event notifications, logging, and automation.

---

# Chapter Summary

Amazon S3 Static Website Hosting provides a simple, scalable, and cost-effective way to host static websites without managing servers. In this chapter, you learned how to configure a bucket for website hosting, upload website assets, apply the correct Bucket Policy, and troubleshoot common issues such as **403 Forbidden** errors.

You also explored the role of **Block Public Access**, custom error pages, and the differences between a website endpoint and a standard S3 object URL. Finally, you gained an understanding of how production architectures often combine **Amazon S3** with **Amazon CloudFront** for improved performance and security.

---

# Key Takeaways

* Amazon S3 can host **static** websites but not server-side applications.
* Static Website Hosting must be explicitly enabled.
* Public websites require a Bucket Policy that allows `s3:GetObject`.
* The Website Endpoint is different from the standard S3 object URL.
* Block Public Access protects buckets by default and should only be adjusted when required.
* For production environments, S3 is commonly paired with CloudFront to improve performance, enable HTTPS, and enhance security.
* Understanding how permissions, website hosting, and public access interact is essential for securely hosting static content.
