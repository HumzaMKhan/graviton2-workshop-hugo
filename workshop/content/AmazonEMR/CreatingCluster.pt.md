---
title: "Criando o Cluster EMR com instâncias Graviton2"
weight: 10
---

1. Crie o bucket do S3 para os trabalhos do EMR.

```bash
cd ~/environment/graviton2-workshop
source scripts/create_emr_buckets.sh 
```

2. Execute o seguinte comando para criar o cluster EMR na sua VPC default.


```bash
aws emr create-default-roles
IF_NET=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/)
SUBNET_ID=$(curl --silent http://169.254.169.254/latest/meta-data/network/interfaces/macs/${IF_NET}/subnet-id)
export EMR_CLUSTER_ID=$(aws emr create-cluster --name graviton2-emr-cluster --use-default-roles --release-label emr-6.1.0 --applications  Name=Spark Name=Hadoop Name=Zeppelin --ec2-attributes SubnetIds=$SUBNET_ID,KeyName=graviton2key --instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m6g.2xlarge,BidPrice=OnDemandPrice InstanceGroupType=CORE,InstanceCount=2,InstanceType=m6g.4xlarge,BidPrice=OnDemandPrice | jq -r '.ClusterId'); echo "Your cluster ID is = $EMR_CLUSTER_ID" 
```

{{% notice tip %}} 
Este processo pode levar 15-20 minutos para ser concluído. É uma ótima oportunidade para fazer uma pequena pausa.
{{% /notice %}}


{{% notice info %}}

Observe que estamos usando instâncias spot para Graviton2 EMR Cluster. As instâncias spot do Amazon EC2 oferecem capacidade computacional sobressalente disponível na nuvem da AWS com grandes descontos em comparação com instâncias sob demanda. As instâncias spot permitem que você otimize seus custos na nuvem da AWS e dimensione a taxa de transferência do aplicativo em até 10X para o mesmo orçamento. 

As instâncias spot podem ser interrompidas com dois minutos de notificação quando o EC2 precisar da capacidade de volta. Você pode usar Instâncias spot para vários aplicativos flexíveis e tolerantes a falhas, como big data, cargas de trabalho em contêineres, computação de alto desempenho (HPC), servidores web sem estado, renderização, CI/CD e outras cargas de trabalho de teste e desenvolvimento. 

Para obter mais informações sobre o uso de instâncias spot com o EMR, visite a [Documentação](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-purchasing-options.html)

{{% /notice %}}

3. Autorize a conexão SSH da instância do Cloud9.

```bash
export C9_PRIVATEIP=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.privateIp')
export EMR_MASTER_SG=$(aws emr describe-cluster --cluster-id $EMR_CLUSTER_ID | jq -r '.Cluster | .Ec2InstanceAttributes | .EmrManagedMasterSecurityGroup')
export EMR_MASTER_IP=$(aws emr describe-cluster --cluster-id $EMR_CLUSTER_ID | jq -r '.Cluster | .MasterPublicDnsName')
aws ec2 authorize-security-group-ingress --group-id $EMR_MASTER_SG --protocol tcp --port 22 --cidr $C9_PRIVATEIP/32
```

4. Copiar tarefa do Spark ETL para o nó Mestre do EMR

```bash
scp scripts/etl-spark.py hadoop@$EMR_MASTER_IP:~/
```

5. Conecte-se ao nó mestre do EMR e execute o trabalho spark. 

```bash
ssh hadoop@$EMR_MASTER_IP "spark-submit etl-spark.py s3://'$emr_s3_name'/input/ s3://'$emr_s3_name'/output/spark"  
```
Quando o trabalho da spark é enviado através da spark-submit na linha de comando, ele mostra os logs no console.

6. Aguarde alguns minutos e navegue até o console de serviço do S3 para verificar a pasta “output/spark” no bucket do S3 para ver os resultados.
