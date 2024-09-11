Here’s a sample README file for your project on deploying a static website with Terraform on an S3 bucket:

---

# Static Website Hosting on AWS S3 with Terraform

This project demonstrates how to host a static website on AWS S3 using Terraform. The website includes a basic `index.html` and `error.html` file, deployed initially via the AWS Console, and later through Terraform.

## Prerequisites

- AWS account with access to S3
- Terraform installed (v0.13 or later)
- Basic knowledge of AWS services, S3, and Terraform

## Project Structure

- **index.html**: Main page for the website.
- **error.html**: Error page to be displayed when a user encounters an error.
- **Terraform scripts**: Automate the S3 bucket creation, website configuration, and file uploads.

## Steps

### 1. Create the S3 Bucket
Create an S3 bucket to host the website. Ensure it has a globally unique name.

### 2. Disable Public Access Block
Update the bucket settings to disable public access block, allowing access via the internet.

### 3. Apply a Bucket Policy
Define a bucket policy that grants `s3:GetObject` permissions to allow public read access for all objects within the bucket. Example policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

### 4. Configure Static Website Hosting
Configure the S3 bucket for static website hosting. This involves specifying the index and error documents (e.g., `index.html` and `error.html`).

```hcl
resource "aws_s3_bucket_website_configuration" "website" {
  bucket = aws_s3_bucket.static_website_bucket.id

  index_document {
    suffix = "index.html"
  }

  error_document {
    key = "error.html"
  }
}
```

### 5. Create HTML Files
Create `index.html` and `error.html` files with some dummy content:

#### `index.html`
```html
<!doctype html>
<html>
<head>
  <title>My Static S3 Website</title>
  <meta name="description" content="My Static S3 Website">
  <meta name="keywords" content="terraform S3 aws hashicorp">
</head>
<body>
  This is the main index.html
</body>
</html>
```

#### `error.html`
```html
<!doctype html>
<html>
<head>
<title>My Static S3 Website</title>
<meta name="description" content="My Static S3 Website">
<meta name="keywords" content="terraform S3 aws hashicorp">
</head>
<body>
    Ooops, this page doesn't exit!
</body>
</html>
```

### 6. Upload Files Manually (Initial Test)
Upload `index.html` and `error.html` to the S3 bucket using the AWS Console. Verify that you can access the static website via the internet.

### 7. Automate File Upload with Terraform
After confirming the website is accessible, use Terraform to automate file uploads:

1. Delete the files from the S3 bucket via the console.
2. Use Terraform to upload the files with `aws_s3_object` resources:

```hcl
resource "aws_s3_object" "index_html" {
  bucket       = aws_s3_bucket.static_website.id
  key          = "index.html"
  source       = "build/index.html"
  etag         = filemd5("build/index.html")
  content_type = "text/html"
}

resource "aws_s3_object" "error_html" {
  bucket       = aws_s3_bucket.static_website.id
  key          = "error.html"
  source       = "build/error.html"
  etag         = filemd5("build/error.html")
  content_type = "text/html"
}
```

### 8. Output S3 Static Website Endpoint
Add an output in Terraform to display the website URL after the deployment:

```hcl
output "static_website_endpoint" {
  value = aws_s3_bucket.static_website_bucket.website_endpoint
}
```

### 9. Test the Website
Run the Terraform output command to retrieve the website URL and verify the site:

```bash
terraform output static_website_endpoint
```

Visit the URL in your browser to confirm the static website is running.

---
