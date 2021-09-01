---
title: "Pré-requisitos"
date: 2020-04-10T11:14:49-06:00
weight: 20
---

1. No seu ambiente Cloud 9, clone o repositório Git:
```
git clone https://github.com/aws-samples/graviton2-workshop 
cd graviton2-workshop
```

2. Atualize a CLI da AWS de acordo com as orientações em [Documentação da AWS] (https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html).

```bash
sudo pip install --upgrade awscli && hash -r
```

3.  Instale jq, envsubst (das utilidades GNU gettext) e bash-completion

```bash
sudo yum -y install jq gettext bash-completion moreutils
```

4. Instale o cliente e bibliotecas mysql

```bash
sudo yum -y localinstall https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
sudo yum -y install mysql-community-client mysql-community-libs
```


5.  Instale as ferramentas necessárias do Kubernetes para interagir com o cluster do EKS.


```bash
sudo curl --silent --location -o /usr/local/bin/kubectl \
   https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.11/2020-09-18/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl

kubectl completion bash >>  ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

6. Defina variáveis de ambiente de conta e região

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

7. Chave SSH

Execute este comando para gerar a chave SSH no Cloud9. Essa chave será usada nas instâncias do nó trabalhador para permitir o acesso ssh, se necessário.

```bash
ssh-keygen
```

{{% notice tip %}}
Pressione `enter` 3 vezes para tomar as escolhas padrão
{{% /notice %}}

Faça upload da chave pública para sua região do EC2:

```bash
aws ec2 import-key-pair --key-name "graviton2key" --public-key-material file://~/.ssh/id_rsa.pub
```

Se você recebeu um erro semelhante a `Ocorreu um erro (InvalidKey.Format) ao chamar a operação ImportKeyPair: A chave não está no formato de chave pública OpenSSH válido` então você pode tentar este comando :

```bash
aws ec2 import-key-pair --key-name "graviton2key" --public-key-material fileb://~/.ssh/id_rsa.pub
```

8. Prepare o AWS CDK 

```bash 
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade aws-cdk.core==1.99.0
pip install -r requirements.txt
cdk bootstrap aws://$ACCOUNT_ID/$AWS_REGION 
cdk synth 
cdk ls 
```
