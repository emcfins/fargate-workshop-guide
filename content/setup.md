## Setup

### AWS Account

In order to complete this workshop you'll need an AWS Account and access to
create AWS Identity and Access Management (IAM), Amazon Elastic Container
Service (ECS), Amazon Elastic Container Registry (ECR), Amazon DynamoDB, and AWS
Cloud9 resources within that account.

The code and instructions in this workshop assume only one participant is using
a given AWS account at a time. If you try sharing an account with another
participant, you will encounter naming conflicts for certain resources. You can
work around this by either using a suffix in your resource names or using
distinct Regions, but the instructions do not provide details on the changes
required to make this work.

Use a personal account or create a new AWS account for this workshop
rather than using an organization's account to ensure you have full access to
the necessary services and to ensure you do not leave behind any vestigial
resources from the workshop.

### Region

Use **US East (N. Virginia)** for this workshop. It supports the complete set of
covered in the material including AWS Fargate, Amazon ECS, Amazon ECR, Amazon
DynamoDB, and AWS Cloud 9. Consult the [Region Table][region-table] to
determine which services are available in a Region.

### AWS Cloud9 IDE

AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets
you write, run, and debug your code with just a browser. It includes a code
editor, debugger, and terminal. Cloud9 comes pre-packaged with essential tools
for popular programming languages and the AWS Command Line Interface (CLI)
pre-installed so you don’t need to install files or configure your laptop for
this workshop. Your Cloud9 environment will have access to the same AWS
resources as the user with which you logged into the AWS Management Console.

Take a moment now and setup your Cloud9 development environment.

**✅ Step-by-step Instructions**

1. Go to the AWS Management Console, click **Services** then select **Cloud9**
   under Developer Tools.

1. Click **Create environment**.

1. Enter `Development` into **Name** and optionally provide a **Description**.

1. Click **Next step**.

1. You may leave **Environment settings** at their defaults of launching a new
t2.micro EC2 instance which will be stopped after 30 minutes of inactivity. 

1. Click **Next step**.

1. Review the environment settings and click **Create environment**. Your
   environment will take several minutes to be created.

1. Once created, your IDE will open to a welcome screen. Below that, you should
   see a terminal prompt similar to:

    ![](images/setup-cloud9-terminal.png)

    You can run AWS CLI commands in here just like you would on your local computer.
    Verify that your user is logged in by running `aws sts get-caller-identity`.

    ```console
    Admin:~/environment $ aws sts get-caller-identity
    ```
    ```json
    {
        "Account": "123456789012",
        "UserId": "AKIAI44QH8DHBEXAMPLE",
        "Arn": "arn:aws:iam::123456789012:user/Alice"
    }
    ```

Keep your AWS Cloud9 IDE opened in a tab throughout this workshop as we'll use
it for activities like building and running a sample app in a Docker container
and using the AWS Command Line Interface.

### 💡 Tips

💡 Keep an open scratch pad in Cloud9 or a text editor on your local computer
for notes.  When the step-by-step directions tell you to note something, copy and
paste that into the scratch pad.

### ⭐ Recap

🔑 Use a unique personal or development [AWS Account](#aws-account)

🔑 Use the **US East (N. Virginia)** [Region](#region)

🔑 Keep your [AWS Cloud9 IDE](#aws-cloud9-ide) opened in a tab

### Next

✅  Proceed to the first module, [Getting Started with Amazon ECS using AWS
Fargate][getting-started], where we'll create a new Amazon ECS cluster, a task
definition, and a service for a Hello World application using the Amazon ECS
first run wizard.

[region-table]: https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/
[getting-started]: getting-started-with-amazon-ecs-using-aws-fargate.html
