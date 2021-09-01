---
title: "Criar pipeline de compilação de contêiner"
date: 2020-04-10T11:14:51-06:00
weight: 10
---

Começaremos criando um pipeline para a construção de imagens de contêiner multi-arquitetura. A criação de imagens para arquiteturas x86-64 e arm64 nos permitirá explorar o suporte nativo de várias arquiteturas nos serviços da AWS e escolher livremente entre a opção de computação mais ideal para a carga de trabalho.

A pilha de CDK que você implantará criará um repositório de código-fonte para o aplicativo NodeJS e duas etapas de compilação para criar imagens de contêiner para arquiteturas x86-64 e arm64.

1. Comece revisando o conteúdo da definição de pilha CDK no arquivo graviton2-workshop/graviton2/cs_graviton/pipeline_graviton2.py para ambas as arquiteturas.

{{% notice note %}}
A fim de construir imagens multi-arquitetura não estamos usando nenhuma camada de emulação. Em vez disso, usamos instâncias de compilação -64 e arm64 para garantir que criamos nossos contêineres em arquiteturas nativas.
{{% /notice %}}

construção de contêiner x86-64 : 
```
        docker_build_x86 = codebuild.PipelineProject(
            scope=self,
            id=f"DockerBuild_x86",
            environment=dict(
                build_image=codebuild.LinuxBuildImage.AMAZON_LINUX_2_3,
                privileged=True),
            environment_variables={
                'REPO_ECR': codebuild.BuildEnvironmentVariable(
                    value=container_repository.repository_uri),
            },
            build_spec=buildspec_x86
        )
```
construção de contêiner arm64 : 
```
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
```

Após a compilação bem-sucedida, as imagens dos contêineirs serão enviadas para o Elastic Container Registy (ECR) dedicado com tags seguinte <COMMIT_HASH>: <ARCH> por padrão. Neste ponto, você pode extrair essas imagens referindo-se às tags específicas da arquitetura.

{{% notice tip %}}Vamos simplificar esse processo criando uma lista de manifestos multi-arch do docker e enviando-a para o Amazon ECR.
{{% /notice %}}

A compilação do contêiner de ponta a ponta corresponderá ao diagrama abaixo.

![Pipeline](/images/Graviton-ID-diagrams-BuildPipeline.png)

2. Iniciar a implantação do pipeline emitindo comando: 
```bash
cdk deploy GravitonID-pipeline
```
3. Uma vez que o pipeline de compilação multi-arquitetura é implantado, você pode prosseguir para a próxima etapa em que criamos contêineres para a versão x86-64 e arm64 do aplicativo NodeJS.



