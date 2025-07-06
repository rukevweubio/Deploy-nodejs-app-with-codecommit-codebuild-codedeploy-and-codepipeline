### Project overview
This project demonstrates how to build a simple Node.js application, containerize it using Docker, push the image to Amazon Elastic Container Registry (ECR), and deploy it to Amazon Elastic Container Service (ECS) using AWS CodePipeline for continuous integration and continuous deployment (CI/CD). CodePipeline orchestrates the workflow: pulling code from AWS CodeCommit, building the Docker image with CodeBuild, pushing the image to ECR, and deploying to ECS using a Load Balancer for traffic distribution. This README provides step-by-step instructions to set up, build, and deploy the project.

### project data arche


### Project Overview
This project automates the deployment of a Node.js application to AWS ECS using AWS developer tools. The workflow includes:
- Committing code to AWS CodeCommit repository.
- Building a Docker image using AWS CodeBuild.
- Pushing the image to Amazon ECR.
- Deploying the updated image to ECS Fargate service.
- Using Application Load Balancer (ALB) for traffic routing and high availability.
- Automating the entire pipeline via AWS CodePipeline for CI/CD.

### Prerequisites
- AWS CLI: Installed and configured with appropriate IAM permissions.
- AWS Account: With ECS, ECR, CodePipeline, CodeBuild, CodeCommit enabled.
- Docker: Installed locally (optional, for local testing).
- webapp: Version 16+ installed locally for development.
- Git: Installed to clone and push code.
- IAM Roles: Properly configured for CodeBuild, CodePipeline, and ECS.

### Setup Instructions
Clone the Repository
```
git clone https://github.com/rukevweubio/Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline
cd Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline

```

## #Create AWS CodeCommit Repository
- Create a new CodeCommit repo via AWS Console or CLI.
- Push your Node.js app code to the CodeCommit repository.
- git remote add codecommit <CodeCommit-repo-URL>
- git push codecommit main

![create the codecommit repo](https://github.com/rukevweubio/Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline/blob/main/screenshoot/Screenshot%20(1355).png)
  
### Create Amazon ECR Repository
- aws ecr create-repository --repository-name nodejs-app --region us-east-1
- Build and Push Docker Image Manually (Optional)

```

$(aws ecr get-login --no-include-email --region us-east-1)
docker build -t nodejs-app .
docker tag nodejs-app:latest <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/nodejs-app:latest
docker push <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/nodejs-app:latest
```
![ ecr repo](https://github.com/rukevweubio/Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline/blob/main/screenshoot/Screenshot%20(1359).png)

### Create ECS Cluster and Service
- Create an ECS Cluster (Fargate).
- Create a task definition using ecs-task-def.json.
- Create an ECS Service linked with Application Load Balancer (ALB).

![ecs cluster](https://github.com/rukevweubio/Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline/blob/main/screenshoot/Screenshot%20(1361).png)

### Setup AWS CodeBuild Project
- Use buildspec.yml for build instructions:
- Build Docker image
- Push to ECR
- Attach the CodeBuild service role with ECR and ECS permissions.

  ![codebuild](https://github.com/rukevweubio/Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline/blob/main/screenshoot/Screenshot%20(1360).png)

### Create AWS CodePipeline
- Define stages:
- Source: AWS CodeCommit
- Build: AWS CodeBuild
- Deploy: AWS ECS (update service with new image)
- Link pipeline to ECS Cluster and Service.
- Configure pipeline to trigger on new commits to CodeCommit.
- 
![codepipeline](https://github.com/rukevweubio/Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline/blob/main/screenshoot/Screenshot%20(1362).png)

Code Snippets
Dockerfile
```
FROM node:16-alpine
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```
buildspec.yml
```
version: 0.2
phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com
      - REPOSITORY_URI=<aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/nodejs-app
      - IMAGE_TAG=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-7)
  build:
    commands:
      - echo Build started on `date`
      - docker build -t $REPOSITORY_URI:$IMAGE_TAG .
  post_build:
    commands:
      - echo Build completed on `date`
      - docker push $REPOSITORY_URI:$IMAGE_TAG
      - printf '[{"name":"nodejs-app","imageUri":"%s"}]' $REPOSITORY_URI:$IMAGE_TAG > imagedefinitions.json
artifacts:
  files: imagedefinitions.json
```
## create asecurity group on th ec2 in ecs
 create or oprn the port for the web application  on the ec2 to allow traffic to flow in the port 
 
## final app deployment
![the nodejs app in ecs](https://github.com/rukevweubio/Deploy-nodejs-app-with-codecommit-codebuild-codedeploy-and-codepipeline/blob/main/screenshoot/Screenshot%20(1370).png)
