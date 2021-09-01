---
title: "AmazonEKS: Executando o aplicativo ASP.NET Core no Graviton2"
date: 2020-04-10T11:14:51-06:00
weight: 45
---

Um dos grandes pontos fortes da plataforma .NET Core .NET 5 é uma melhoria significativa no desempenho de tempo de execução e que .NET está pronto para esforços de modernização escaláveis. Os clientes também podem optar por criar sua plataforma.NET Core com o processador AWS Graviton2 criado para fins específicos da AWS. A AWS é o único grande provedor de nuvem que permite a opção de arquitetura .NET 5 e arm64 hoje. As instâncias do Amazon EC2 que executam o AWS Graviton2 oferecem até 40% melhor desempenho de preço em relação às instâncias baseadas em x86-64 da geração atual comparáveis. O AWS Graviton2 ajuda os clientes que executam o .NET a perceber melhorias de desempenho arm64 com todas as distribuições suportadas pelo .NET 5 Linux (Linux Alpine, Debian e Ubuntu).

{{% notice tip %}} 
Certifique-se de concluir o AmazonEKS: laboratório “Implantar cluster EKS multi-arquitetura” antes de iniciar este.
{{% /notice %}}

Comece revisando a pilha CDK responsável pela implantação do pipeline de compilação .NET Core no arquivo graviton2-workshop/graviton2/cs_graviton/pipeline_netcore_graviton2.py.


        docker_build_arm64 = codebuild.PipelineProject(
            scope=self,
            id=f"DockerBuild_ARM64",
            environment=dict(
                build_image=codebuild.LinuxBuildImage.AMAZON_LINUX_2_ARM,
                privileged=True),
            environment_variables={
                'REPO_ECR': codebuild.BuildEnvironmentVariable(
                    value=container_repository.repository_uri),
            },
            build_spec=buildspec_arm64
        )

1. Implante um pipeline de compilação do .NET Core usando o CDK

```bash
cd ~/environment/graviton2-workshop/
cdk deploy GravitonID-pipeline-dotnet

```

2. Clonar o repositório git de amostras do .NET Core executando : 

```bash
git clone --depth 1 https://github.com/dotnet/dotnet-docker.git
```

3. Clonar .NET Core Code Commit git repositório executando : 

```bash 
git clone `aws cloudformation describe-stacks --stack-name GravitonID-pipeline-dotnet --query "Stacks[0].Outputs[0].OutputValue" --output text`
```

4. Copie os arquivos de especificação de compilação de código e o código-fonte do aplicativo .NET Core 5 para o novo repositório git.

```bash
cp -r dotnet-docker/samples/aspnetapp/*  graviton2-aspnet-lab/
cp dotnet-docker/samples/aspnetapp/Dockerfile.debian-arm64 graviton2-aspnet-lab/Dockerfile
cp graviton2/cs_graviton/arm64-dotnet-buildspec.yml graviton2-aspnet-lab/
```

5. Execute os comandos abaixo para confirmar e enviar alterações no repositório CodeCommit.

```bash
cd ~/environment/graviton2-workshop/graviton2-aspnet-lab/
git add .
git commit -m ".NET Core 5 ASP Sample First Commit"
git push
```
Isso irá disparar as seguintes ações :
* Compilação da imagem docker do aplicativo .NET Core 5 para arquitetura arm64 
* Push de imagens de encaixe de aplicativos .NET Core 5 para o repositório ECR

6. Monitore as etapas do CodePipeline usando o AWS Console abrindo esse link em uma nova guia. Lembre-se de escolher a região correta da AWS. https://console.aws.amazon.com/codesuite/codepipeline/pipelines/

7. Quando todas as etapas do CodePipeline forem bem-sucedidas, retorne ao ambiente Cloud9 e execute o seguinte comando para definir variáveis de ambiente para deployemnt.


```bash 
export NET_REPO_URI=$(aws ecr describe-repositories --repository-name graviton2-aspnet-lab  | jq -r '.repositories[0].repositoryUri')
export NET_IMAGE_TAG=$(aws ecr describe-images --repository-name graviton2-aspnet-lab --query 'sort_by(imageDetails,& imagePushedAt)[-1].imageTags[0]' | jq -r .)
export NET_CONTAINER_URI=$NET_REPO_URI:$NET_IMAGE_TAG
echo $NET_REPO_URI
echo $NET_IMAGE_TAG
echo $NET_CONTAINER_URI
aws ssm put-parameter --name "graviton_net_container_uri" --value $NET_CONTAINER_URI --type String --overwrite 

```

8. Implante o aplicativo .NET Core no cluster do EKS

```bash
cd ~/environment/graviton2-workshop/
cat graviton2/cs_graviton/Deployment-aspnet.yaml | sed "s#{{container_uri}}#$NET_CONTAINER_URI#" | kubectl apply -f -
```

9. Você pode verificar o status de sua implantação executando:

```bash
kubectl get svc,deployment,po -n aspnet
```

10. Acessar o aplicativo .NET Core via endpoint LoadBalancer

{{% notice tip %}} 
Levará vários minutos para que o ELB se torne saudável e comece a passar o tráfego para os pods.

{{% /notice %}}

```bash
NET_ELB=$(kubectl get service aspnet-service -n aspnet -o json | jq -r '.status.loadBalancer.ingress[].hostname')
echo $NET_ELB 
```
Copie e cole o nome dns LoadBalancer no seu navegador e veja o aplicativo em execução.

### Parabéns! Agora você implantou o aplicativo .NET Core 5 usando os tipos de instância do Elastic Kubernetes Service e Graviton2.
