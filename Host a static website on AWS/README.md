## Host a static website on AWS
Hosting a static website on AWS (Amazon Web Services) involves several steps. 
### 1.	Create an AWS Account:
Go to the AWS website (https://aws.amazon.com/) and follow the instructions to create an account. It is best practise to create an admin user account using root account and use the admin account should be used throughout which is inline with real-life prod account.
### 2.	Access AWS Management Console:
Once you have an AWS account, log in to the AWS Management Console.
### 3.	Choose a Storage Service:
AWS offers multiple services for hosting static websites. The most common options are Amazon S3 (Simple Storage Service) and AWS Amplify Console. Here, we'll use Amazon S3.
### 4.	Create an S3 Bucket:
- Go to the AWS S3 service in the AWS Management Console.
- Click on the "Create bucket" button.
- Choose a unique and descriptive name for your bucket. 
- Select a region for your bucket (choose the one closest to your target audience).
- Configure other options as needed and click "Create bucket."
### 5.	Upload Your Website Files and set Bucket permissions:
- Inside S3 bucket, click on the "Upload" button.
- Select the HTML, CSS, JavaScript, and other static files that make up the website. Here I have used an html file and some photos.
- Click "Upload."
- By default, S3 buckets and their objects are private. To make your website files publicly accessible, Set the bucket permissions to allow public access.
### 6.	Enable Static Website Hosting:
- In your S3 bucket's properties, go to the "Static website hosting" card.
- Click "Use this bucket to host a website."
- Enter the index document (usually "index.html") and the error document (if any).
- Click "Save."
### 7.	Configure DNS (Domain Name System):
To use a custom domain for your website, we will need to configure DNS. This typically involves creating a CNAME record or an alias record pointing to your S3 bucket's endpoint. AWS Route 53 or another domain registrar can be used for this step.
- Open the Amazon Route 53 console.
- Choose "Domain registration" and then "Register domain."
- Follow the prompts to purchase your custom domain.
- In the "Route 53 hosted zones," create a new record set.
- Enter your S3 bucket's endpoint as the alias target.

### 8.	Testing the Website:
After DNS propagation (which may take some time), test the static website using custom domain.

### Optional: 

#### Enable SSL:
For secure browsing, consider using AWS Certificate Manager to provision an SSL/TLS certificate for your custom domain and then configure your website to use HTTPS.

#### Content Distribution:
To improve website performance, consider using AWS CloudFront as a content delivery network (CDN) to distribute your website's content globally.

#### Monitoring and Scaling:
Set up monitoring, logging, and scaling as needed for your website to ensure it can handle traffic efficiently.


