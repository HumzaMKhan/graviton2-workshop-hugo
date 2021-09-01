---
title: "Workspace"
date: 2020-04-10T11:14:49-06:00
weight: 20
---

This first step is focused on deploying all prerequisites for Graviton2 Labs. For the purpose of this lab we will use following AWS services and software components:
* [Cloud9 IDE](https://aws.amazon.com/cloud9/)
* [AWS CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) (Cloud Development Kit)
  
We will use the [AWS CDK](https://docs.aws.amazon.com/cdk/latest/guide/home.html) to deploy some prerequisites.  
Our guiding principle is that we'll use the CDK to deploy static infrastructure and prerequisites.

{{% notice warning %}}
The Cloud9 workspace should be built by an IAM user with Administrator privileges,
not the root account user. Please ensure you are logged in as an IAM user, not the root
account user.
{{% /notice %}}

{{% notice info %}}
A list of supported browsers for AWS Cloud9 is found [here]( https://docs.aws.amazon.com/cloud9/latest/user-guide/browsers.html).
{{% /notice %}}

{{% notice tip %}}
Ad blockers, javascript disablers, and tracking blockers should be disabled for
the cloud9 domain, or connecting to the workspace might be impacted.
Cloud9 requires third-party-cookies. You can whitelist the [specific domains]( https://docs.aws.amazon.com/cloud9/latest/user-guide/troubleshooting.html#troubleshooting-env-loading).
{{% /notice %}}

### Set up a Cloud9 IDE

1. In the AWS console, go to the Cloud9 service and select `Create environment`.  Call your new IDE `Graviton2IDE` and click `Next Step`.  
On the next screen, select "Other instance type" and choose `m5.xlarge` instance type. Click `Next step` again.  
On the final page, click `Create environment`.  Make sure that you leave the VPC settings at the default values.

Once the environment builds, you'll automatically redirect to the IDE.  Take a minute to explore the interface, and note that you can change 
the color scheme if you like (AWS Cloud9 menu -> Preferences -> Themes).

Next, let's update the Cloud9 environment to let you run the labs from the environment.


* Create a role for your Cloud9 environment by clicking on the following [link](https://console.aws.amazon.com/iam/home#/roles$new?step=review&commonUseCase=EC2%2BEC2&selectedUseCase=EC2&policies=arn:aws:iam::aws:policy%2FAdministratorAccess)
* Confirm that AWS service and EC2 are selected, then click Next to view permissions.
* Confirm that AdministratorAccess is checked, then click Next: Tags to assign tags.
* Leave the defaults, and click Next: Review to review.
* Enter `Cloud9-Admin-Role` for the Name, and click Create role. 

* Once this new profile is created, go to EC2 and find the Cloud9 instance, and assign the instance profile to this instance.
* Go to Cloud9 Preferences and under AWS Credentials disable `AWS managed temporary credentials`.  


2. Move to the "Prerequisites" step



