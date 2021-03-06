---
title: "Prerequisites"
date: 2020-04-10T11:14:49-06:00
weight: 30
---

1. In your Cloud 9 environment, clone the Git repo:
```
git clone https://github.com/aws-samples/graviton2-workshop 
cd graviton2-workshop
```

2. Upgrade AWS CLI according to guidance in [AWS documentation](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html).

```bash
sudo pip install --upgrade awscli && hash -r
```

3.  Install jq, envsubst (from GNU gettext utilities) and bash-completion

```bash
sudo yum -y install jq gettext bash-completion moreutils
```

4. Install mysql client and libraries

```bash
sudo yum -y localinstall https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
sudo yum -y install mysql-community-client mysql-community-libs
```


5.  Install the required Kubernetes tools for interacting with the EKS cluster.


```bash
sudo curl --silent --location -o /usr/local/bin/kubectl \
   https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.11/2020-09-18/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl

kubectl completion bash >>  ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

6. Set account and region environmental variables

```bash
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure set default.account ${ACCOUNT_ID}
aws configure get default.region
aws configure get default.account

aws iam get-role --role-name "AWSServiceRoleForElasticLoadBalancing" || aws iam create-service-linked-role --aws-service-name "elasticloadbalancing.amazonaws.com"
```

7. SSH key 

Run this command to generate SSH Key in Cloud9. This key will be used on the worker node instances to allow ssh access if necessary.

```bash
ssh-keygen
```

{{% notice tip %}}
Press `enter` 3 times to take the default choices
{{% /notice %}}

Upload the public key to your EC2 region:

```bash
aws ec2 import-key-pair --key-name "graviton2key" --public-key-material file://~/.ssh/id_rsa.pub
```

If you got an error similar to `An error occurred (InvalidKey.Format) when calling the ImportKeyPair operation: Key is not in valid OpenSSH public key format` then you can try this command instead:

```bash
aws ec2 import-key-pair --key-name "graviton2key" --public-key-material fileb://~/.ssh/id_rsa.pub
```

8. Prepare AWS CDK 

```bash 
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade aws-cdk.core==1.99.0 
pip install -r requirements.txt
cdk bootstrap aws://$ACCOUNT_ID/$AWS_REGION 
cdk synth 
cdk ls 
```
