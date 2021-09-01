---
title: "AmazonECS: implantar o cluster do Graviton2 ECS"
date: 2020-04-10T11:14:51-06:00
weight: 80
---

![ECS Logo](/images/ecs.png)

O Amazon Elastic Container Service (Amazon ECS) é um serviço adminstrado de orquestração de contêineres altamente escalável e rápido que facilita a execução e o gerenciamento de contêineres em um cluster. Seus contêineres são definidos em uma definição de tarefa que você usa para executar tarefas individuais ou tarefas dentro de um serviço. O Amazon ECS permite que você inicie e interrompa seus aplicativos baseados em contêiner usando chamadas de API simples. Você também pode recuperar o estado do cluster de um serviço centralizado e ter acesso a muitos recursos familiares do Amazon EC2.


{{% notice tip %}}
Certifique-se de concluir o laboratório de compilação de aplicativos de arquitetura múltipla antes de iniciar este.
{{% /notice %}}


Comece analisando o conteúdo da definição de tarefa do AmazonECS no arquivo graviton2-workshop/graviton2/cs_graviton/ecs_graviton2.py

```bash

        ecs_ami = ecs.EcsOptimizedAmi(generation=ec2.AmazonLinuxGeneration.AMAZON_LINUX_2,
                                                             hardware_type=ecs.AmiHardwareType.ARM)
                                                                                
        asg_ecs = cluster.add_capacity("G2AutoScalingGroup",
                         instance_type=ec2.InstanceType("m6g.2xlarge"),
                         machine_image=ecs_ami
                         
        )

        container = task_definition.add_container(
            "web",
            image=ecs.ContainerImage.from_registry("{{container_uri}}"),
            memory_limit_mib=512,
            logging=ecs.LogDrivers.firelens(
                options={
                    "Name": "cloudwatch",
                    "log_key": "log",
                    "region": "us-east-1",
                    "delivery_stream": "my-stream",
                    "log_group_name": "firelens-fluent-bit",
                    "auto_create_group": "true",
                    "log_stream_prefix": "from-fluent-bit"}
            )    
        )
```

1. Implantar cluster ECS usando CDK

```
cdk deploy GravitonID-ecs
```

2. Uma vez que o cluster e a tarefa ECS estão disponíveis execute os seguintes comandos verificar o endpoint ELB para o serviço.

{{% notice note %}} 
Levará vários minutos para que o ELB se torne saudável(healthy) e comece a passar o tráfego para os pods.
{{% /notice %}}


```bash
ECS_ELB=$(aws cloudformation describe-stacks --stack-name GravitonID-ecs --query "Stacks[0].Outputs[0].OutputValue" --output text)
```

3. Teste o cluster e verifique seu serviço usando o tipo de instância Graviton2.

```bash
for i in {1..8}; do curl -m3 $ECS_ELB ; echo; done 

```

Agora você tem um cluster ECS totalmente funcional usando o tipo de instancia Graviton2 que está pronto para uso.
