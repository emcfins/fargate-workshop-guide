## Create a Service

In this module we will create a new task definition and service to run the
sample application we built in the previous module. To support this service,
we'll need to create an AWS Identity and Access Management (AWS IAM) role to
allow our application to read from and write to the Amazon DynamoDB table it
uses to store quotations.  We'll also need to create an Application Load
Balancer to distribute traffic across the running tasks that are being ran by
the service.

This module provides instructions for creating the service in both the AWS
Management Console and the AWS Command Line Interface.

### Implementation

#### 1. Create the Task Role

Task roles are IAM roles that can be used by the containers in the task. For our
application, we need to grant permission to read from and write to the Amazon
DynamoDB table we created in the last module.

**✅ Step-by-step Instructions (AWS Management Console)**

1. Go to the AWS Management Console, click **Services** then select **IAM**
   under Security, Identity & Compliance.

1. Click on **Roles** in the left-hand navigation.

1. Click **Create role**.

1. First, we'll configure which AWS service can assume this role. Click
   **Elastic Container Service** from the **Choose the service that will use this
   role** list. Next, choose **Elastic Container Service Task** from **Select
   your use case**.

1. Click **Next: Permissions**.

1. Click **Create policy**. The visual policy editor will open in a new tab.

1. Click on **Choose a service** and click **DynamoDB**.

1. Expand the **Read** permissions and check the **Scan** and **GetItem**
   checkboxes.

1. Expand the **Write** permissions and check the **PutItem** checkbox.

1. Click **Resources** to limit the role to the **quotes** table.

1. Click **Add ARN** next to **table**.

1. Enter `us-east-1` in **Region**, your [Account ID][find-account-id] in
   **Account**, and `quotes` in **Table name**.

    ![](images/create-a-service/add-arn.png)

1. Click **Add**.

    ![](images/create-a-service/visual-editor.png)

    This will result in a policy allowing **dynamodb:PutItem**,
    **dynamodb:Scan**, and **dyanmodb:GetItem**.

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "dynamodb:PutItem",
                    "dynamodb:GetItem",
                    "dynamodb:Scan"
                ],
                "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/quotes"
            }
        ]
    }
    ```

1. Click **Review policy**.

1. Enter `WorkshopAppPolicy` in **Name**.

    ![](images/create-a-service/review-policy.png)

1. Click **Create policy**.

    ![](images/create-a-service/policy-confirmation.png)

1. Return to the original tab where you were creating the role. Click
   **Refresh** and type `WorkshopAppPolicy` in the **Filter** textbox. Check the
   **WorkshopAppPolicy* checkbox. Click **Next: Review**.

1. Enter `WorkshopAppRole` in **Role name**.

    ![](images/create-a-service/review-role.png)

1. Click **Create role**.

**✅ Step-by-step Instructions (AWS CLI)**

1. Switch to the tab where you have your Cloud9 environment opened.

1. Create a new IAM role called `WorkshopAppRole` by running this command in the
   Cloud9 terminal:

    ```console
    aws iam create-role --role-name WorkshopAppRole --assume-role-policy-document file://iam/WorkshopAppAssumeRolePolicy.json
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

    This creates a role and allows an ECS task to assume that role. Navigate to
    `fargate-workshop-app/iam/WorkshopAppAssumeRolePolicy.json` to see the
    assume role policy document we're assigning to the new role.

    The command will return information about the newly created role:

    ```console
    Admin:~/environment/fargate-workshop-app (master) $ aws iam create-role --role-name WorkshopAppRole --assume-role-policy-document file://iam/WorkshopAppAssumeRolePolicy.json
    ```
    ```json
    {
        "Role": {
            "AssumeRolePolicyDocument": {
                "Version": "2012-10-17",
                "Statement": [
                    {
                        "Action": "sts:AssumeRole",
                        "Effect": "Allow",
                        "Principal": {
                            "Service": "ecs-tasks.amazonaws.com"
                        }
                    }
                ]
            },
            "RoleId": "AROAJD4AD2SWV6AZAIVN4",
            "CreateDate": "2018-01-07T18:09:03.866Z",
            "RoleName": "WorkshopAppRole",
            "Path": "/",
            "Arn": "arn:aws:iam::123456789012:role/WorkshopAppRolet"
        }
    }
    ```


1. Open the file `fargate-workshop-app/iam/WorkshopAppPolicy.json` by navigating
   to it in the environment tree and double clicking the filename.

1. The file has the following contents:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "dynamodb:PutItem",
                    "dynamodb:GetItem",
                    "dynamodb:Scan"
                ],
                "Resource": "arn:aws:dynamodb:us-east-1:YOUR_ACCOUNT_ID_HERE:table/quotes"
            }
        ]
    }
    ```

    Replace the **YOUR_ACCOUNT_ID_HERE** placeholder with your [Account
    ID][find-account-id]. Save the file by going to **File** and selecting
    **Save** in the menu bar, or pressing **⌘-S** (macOS) / **Ctrl-S**
    (Windows).

1. Create the IAM policy by running this command in the Cloud9 terminal:

    ```console
    aws iam create-policy --role-name WorkshopAppPolicy --policy-document file://iam/WorkshopAppPolicy.json
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

    The command will return details about the newly created policy:

    ```console
    Admin:~/environment/fargate-workshop-app (master) $ aws iam create-policy --role-name WorkshopAppPolicy --policy-document file://iam/WorkshopAppPolicy.json
    ```
    ```json
    {
        "Policy": {
            "PolicyName": "WorkshopAppPolicy",
            "CreateDate": "2018-01-07T18:12:00.829Z",
            "AttachmentCount": 0,
            "IsAttachable": true,
            "PolicyId": "ANPAIITP5ALYCNFCXUTDA",
            "DefaultVersionId": "v1",
            "Path": "/",
            "Arn": "arn:aws:iam::123456789012:policy/WorkshopAppPolicy",
            "UpdateDate": "2018-01-07T18:12:00.829Z"
        }
    }
    ```

    Note the policy's **Arn** above for use in the next step.

3. Attach the policy to the role by running this command in the Cloud9 terminal.
   You'll need to change the **--policy-arn** attribute to the **Arn value you
   noted in the previous step. For example, for the output above, we'd run:

    ```console
    aws iam attach-role-policy --role-name WorkshopAppRole --policy-arn arn:aws:iam::123456789012:policy/WorkshopAppPolicy
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

#### 2. Create the Task Definition

Task definitions are blueprints for your application. They include details about
what containers to run, their resource requirements, environment settings,
networking configuration, and task role permission settings. In this step, we'll
create a task definition for our application. Complete either the directions
using the AWS Management Console or the AWS Command Line Interface.

**✅ Step-by-step Instructions (AWS Management Console)**

1. Go to the AWS Management Console, click **Services** then select **Elastic
   Container Service** under Compute.

1. Click **Task Definitions** in the left-hand navigation.

1. Click **Create new Task Definition**.

1. Click **Fargate** to select the Fargate launch type.

    ![](images/create-a-service/launch-type.png)

1. Click **Next step**.

1. Enter `workshop` into **Task Definition Name**.

1. Select **WorkshopAppRole** from **Task Role**.

1. Select **0.5GB** from **Task memory (GB)**.

1. Select **0.25 vCPU** from **Task CPU (vCPU)**.

1. Click **Add container**.

1. Enter `workshop` into **Container name**.

1. In **Image**, paste the repository URI for the Docker image you built and
   pushed in the previous module. For example, if your Account ID was
   123456789012, then you'd enter:
   `123456789012.dkr.ecr.us-east-1.amazonaws.com/workshop`

1. Enter `8080` into **Container port** and select **tcp** from **Protocol** in
   **Port mappings**.

1. Click **Update**.

1. Click **Create**.

**✅ Step-by-step Instructions (AWS CLI)**

1. Switch to the tab where you have your Cloud9 environment opened.

1. Open the file `fargate-workshop-app/ecs/workshop.json` by navigating
   to it in the environment tree and double clicking the filename.

1. The file has the following contents:

    ```json
    {
      "family": "workshop",
      "requiresCompatibilities": ["FARGATE"],
      "cpu": "256",
      "memory": "512",
      "networkMode": "awsvpc",
      "taskRoleArn": "arn:aws:iam::YOUR_ACCOUNT_ID_HERE:role/WorkshopAppRole",
      "executionRoleArn": "arn:aws:iam::YOUR_ACCOUNT_ID_HERE:role/ecsTaskExecutionRole",
      "containerDefinitions": [
        {
          "name": "workshop",
          "image": "YOUR_ACCOUNT_ID_HERE.dkr.ecr.us-east-1.amazonaws.com/workshop",
          "essential": true,
          "logConfiguration": {
            "logDriver": "awslogs",
            "options": {
              "awslogs-group": "/ecs/workshop",
              "awslogs-region": "us-east-1",
              "awslogs-stream-prefix": "ecs"
            }
          },
          "portMappings": [
            {
              "protocol": "tcp",
              "containerPort": 8080
            }
          ]
        }
      ]
    }
    ```

1. Replace the **YOUR_ACCOUNT_ID_HERE** placeholders with your [Account
   ID][find-account-id]. Save the file by going to **File** and selecting
   **Save** in the menu bar, or pressing **⌘-S** (macOS) / **Ctrl-S** (Windows).

1. Create a new task definition from the JSON file by running this command in
   your Cloud9 terminal:

    ```console
    aws ecs register-task-definition --cli-input-json file://~/environment/fargate-workshop-app/ecs/workshop.json
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

1. Create the CloudWatch Logs log group `/ecs/workshop` configured in your new
   task definition by running this command in your Cloud9 terminal:

    ```console
    aws logs create-log-group --log-group-name /ecs/workshop
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

#### 3. Create an Application Load Balancer

**✅ Step-by-step Instructions (AWS Management Console)**

1. Go to the AWS Management Console, click **Services** then select **EC2**
   under Compute.

1. Click on **Load Balancers** in the left-hand navigation.

1. Click **Create Load Balancer**.

1. In **Application Load Balancer**, click **Create**.

    ![](images/create-a-service/application-load-balancer.png)

1. Enter `workshop` into **Name**.

1. Select the **VPC** created in the first module when you created the ECS
   cluster in the first module. If you need to find the VPC ID do one of the
   following:

    **AWS Management Console**

    1. Click on **Services**, right-click on **VPC** under Networking &
       Content Delivery and click **Open Link in New Tab**.

    1. Click on **Your VPCs** in the left-hand navigation.

    1. Click on each VPC, and click on its **Tags** tab. The VPC you're
       looking for has a tag with **Key** `aws:cloudformation:stack-name` and
       **Value** `EC2ContainerService-workshop`

    **AWS CLI**

    1. Run the following command in your Cloud9 terminal:

        ```console
        aws ec2 describe-vpcs --query Vpcs[0].VpcId --output text \
                              --filters Name=tag:aws:cloudformation:stack-name,Values=EC2ContainerService-workshop
        ```
        <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

1. Select all **Availability Zones** configured for the VPC by checking each
   checkbox.

1. Click **Next: Configure Security Settings**.

1. The wizard will warn you that you've not established a secure listener as
   we didn't define an HTTPS listener. Click **Next: Configure Security
   Groups**.

1. Check the security group the is prefixed with
   **EC2ContainerService-workshop**. This security group will permit HTTP
   traffic to ingress to the load balancer.

1. Click **Next: Configure Routing**.

1. Enter `workshop` into **Name**.

1. Enter `8080` into **Port**.

1. Select **ip** from **Target type**.

1. Click **Next: Register Targets**. We won't register anything as we'll rely on
   Amazon ECS to manage our Target Group for us.

1. Click **Next: Review**. Review the details you configured, then click
   **Create**.

**✅ Step-by-step Instructions (AWS Management Console)**

1. Switch to the tab where you have your Cloud9 environment opened.

1. Find the subnets created for your ECS cluster via the first run wizard by running
   this command in your Cloud9 terminal:

    ```console
    aws ec2 describe-subnets --query Subnets[].SubnetId --output text \
                             --filters Name=tag:aws:cloudformation:stack-name,Values=EC2ContainerService-workshop
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

    Note these subnet IDs for use later in this module.

1. Find the security group created for your sample application via the first
   run wizard by running this command in your Cloud9 terminal:

    ```console
    aws ec2 describe-security-groups --query SecurityGroups[].GroupId --output text \
                                     --filters Name=tag:aws:cloudformation:stack-name,Values=EC2ContainerService-workshop
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

    Note this security group ID for use later in this module.

1. Create an Application Load Balancer via the AWS CLI in your Cloud9 terminal.
   You'll need the security group and subnet IDs from the previous two steps:

    ```console
    aws elbv2 create-load-balancer --name workshop --subnets subnet-SUBNET_ID_1_HERE subnet-SUBNET_ID_2_HERE --security-groups sg-SECURITY_GROUP_ID_HERE
    ```
    <button class="btn btn-outline-primary copy">Copy to Clipboard</button>

    The command will return information about the new Application Load Balancer:

    ```json
    {
        "LoadBalancers": [
            {
                "IpAddressType": "ipv4",
                "VpcId": "vpc-abcd1234",
                "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-1:123456789012:loadbalancer/app/workshop1/ed108c3680cfe3df",
                "State": {
                    "Code": "provisioning"
                },
                "DNSName": "workshop1-1971146816.us-east-1.elb.amazonaws.com",
                "SecurityGroups": [
                    "sg-abcd1234"
                ],
                "LoadBalancerName": "workshop",
                "CreatedTime": "2018-01-08T04:09:37.770Z",
                "Scheme": "internet-facing",
                "Type": "application",
                "CanonicalHostedZoneId": "Z35SXDOTRQ7X7K",
                "AvailabilityZones": [
                    {
                        "SubnetId": "subnet-abcd1234",
                        "ZoneName": "us-east-1d"
                    },
                    {
                        "SubnetId": "subnet-1234abcd",
                        "ZoneName": "us-east-1c"
                    }
                ]
            }
        ]
    }
    ```

    Note the **LoadBalancerArn** for use later in this module.


#### 4. Create the Service


[find-account-id]: https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html
