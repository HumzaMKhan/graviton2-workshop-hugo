---
title: "Getting started"
date: 2020-04-10T11:14:49-06:00
weight: 10
---

Welcome to the Graviton2 Workshop!

The intent of this workshop is to educate users about the use of Graviton2 with AWS services and architectures.
Background in specific technology areas such as Containers, Databases, DevOps, and Big Data is not required, but it is recommended. This workshop is recommended for Cloud Architects, SREs, DevOps engineers, and other IT Professionals.

This workshop is intended to run in AWS US East (N. Virginia, Ohio), US West (Oregon), and Europe (Ireland) regions.

{{% notice info %}}
Unless you  are at an AWS hosted event, you will be charged for the AWS services that you are using in the workshop. For exploring AWS products take a look at the AWS Free Tier.
{{% /notice %}}

Scenarios covered in this workshop include:

* EKS cluster with multi-architecture (x86-64 and arm64) nodegroups
* ECS cluster with sample task and service running on Graviton2 instance type
* CI pipeline for multi-architecture docker container images using CodePipeline, CodeBuild, CodeCommit, and ECR (with docker manifests)
* CI pipeline for running .NET Core 5 on Amazon EKS cluster with Graviton2 worker nodes
* RDS migration scenario from MySQL 8 on m5 instance type to MySQL on m6g instance type
* RDS migration scenario from MySQL 5 on m5 instance type ->  in-place major version upgrade  MySQL 8 ->  to in-place instance change to m6g instance type
* EMR cluster with sample ETL Spark job running on Graviton2 instance type

{{% notice info %}}
Executing all scenarios can take up to 4 hours.
{{% /notice %}}
