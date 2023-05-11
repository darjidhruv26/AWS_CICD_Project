# AWS_CICD_Project

![AWS_DevOps_Arch](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/7f602955-af14-4f19-b781-fe0f5870a09f)


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
![CodeCommit1 repo ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/55919e95-7ea6-4b69-94f2-fda44af8ce25)

![CodeCommit Code ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/fe415a06-1e71-4560-8e1b-3c2a31809819)

![CodeCommit Branchs ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/c3443c4b-fb03-41d2-bf33-cd8d608e0293)

![CodeBuild Project ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/4c334c45-8177-41be-8199-5ce5c8a13177)

![s3 artifect files ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/fb2d4b72-0dd9-4308-a4fd-d70612299607)

![CodeBuild_history](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/785ef619-a181-4710-aea4-c20082c75831)

![EC2 ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/a1697fff-dd42-4db5-b516-936710fb0184)

![CodeDeploy output ss](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/9a147120-e7d0-4251-bab0-664997f90511)

![pipeline output](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/78bab773-32f5-48a0-bea3-1b6f2711f635)

![OutPut SS](https://github.com/darjidhruv26/AWS_CICD_Project/assets/90086813/c6c91d9f-ee15-4070-a88e-0d79654d0afb)
