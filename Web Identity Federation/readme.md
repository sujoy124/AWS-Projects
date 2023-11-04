# Web Identity Federation
This is a simple serverless application which uses Web Identity Federation. Credit goes to **Adrian Cantrill**.

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


### STAGE 1 : Provision the environment

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

## STAGE 2 : Create Google API Project & Client ID

Any application that uses OAuth 2.0 to access Google APIs must have authorization credentials that identify the application to Google's OAuth 2.0 server
You will need a valid google login, GMAIL will do. If you don't have one, you will need to create one as part of this process.
- Move to the Google Credentials page https://console.developers.google.com/apis/credentials
- Either sign in, or create a google account. You will be moved to the Google API Console
- Click the Select a project dropdown, and then click NEW PROJECT.  For project name enter PetIDF and Click Create
- Next Click CONFIGURE CONSENT SCREEN. because our application will be usable by any google user, we have to select external users
- Check the box next to External and click CREATE
- Next give the application a name .enter PetIDF in the App Name box.
- enter your own email in user support email, enter your own email in Developer contact information
- Click SAVE AND CONTINUE for the next 2/3 screens and in the final screen Click BACK TO DASHBOARD.
- Click Credentials on the menu on the left
- Click CREATE CREDENTIALS and then OAuth client ID
- In the Application type download select Web Application
- Under Name enter PetIDFServerlessApp
- We need to add the WebApp URL, this is the distribution domain name of the cloudfront distribution (making sure it has https:// before it)
- Click ADD URI under Authorized JavaScript origins
- Enter the endpoint URL, you need to enter the Distribution DNS Name of your CloudFront distribution (created by the 1-click deployment), you should add https:// at the start, it should look something like this https://d38sv1tnkmk8i6.cloudfront.net but you NEED to use your own distributions DNS name DONT USE THIS ONE
- Click CREATE.
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/f37439f4-4401-4baa-8c91-4a9194534600)

- You will be presented with two pieces of information: Client ID, Client Secret. Note down the Client ID.Client Secret is not going to be used.
- Once noted down safely, click OK
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/ccde28a0-3e8f-41b6-ab2a-0df4fabe4fed)

## STAGE 3 : Create Cognito Identity Pool

- Move to the Cognito Console. On the menu on the left, select Federated Identities for creating a new identity pool
- If this is your first, the creation process will begin immediately, if you already have any identity pools you'll have to click federated identities then click on Create new identity pool
- In Identity pool name enter PetIDFIDPool
- Expand Authentication Providers and click on Google+
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/df419f3d-1ae2-4ec1-84aa-2e72921e83be)

- In the Google Client ID box, enter the Google Client ID you noted down in the previous step.
- Click Create Pool
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/7081e440-8094-44ba-9ee4-1fa853885cf1)
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/c5a3af97-ce9f-4e4a-8c52-d3886306c584)
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/22072d5a-5ef0-4d29-9c35-8926528dabfd)

- Expand View Details. This is going to create two IAM roles. One for Your authenticated identities and another for your Your unauthenticated identities. For now, we're just going to click on Allow we can review the roles later.
- You will be presented with your Identity Pool ID, note this down, you will need it later. Click to move back to the dashboard
- Move to the IAM Console. Click Roles. 
- Locate and click on **Cognito_PetIDFIDPoolAuth_Role**
- Click on Trust Relationships
- See how this is assumable by cognito-identity.amazonaws.com
- With two conditions
   - StringEquals cognito-identity.amazonaws.com:aud your congnito ID pool
   - ForAnyValue:StringLike cognito-identity.amazonaws.com:amr authenticated
- This means to assume this role - user has to be authenticated by one of the ID providers defined in the cognito ID pool. When WEDIDF used with cognito, this role is assumed by cognito, and its what generates temporary AWS credentials which are used to access AWS resources.
- Click permissions. The cloudformation template created a managed policy which can access the privatepatches bucket Click Add permissions and then Attach policies
- Type PrivatePatches in the search box and press enter
- Check the box next to PrivatePatchesPermissions and click Attach Policies
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/f76bd5dd-f688-459b-b5d8-3f4386a5736f)
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/2f6bcdd4-6dbc-4958-b6e7-7339e9a6c542)

### STAGE 4 : Update App Bucket & Test Application

- Move to the S3 Console
- Open the webidf-appbucket- bucket/ Public Bucket
- select index.html and click Download & save the file locally
- select scripts.js and click Download & save the file locally
- both files are also attached here.
- Open the local copy of index.html in a code editor.
- Locate the REPLACE_ME_GOOGLE_APP_CLIENT_ID placeholder
- Replace this with YOUR CLIENT ID
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/978c0324-bf40-40ed-abfd-f1497cae6919)

- Save index.html and upload it to the webidf-appbucket- bucket/ Public Bucket.
- Open the local copy of scripts.js in a code editor.
- Locate the IdentityPoolId: REPLACE_ME_COGNITO_IDENTITY_POOL_ID placeholder
- Replace the REPLACE_ME_COGNITO_IDENTITY_POOL_ID part with your IDENTITY POOL ID you noted down in the previous step
- Locate the Bucket: "REPLACE_ME_NAME_OF_PATCHES_PRIVATE_BUCKET"  placeholder.
- Replace REPLACE_ME_NAME_OF_PATCHES_PRIVATE_BUCKET with with bucket name of the webidf-patchesprivatebucket- bucket.
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/85b0870e-0d10-4882-b246-2af2cce79fa5)
![image](https://github.com/sujoy124/AWS-projects/assets/91733661/9c259228-ffb1-4674-9ebe-c007b5cd69b8)

- Save scripts.js and upload it to the webidf-appbucket- bucket/ Public Bucket.
- Open the WebApp URL you noted down earlier, the distribution domain name of the cloudfront distribution
- This is the web app, with no access to any AWS resources right now
- Open any browser web developer tools (firefox tool->browser tools-> web developer tools). it might be called browser console in other browsers, it will log any output from javascript running in the web browser.
- With the browser console open, Click Sign In. Sign in with google account
- When we click the Sign In button a few things happen:- (watch the console)
    - authenticate with the Google IDP
    - a Google Access token is returned
    - This token is provided as part of the API Call to Cognito. If successful this exchanges this for Temporary AWS credentials
    - These are used to list objects in the private bucket. for all objects, presignedURLs are generated and used to load the images in the browser.
    - Once signed in, it should display files/images loaded from a private S3 bucket
    - Click on each of them, notice the URL which is used? it's a presignedURL generated by the JS running in browser, using the API's which can be accessed using the cognito credentials.
- All of this is done with no self-managed compute.


