## Web Identity Federation
This is a simple serverless application which uses Web Identity Federation. Credit goes to ##Adrian Cantrill.

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

