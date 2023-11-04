## Web Identity Federation
This is a simple serverless application which uses Web Identity Federation. Credit goes to ## Adrian Cantrill.

The application uses the followings: 
- S3 for front-end application hosting via CloudFront
- Google API Project as an ID Provider
- Cognito and IAM Roles to swap Google Token for AWS credentials.
  
The application runs from a browser, gets the user to login using a Google ID and then loads all files from a private S3 bucket into a browser using presigned URLS.

This consists of 5 stages:-

- STAGE 1 : Provision the environment
- STAGE 2 : Create Google API Project & Client ID
- STAGE 3 : Create Cognito Identity Pool
- STAGE 4 : Update App Bucket & Test Application
- STAGE 5 : Cleanup

Project setup/architecture is as follows:

![image](https://github.com/sujoy124/AWS-projects/assets/91733661/071e9056-1a4e-4324-9ef5-19158a8bedb1)


### STAGE 1 : Provision the environment and review tasks

- Login to an AWS Account
- Region is set to us-east-1 N. Virginia
- Use the attached CloudFromation template to auto configure the infrastructure the app requires and Click Create Stack
- Wait for the STACK to move into the CREATE_COMPLETE state before continuing.
- Open the S3 console and locate the new buckets
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/151a8165-e1d2-4fe5-b779-a6c612b62b84)

- Open the bucket starting webidf-appbucket
- It should have objects within it, including index.html and scripts.js. examples are attached here.
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/2f71b17f-1bfb-46af-ad64-8d3badb04834)

- Click the Permissions Tab. Verify Block all public access is set to Off
- Click Bucket Policy. Verify there is a bucket policy
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/17fa6585-2bc1-4148-bed7-54ae44f61f34)

- Open the bucket starting webidf-patchesprivatebucket-
- Load the objects in the bucket so you are aware of the contents
- Verify there is no bucket policy and the bucket is entirely private.
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/72d56eaf-85b2-466c-8cba-37577dedce88)
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/b4e20cd6-09a3-4a86-98e4-cf32f5899f1c)

- Next open the CloudFront consle
- Locate the distribution pointing at origin webidf-appbucket and click
- Locate the distribution domain name. Note down as the WebApp URL this name prefixed with https i.e if yours is d1o4f0w1ew0exo.cloudfront.net then your WebApp URL is https://https://d1o4f0w1ew0exo.cloudfront.net (note, the copy icon may copy the https:// for you)
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/2cfe09b7-aa15-44e6-92a9-bf6af59e61f1)


