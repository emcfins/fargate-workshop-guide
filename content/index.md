## Welcome!

Welcome to the _Running Containers on AWS Fargate_ workshop!

This is a 200-level workshop designed to illustrate how to deploy containerized
applications to AWS without managing clusters or provisioning servers.

In this workshop, we're going deploy applications using [Amazon Elastic
Container Service (Amazon ECS)][ecs] to orchestrate our containers on top of
[AWS Fargate][fargate], [Amazon Elastic Container Registry (Amazon ECR)][ecr] to
store our Docker container images, and [AWS Cloud9][cloud9] to drive our
development and deployment. In the last module, we'll use [AWS
CodeCommit][codecommit], [AWS CodePipeline][codepipeline], and [AWS
CodeBuild][codebuild] to create a continuous deployment pipeline to automatically
deploy changes to our application to Amazon ECS.

### Modules

This workshop is divided into individual modules. Each module describes a
scenario of what to build within that module. The modules include step-by-step
directions to help you implement the architecture and verify your work. Some
modules may contain instructions for both the AWS Management Console and the AWS
Command Line Interface giving you the option to choose either to complete the
task.

The modules build on each other and are intended to be executed linearly.

| Module | Description |
| ---------------- | -------------------------------------------------------- |
| [Getting Started with Amazon ECS using AWS Fargate][getting-started] | Create a new Amazon ECS cluster using the AWS Management Console. At the end of this module, we'll have a new ECS cluster and supporting infrastructure such as a VPC and subnets and a small Hello World application running. |
| [Create a Docker Image Repository][create-docker-image-repository] | Create a new Docker registry repository for workshop images in Amazon ECR. |
| [Build and Push a Docker Image][build-push-image] | Fork a sample application from GitHub which uses an Amazon DynamoDB table to store notable quotations and build it as a Docker container image and push it to your new Docker image repository. |
| [Create a Service][create-a-service] | Configure a task definition, create a load balancer, and create a service to run our Docker container image. |
| [Build a Continuous Deployment Pipeline][build-a-continuous-deployment-pipeline] | Using AWS developer tools, create a pipeline to automatically deploy changes committed to a source repository to the ECS service. |

### Next

✅ Review and follow the directions in the [setup guide][setup], wherein we'll
configure our AWS Cloud9 IDE and discuss pre-requisites like Region selection
and AWS Account setup.

[ecs]: http://aws.amazon.com/ecs/
[ecr]: http://aws.amazon.com/ecr/
[fargate]: http://aws.amazon.com/fargate/
[cloud9]: http://aws.amazon.com/cloud9/
[codepipeline]: http://aws.amazon.com/codepipeline/
[codebuild]: http://aws.amazon.com/codebuild/
[codecommit]: http://aws.amazon.com/codecommit/
[setup]: setup.html
[getting-started]: getting-started-with-amazon-ecs-using-aws-fargate.html
[create-docker-image-repository]: create-a-docker-image-repository.html
[build-push-image]: build-and-push-a-docker-image.html
[create-a-service]: create-a-service.html
[build-a-continuous-deployment-pipeline]: build-a-continuous-deployment-pipeline.html
