---
title: "AmazonEKS: Implantar cluster EKS multi-arquitetura"
date: 2020-04-10T11:14:51-06:00
weight: 30
---

O Amazon Elastic Kubernetes Service (Amazon EKS) é um serviço gerenciado que você pode usar para executar o Kubernetes na AWS sem precisar instalar, operar e manter seu próprio plano ou nós de controle do Kubernetes. O Kubernetes é um sistema de código aberto para automatizar a implantação, o dimensionamento e o gerenciamento de aplicativos em contêiner.

1. Comece revisando o conteúdo da definição de pilha CDK no arquivo graviton2-workshop/graviton2/cs_graviton/eks_graviton2.py. Esta pilha criará um cluster EKS com dois nodegroups: x86-node-group e arm64-node-group.

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
Note que estamos usando instâncias spot para o grupo de nodes Graviton2. As instâncias spot do Amazon EC2 oferecem capacidade computacional sobressalente disponível na nuvem da AWS com grandes descontos, em comparação com instâncias sob demanda. As instâncias spot permitem que você otimize seus custos na nuvem da AWS e dimensione a taxa de transferência do aplicativo em até 10X para o mesmo orçamento.

As instâncias spot podem ser interrompidas com dois minutos de notificação quando o EC2 precisar da capacidade de volta. Você pode usar Instâncias spot para vários aplicativos flexíveis e tolerantes a falhas, como big data, cargas de trabalho em contêineres, computação de alto desempenho (HPC), servidores web sem estado, renderização, CI/CD e outras cargas de trabalho de teste e desenvolvimento.

Nesse cenário, o CDK usará a estratégia de alocação “Otimizada para capacidade” para provisionar Spot e garantir que os nodes spot sejam provisionados nos pools de capacidade spot ideais. Para obter mais informações sobre o uso de instâncias spot com o EKS, visite [Documentação de EKS](https://docs.aws.amazon.com/eks/latest/userguide/managed-node-groups.html)

2. Implantar cluster EKS com dois grupos de nós usando CDK

```
cd ~/environment/graviton2-workshop/
cdk deploy GravitonID-eks
```
{{% notice tip %}} 
Este processo pode levar 15-20 minutos para ser concluído. É uma ótima oportunidade para fazer uma pequena pausa.{{% /notice %}}

3. Uma vez que o cluster e os nodegroups estiverem disponíveis, execute os seguintes comandos para criar o arquivo de configuração kubectl.

```bash
`aws cloudformation describe-stacks --stack-name GravitonID-eks --query "Stacks[0].Outputs[1].OutputValue" --output text`
```

4. Teste o cluster e verifique seus nós.

```bash
kubectl get nodes --label-columns=kubernetes.io/arch
```
Você deve ver uma saída semelhante à abaixo com quatro nós no total (dois nós arm64 usando instâncias Graviton2 e dois amd64 em execução na arquitetura x86-64)
```
NAME                         STATUS   ROLES    AGE   VERSION              ARCH
ip-10-0-2-105.ec2.internal   Ready    <none>   10h   v1.18.9-eks-d1db3c   amd64
ip-10-0-2-193.ec2.internal   Ready    <none>   10h   v1.18.9-eks-d1db3c   arm64
ip-10-0-3-28.ec2.internal    Ready    <none>   10h   v1.18.9-eks-d1db3c   arm64
ip-10-0-3-58.ec2.internal    Ready    <none>   10h   v1.18.9-eks-d1db3c   amd64
```

Agora você tem um cluster do Amazon EKS multiarquitetura totalmente funcional que está pronto para uso.

5. Continue para o próximo passo.
