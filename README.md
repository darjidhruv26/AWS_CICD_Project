# Project :)
# Implimented DevOps CI/CD pipeline using AWS Services

 - CodeCommit
 - CodeBuild
 - CodeDeploy
 - CodePipeline
 - Identity and Access Management (IAM) for creating Service Role
 - S3 for artifact storing
 - EC2 for Deployment
 
# Step 1: Simple Project Architecture

![AWSDevOps_pro](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/8df97317-8a3d-43b7-9b78-96bb9686cdff)

# Step 2: Create a Repository in AWS CodeCommit

- After creating a repository on the next page you can see this warning.😟

![WOrning aws](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/e9c9f8f2-1332-43b6-adfb-7d6a6acbd525)
- Go to IAM and create a User

- User can not get permission to access AWS CodeCommit, So first gives permissions in IAM.
   (IAM -> Users -> Security credentials -> HTTPS Git credentials for CodeCommit -> Generate)
   
- After this click on the Clone URL button.

![gitURL clone](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/ec2e4308-5ef2-48c9-ba67-0ea227354f5e)
- Also, Add permission to the User get to access this repo on Locel. 
  (IAM -> Users -> SelectUser -> Add permissions -> Attach policies directly ->   'AWSCodeCommitPowerUser')
  
- After performing some GitHubActions and pushing all files in CodeCommit.

![CodeCommit Code ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/fe415a06-1e71-4560-8e1b-3c2a31809819)

- Master is default branch.

![CodeCommit Branchs ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/c3443c4b-fb03-41d2-bf33-cd8d608e0293)

# Step 3: Create Build Project

- GO to IAM and create Service Role for CodeBuild
      Service Role ("Access Service-Service")
  (IAM -> Create Role -> AWS service -> Use case -> Create)
  
- Create a buildSpec file.
```bash
   buildspec.yml
```
```bash
version: 0.2
phases:
  install:
    commands:
      - echo Installing NGINX
      - sudo apt-get update
      - sudo apt-get install nginx -y
  build:
    commands:
      - echo Build started on `data`
      - cp index.html /var/www/html/ 
  post_build:
    commands:
      - echo Configuring NGINX

artifacts:
  files:
    - '**/*'         
```
- Push this buildspec.yml file in CodeCommi.
- After you can Start to Build 
![CodeBuild Project ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/4c334c45-8177-41be-8199-5ce5c8a13177)

# Step 4: Build Artifact store in S3 Bucket
- In the CodeBuild console Click on Edit button -> Artifacts -> Type:"S3" -> put Uplode Locetion.
![s3 artifect files ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/fb2d4b72-0dd9-4308-a4fd-d70612299607)

![CodeBuild_history](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/785ef619-a181-4710-aea4-c20082c75831)

# Step 5: CodeDeploy Part

- Create Application and Compute platform is EC2/On-premises
- Create Deployment group
- Create Service role (Give permissions -1.AmazonEC2FullAccess 2.AmazonEC2RoleforAWSCodeDeploy 3. AmazonS3FullAccess 4.AWSCodeDeployeFullAccess 5.AWSCodeDeployRole 6.AmazonEC2RoleforAWSCodeDeployLimitaccesstoS3)
- Deployment type - In-place
- Environment configuration - Amazon EC2 instances

# Step 6: Create EC2 instance for deployment

- Click Launch Instances
- Ubuntu t2.micro
- Connect to the server and run this command to convert it into a Root user

```bash
sudo su
```
- First of all select instance -> Actios -> Security -> Modify IAM role -> select relevant role

- After running this script for setup deployingAgent on the EC2 instance
- Make sure your Rigeon and Script regions same.(6th line in this script)
 
```bash
#!/bin/bash 
# This installs the CodeDeploy agent and its prerequisites on Ubuntu 22.04.  
sudo apt-get update
sudo apt-get install ruby-full ruby-webrick wget -y
cd /tmp
wget https://aws-codedeploy-us-east-1.s3.us-east-1.amazonaws.com/releases/codedeploy-agent_1.3.2-1902_all.deb
mkdir codedeploy-agent_1.3.2-1902_ubuntu22
dpkg-deb -R codedeploy-agent_1.3.2-1902_all.deb codedeploy-agent_1.3.2-1902_ubuntu22
sed 's/Depends:.*/Depends:ruby3.0/' -i ./codedeploy-agent_1.3.2-1902_ubuntu22/DEBIAN/control
dpkg-deb -b codedeploy-agent_1.3.2-1902_ubuntu22/
sudo dpkg -i codedeploy-agent_1.3.2-1902_ubuntu22.deb
systemctl list-units --type=service | grep codedeploy
sudo service codedeploy-agent status
```

![EC2 ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/a1697fff-dd42-4db5-b516-936710fb0184)

- Create appspec.yml for App configuretion


```bash
   appspec.yml
```
```bash
version: 0.0
os: linux
files:
  - source: /
    destination: /var/www/html
hooks:
  AfterInstall:
    - location: scripts/install_nginx.sh
      timeout: 300
      runas: root
  ApplicationStart:
    - location: scripts/start_nginx.sh
      timeout: 300
      runas: root    
```

- And Also create scripts folder
- In scripts folder create two files
   1. install_nginx.sh
   ```bash
   #!/bin/bash

   sudo apt-get update
   sudo apt-get install -y nginx
   ```
   2. start_nginx.sh
   ```bash
   #!/bin/bash

  sudo service nginx start
   ```
- Push this file on CodeCommit

- After Deployment group is create then Create deployment.

![CodeDeploy output ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/9a147120-e7d0-4251-bab0-664997f90511)

# Step 7: Create Pipeline

- Step 1: Choose pipeline setting -> PipelineName > Service role
- Step 2: Add source stage -> CodeCommit > repoName > branchName > Select CodePipeline periodically for changes(For autometion)
- Step 3: Add build stage -> BuildProvider > Region > ProjectName > Single build 
- Step 4: Add deploy stage -> DeployProvider > Region > AppName > Deployment group
- Step 5: Review

  # CodePipeline = Code->Commit->Build->Deploy
![pipeline output](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/78bab773-32f5-48a0-bea3-1b6f2711f635)

# Step 8
![OutPut SS](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/c6c91d9f-ee15-4070-a88e-0d79654d0afb)
