#I.- AWS CodeDeploy Configuration


##Step 1: Files to Store in the GitHub Repository

The file appspec.yaml contains the instructions to transfer files from GitHub Source to AWS EC2 Destination.

The script directy contains some .sh files with some commands to execute before or after the file transfer 
action.

The file appspec.yalm and the script directoy and its files should be store in the Github Repository at the 
root level.

##Step 2:  AWS permission configuracion

Go to the IAM Management Console and Create 2 roles:

1) Role Name: CodeDeploy with the Policy Name: AWSCodeDeployRole add in the Trush Relationship this code:

        {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "",
            "Effect": "Allow",
            "Principal": {
              "Service": "codedeploy.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
          }
        ]
        }


2) Role Name: CodeDeploy-E2 with the Policy Name: AmazonS3ReadOnlyAccess.

##Step 3: Create an EC2 instance (Ubuntu / Linux)  
        
- Set IAM Role as code-deploy-ec2
- Enable Protect against accidental termination
- Add a Tag Name for the Instance
- Configure Security Group (Open Ports)
    
##Step 4: Install the CodeDeploy Agent

      sudo apt-get install python-pip ruby wget
      cd /home/ubuntu
      wget https://aws-codedeploy-{set the instance region like us-south-1}.s3.amazonaws.com/latest/install
      chmod +x ./install
      sudo ./install auto
      sudo service codedeploy-agent start
      sudo service codedeploy-agent status
 
 
#II.-Make a Deploy from Github to AWS Manually 


##1) Go to the AWS CodeDeploy Service and Create an Application:
  
- Set an Application Name, Deployment Group Name and Add the Instance for the Development Group.
- Select the Deployment Configuracion (Ex.OneAtATime).
- Select the Service Role "arn:aws:iam::....../DeployCode".
- Press the button Create Application.
   
##2) Deploy a New Revision / Commit
  
Go to your GitHub Repository and make any commit after that Deploy a New Revision with the AWS CodeDeploy Service and follow this instructions.
    
- Select the Deployment Group from the table.

- Select the Action "Deploy new revision" to display the Page Create a New Deploy.

Create Deploymenet Page
- Select the Application Name.
- Select the Deployment Group.
- Select the Revision Type: "MyApplication is sotred in GitHub"
- Press the button Connect to GitHub.
- Input the Github Repository Name.
- Input a CommitID (Found in the Github Commits History)
- Select Rollback Configuration.
- Press button Deploy Now
    
    
#III.- Autodeploy from Github to AWS EC2 instance

##Step 1: Create an IAM Policy
   
Create a IAM policy which give access to register and create a new deployment, also to create new revision 
for a deployment group.
  
a) Select a create Your Own Policy and give a name like codedeploy-github-access

b) Paste this json objet in the Policy Document Field:
        
        {
          "Version": "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Action": "codedeploy:GetDeploymentConfig",
              "Resource": "arn:aws:codedeploy:ACCOUNT_REGION:ACCOUNT_ID:deploymentconfig:*"
            },
            {
              "Effect": "Allow",
              "Action": "codedeploy:RegisterApplicationRevision",
              "Resource": "arn:aws:codedeploy:ACCOUNT_REGION:ACCOUNT_ID:application:APPLICATION_NAME"
            },
            {
              "Effect": "Allow",
              "Action": "codedeploy:GetApplicationRevision",
              "Resource": "arn:aws:codedeploy:ACCOUNT_REGION:ACCOUNT_ID:application:APPLICATION_NAME"
            },
            {
              "Effect": "Allow",
              "Action": "codedeploy:CreateDeployment",
              "Resource": "arn:aws:codedeploy:ACCOUNT_REGION:ACCOUNT_ID:deploymentgroup:
              APPLICATION_NAME/DEPLOYMENT_GROUP"
            }
          ]
        }
        
c) Change in the Json Objet the words
    
- ACCESS_REGION with the E2 Instance Region (lIKE us-west-2).
- ACCOUNT_ID with the one found in My Account Page -> Congiguration Account.
- APPLICATION_NAME found in the CodeDeploy Console.
- DEPLOYMENT_GROUP found in the CodeDeploy Console.

##Step 2: Create an IAM User

- Give a Name to the User like CodeDeployUser
- Attach the Policy created in the Step 1
- Download/Copy the Access ID and the Secret Access Token and store in a secure place.

##Step 3: Github Integration

a) Generate a new personal aceess token in the Personal Setting Page -> Develop Settings -> Personal access tokens
   
- Set a Token Description like autodeploy-codedeploy
- Check repo:status and repo_deployment
- Press the button Regenerate Token
- Download/Copy the Github Token and store in a secure place, it will be need for the Github AutoDeployment integration.

b) Crete two integration services in Github
   
Go to Proyect Settings -> Integration and Services

Add a Service: AWS CodeDeploy

- Set the Application Name found in the ASW CodeDeploy Console.
- Set the Development Group found in the ASW CodeDeploy Console.
- Set the AWS Access Key from the user (codedeploy-github-user) created in the AWS CodeDeploy Console.
- Set the AWS Instance Region (like us-west 2).
- Set the AWS Secret Access Key from the user (codedeploy-github-user) created in the AWS CodeDeploy Console.
- Press the button Add Servie.

Other fields are optional.

Add a Service: Github Auto-Deployment

- Input the Github Token Generated above.
- Input the Enviromentment take it from the AWS CodDeploy Development Group.
- If you are using Continues Integration (CI) check Deploy on status

Other fields are optional.

Now when we edit file and commit on master branch or merge any pull request a new deployment will be created 
on AWS CodeDeploy base on the appspec.yaml file.  

Done!!!    


###REFERENCES:

[Autodeploy from github using AWS CodeDeploy](http://crypt.codemancers.com/posts/2016-12-26-autodeploy-from-github-using-aws-codedeploy/), From: crypt.codemancers.com, Author: Revath S Kumar  Publish Date: December 26, 2016

