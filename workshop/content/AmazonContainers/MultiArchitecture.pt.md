---
title: "Compilação de aplicativos multi-arquitetura"
date: 2020-04-10T11:14:51-06:00
weight: 20
---


1. Antes de começarmos este laboratório, certifique-se de que você está trabalhando em sua pasta graviton2-workshop. Se você não estiver, por favor mude para a pasta executando o seguinte comando em seu shell bash:

```bash
cd ~/environment/graviton2-workshop/
```

2. Clonar um repositório CodeCommit vazio executando : 

```bash
git clone `aws cloudformation describe-stacks --stack-name GravitonID-pipeline --query "Stacks[0].Outputs[0].OutputValue" --output text`               
```

3. Copie os arquivos de especificação de compilação de código e o código-fonte do aplicativo NodeJS para o novo repositório git.

```bash
cp -r graviton2/cs_graviton/app/* graviton2-pipeline-lab/
```

4. Execute os comandos abaixo para confirmar e enviar alterações no repositório CodeCommit.

```bash
cd graviton2-pipeline-lab
git add .
git commit -m "First commit Node.js sample application."
git push
```

Isto irá disparar as seguentes ações:

* compilação de imagens docker para ambas as arquiteturas x86-64 e arm64 
* compilação de imagem docker com manifesto multi-arquitectura
* um push de todas as imagens docker para o repositório ECS

5. Voce pode monitorar as etapas do CodePipeline usando o Console da AWS abrindo este link em uma nova guia. https://console.aws.amazon.com/codesuite/codepipeline/pipelines/graviton2-pipeline-lab/view?region=us-east-1

6. Quando todas as etapas do CodePipeline forem bem-sucedidas, execute os seguintes comandos para definir variáveis de ambiente:

```bash 
export ECR_REPO_URI=$(aws ecr describe-repositories --repository-name graviton2-pipeline-lab  | jq -r '.repositories[0].repositoryUri')
export MULTI_IMAGE_TAG=$(aws ecr describe-images --repository-name graviton2-pipeline-lab --query 'sort_by(imageDetails,& imagePushedAt)[-1].imageTags[0]' | jq -r .)
export CONTAINER_URI=$ECR_REPO_URI:$MULTI_IMAGE_TAG
echo $ECR_REPO_URI
echo $MULTI_IMAGE_TAG
echo $CONTAINER_URI
aws ssm put-parameter --name "graviton_lab_container_uri" --value $CONTAINER_URI --type String --overwrite 

```
