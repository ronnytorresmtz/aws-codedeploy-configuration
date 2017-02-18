#I.- AWS CodeDeploy Configuration


##Step 1: Files to Store in the GitHub Repository

    The file appspec.yaml contains the instructions to transfer files from GitHub Source to AWS EC2 Destination.

    The script directy contains some .sh files with some commands to execute before or after the file transfer 
    action.

    The file appspec.yalm and the script directoy and its files should be store in the Github Repository at the 
    root level.

##Step 2:  AWS permission configuracion

 ##Go to the IAM Management Console and Create 2 roles:

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


##Step 3: Intall the CodeDeploy Agent

      sudo apt-get install python-pip ruby wget
      cd /home/ubuntu
      wget https://aws-codedeploy-{set the instance region like us-south-1}.s3.amazonaws.com/latest/install
      chmod +x ./install
      sudo ./install auto
      sudo service codedeploy-agent start
      sudo service codedeploy-agent status
 
 


#II.-Deploy from Github to AWS Manually 


##1) Go to the AWS CodeDeploy Service and Create an Application:
  
    a) Set an Application Name, Deployment Group Name and Add the Instance for the Development Group.
    b) Select the Deployment Configuracion (Ex.OneAtATime).
    c) Select the Service Role "arn:aws:iam::....../DeployCode".
    d) Press the button Create Application.
   
##2) Deploy a New Revisión (commit)
  
    a) Select the Deployment Group from the table.
    b) Select the Action "Deploy new revision" to display the Page Create a New Deploy.
    
    Create Deploymenet Page
    c) Select the Application Name.
    d) Select the Deployment Group.
    c) Select the Revision Type: "MyApplication is sotred in GitHub"
    d) Press the button Connect to GitHub.
    e) Input the Github Repository Name.
    f) Input a CommitID (Found in the Github Commits History)
    g) Select Rollback Configuration.
    h) Press button Deploy Now
    

