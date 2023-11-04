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
