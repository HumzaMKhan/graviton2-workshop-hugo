---
title: "Creating EMR Cluster with Graviton2 instances"
weight: 10
---

1. Create S3 bucket for EMR jobs.

```bash
cd ~/environment/graviton2-workshop
source scripts/create_emr_buckets.sh 
```

2. Execute following command to create EMR cluster in your default VPC.


```bash
aws emr create-default-roles
IF_NET=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
SUBNET_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IF_NET}/subnet-id)
export EMR_CLUSTER_ID=$(aws emr create-cluster --name graviton2-emr-cluster --use-default-roles --release-label emr-6.1.0 --applications  Name=Spark Name=Hadoop Name=Zeppelin --ec2-attributes SubnetIds=$SUBNET_ID,KeyName=graviton2key --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m6g.2xlarge,BidPrice=OnDemandPrice InstanceGroupType=CORE,InstanceCount=2,InstanceType=m6g.4xlarge,BidPrice=OnDemandPrice | jq -r '.ClusterId'); echo "Your cluster ID is = $EMR_CLUSTER_ID" 
```

{{% notice tip %}} 
This process can take 15-20 minutes to complete. It's a great opportunity to take a short break.
{{% /notice %}}


{{% notice info %}}
Please note we are using Spot instances for Graviton2 EMR Cluster.
Amazon EC2 Spot Instances offer spare compute capacity available in the AWS cloud at steep discounts compared to On-Demand instances. Spot Instances enable you to optimize your costs on the AWS cloud and scale your application’s throughput up to 10X for the same budget.

Spot Instances can be interrupted with two minutes of notification when EC2 needs the capacity back. You can use Spot Instances for various fault-tolerant and flexible applications, such as big data, containerized workloads, high-performance computing (HPC), stateless web servers, rendering, CI/CD and other test & development workloads.

For more information about use of Spot instances with EMR please visit [EMR documentation](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-purchasing-options.html)
{{% /notice %}}


3. Authorize SSH connection from Cloud9 instance.

```bash
export C9_PRIVATEIP=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.privateIp')
export EMR_MASTER_SG=$(aws emr describe-cluster --cluster-id $EMR_CLUSTER_ID | jq -r '.Cluster | .Ec2InstanceAttributes | .EmrManagedMasterSecurityGroup')
export EMR_MASTER_IP=$(aws emr describe-cluster --cluster-id $EMR_CLUSTER_ID | jq -r '.Cluster | .MasterPublicDnsName')
aws ec2 authorize-security-group-ingress --group-id $EMR_MASTER_SG --protocol tcp --port 22 --cidr $C9_PRIVATEIP/32
```

4. Copy Spark ETL job to EMR Master node

```bash
scp scripts/etl-spark.py hadoop@$EMR_MASTER_IP:~/
```

5. Connect to EMR Master node and execute spark job 

```bash
ssh hadoop@$EMR_MASTER_IP "spark-submit etl-spark.py s3://'$emr_s3_name'/input/ s3://'$emr_s3_name'/output/spark"  
```
When Spark job submitted through spark-submit on the command line, it shows up logs on the console. 

6. Wait few minutes and navigate to S3 service console to check  “output/spark” folder in your S3 bucket to see the results.
