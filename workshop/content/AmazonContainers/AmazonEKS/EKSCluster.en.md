---
title: "AmazonEKS : Deploy multi-architecture EKS cluster"
date: 2020-04-10T11:14:51-06:00
weight: 30
---

Amazon Elastic Kubernetes Service (Amazon EKS) is a managed service that you can use to run Kubernetes on AWS without needing to install, 
operate, and maintain your own Kubernetes control plane or nodes. 
Kubernetes is an open-source system for automating the deployment, scaling, and management of containerized applications.

1. Start by reviewing the contents of the CDK stack definition in file graviton2-workshop/graviton2/cs_graviton/eks_graviton2.py.
This stack will create an EKS cluster with two nodegroups: x86-node-group and arm64-node-group.

```
        self.cluster = eks.Cluster(self, "EKSGraviton2",
            version=eks.KubernetesVersion.V1_18,
            default_capacity=0,
            endpoint_access=eks.EndpointAccess.PUBLIC_AND_PRIVATE,
            vpc=vpc,
            security_group=eks_security_group

        )

        self.ng_x86 = self.cluster.add_nodegroup_capacity("x86-node-group",
            instance_types=[ec2.InstanceType("m5.large")],
            desired_size=2,
            min_size=1,
            max_size=3
        )
        
        self.ng_arm64 = self.cluster.add_nodegroup_capacity("arm64-node-group",
            instance_types=[ec2.InstanceType("m6g.large")],
            desired_size=2,
            min_size=1,
            max_size=3,
            capacity_type=eks.CapacityType.SPOT

        )
```
{{% notice info %}}
Please note we are using Spot instances for Graviton2 node group.
Amazon EC2 Spot Instances offer spare compute capacity available in the AWS cloud at steep discounts compared to On-Demand instances. Spot Instances enable you to optimize your costs on the AWS cloud and scale your application’s throughput up to 10X for the same budget.

Spot Instances can be interrupted with two minutes of notification when EC2 needs the capacity back. You can use Spot Instances for various fault-tolerant and flexible applications, such as big data, containerized workloads, high-performance computing (HPC), stateless web servers, rendering, CI/CD and other test & development workloads.

In this scenario CDK will use "Capacity Optimized" allocation strategy to provision Spot and to ensure that Spot nodes are provisioned in the optimal Spot capacity pools.
For more information about use of Spot instances with EKS please visit [EKS documentation](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html)
{{% /notice %}}



2. Deploy EKS cluster with two nodegroups using CDK

```
cd ~/environment/graviton2-workshop/
cdk deploy GravitonID-eks
```
{{% notice tip %}} 
This process can take 15-20 minutes to complete. It's a great opportunity to take a short break.
{{% /notice %}}

3. Once the cluster and the nodegroups are available execute following commands to create kubectl configuration file.

```bash
`aws cloudformation describe-stacks --stack-name GravitonID-eks --query "Stacks[0].Outputs[1].OutputValue" --output text`
```

4. Test the cluster and verify your nodes.

```bash
kubectl get nodes --label-columns=kubernetes.io/arch
```
You should see output similar to the one below with four nodes in total (two arm64 nodes using Graviton2 instances, and two amd64 running on x86-64 architecture)
```
NAME                         STATUS   ROLES    AGE   VERSION              ARCH
ip-10-0-2-105.ec2.internal   Ready    <none>   10h   v1.18.9-eks-d1db3c   amd64
ip-10-0-2-193.ec2.internal   Ready    <none>   10h   v1.18.9-eks-d1db3c   arm64
ip-10-0-3-28.ec2.internal    Ready    <none>   10h   v1.18.9-eks-d1db3c   arm64
ip-10-0-3-58.ec2.internal    Ready    <none>   10h   v1.18.9-eks-d1db3c   amd64
```

You now have a fully working multi-architecture Amazon EKS Cluster that is ready to use.

5. Continue to the next step
