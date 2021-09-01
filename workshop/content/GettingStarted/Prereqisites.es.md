---
title: "Prerrequisitos"
date: 2020-04-10T11:14:49-06:00
weight: 30
---

1. En tu ambiente de Cloud9 clona el siguiente repositorio de Git:

```
git clone https://github.com/aws-samples/graviton2-workshop
cd graviton2-workshop
```

2. Actualiza la CLI de AWS de acuerdo a la guía [en la documentación](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html).

```bash
sudo pip install --upgrade awscli && hash -r
```

3. Instala jq, envsubst (de las utilerías de GNU gettext), tambíen instala bash-completion.

```bash
sudo yum -y install jq gettext bash-completion moreutils
```

4. Instala el cliente de mysql y sus librerias.

```bash
sudo yum -y localinstall https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
sudo yum -y install mysql-community-client mysql-community-libs
```

5.  Instala las herramientas necesarias de Kubernetes para interactuar con el cluster de EKS.

```bash
sudo curl --silent --location -o /usr/local/bin/kubectl \
   https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.11/2020-09-18/bin/linux/amd64/kubectl

sudo chmod +x /usr/local/bin/kubectl

kubectl completion bash >>  ~/.bash_completion
. /etc/profile.d/bash_completion.sh
. ~/.bash_completion
```

6. Configura tu cuenta y la región en las variables de ambiente.

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

Ejecuta este comando para generar tus llaves SSH en el ambiente de Cloud9. Utilizaremos estas llaves en los worker nodes y nos permitiran tener acceso ssh a las instancias en caso de ser necesario.

```bash
ssh-keygen
```

{{% notice tip %}}
Presiona `enter` 3 veces para seleccionar las opciones predeterminadas.
{{% /notice %}}

Sube tu llave pública a tu instancia EC2:

```bash
aws ec2 import-key-pair --key-name "graviton2key" --public-key-material file://~/.ssh/id_rsa.pub
```

Si tienes algún error similar a este: `An error occurred (InvalidKey.Format) when calling the ImportKeyPair operation: Key is not in valid OpenSSH public key format` utiliza este comando para subir tus llaves:

```bash
aws ec2 import-key-pair --key-name "graviton2key" --public-key-material fileb://~/.ssh/id_rsa.pub
```

8. Prepara tu ambiente para usar el servicio AWS CDK:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install --upgrade aws-cdk.core==1.99.0
pip install -r requirements.txt
cdk bootstrap aws://$ACCOUNT_ID/$AWS_REGION 
cdk synth 
cdk ls 
```
